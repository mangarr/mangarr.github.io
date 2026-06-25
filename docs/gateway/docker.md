# Gateway with Docker Compose

Docker is the recommended way to run MangarrGateway. There are three setups, each building on the previous one:

- **[Simple](#simple-gateway-only)** — just the gateway container. Runs **MangaDex, MangaFire, Atsumaru, Weeb Central, and ProjectSuki** with no extra setup.
- **[Full](#full-with-the-android-solver)** — adds the Android-WebView solver stack (**one shared device**) so the four Cloudflare-challenge sources (**Comix, Mangadot, MangaBall, Kagane**) also work. **This is the right choice for most deployments.**
- **[Dedicated solver lanes](#dedicated-solver-lanes-advanced)** — *advanced.* Gives a busy Cloudflare source its **own** Android device so it can't slow the others down. Optional scaling on top of Full.

All pull published images from the GitHub Container Registry — no source tree needed. For API-key, source, tuning, and troubleshooting details, see **[MangarrGateway](../gateway.md)**. For the per-source breakdown, see **[Sources](sources.md)**.

!!! warning "Image tags drop the leading `v`"
    The git tag is `v1.0.0`, but the **image** tag is `1.0.0` (no `v`). Pin with `MANGA_GATEWAY_VERSION=1.0.0`.

## Simple (gateway only)

Save as `docker-compose.yml`:

```yaml
services:
  gateway:
    image: ghcr.io/devbrian/mangarrgateway:${MANGA_GATEWAY_VERSION:-latest}
    container_name: mangarr-gateway
    ports:
      - "9191:9191"
    # Optional: copy .env.example → .env to set operator knobs. The image bakes
    # safe defaults, so the gateway also runs fine with no .env at all.
    env_file:
      - path: .env
        required: false
    volumes:
      - ${MANGARR_STATE_DIR:-gateway-state}:/state        # config.toml + API key + DBs + logs
      - ${MANGARR_DOWNLOADS_DIR:-gateway-output}:/data/manga  # finished CBZ output
    restart: unless-stopped

volumes:
  gateway-state:
  gateway-output:
```

Bring it up and read the auto-generated API key:

```bash
docker compose up -d
docker compose exec gateway cat /state/config.toml   # find the api_key line
```

!!! tip "Share the downloads volume with your library"
    Mount the gateway's `/data/manga` and Mangarr's library under a **single common parent** so Mangarr can move/hardlink imports instead of slow-copying. The container runs as **non-root uid 10001** — a bound host directory must be writable by that uid (`chown 10001:10001 <dir>`).

## Full (with the Android solver)

This adds two sidecars under an opt-in **`android`** compose profile:

- **`redroid`** — Android 11 in Docker; runs the real WebView that clears the strict Cloudflare challenge.
- **`android-solver`** — drives `redroid` and mints the clearance the gateway needs.

!!! warning "Linux host prerequisites (redroid will not boot without them)"
    The host kernel must load the **binder/ashmem** modules, and the host must expose a **GPU** at `/dev/dri`:

    ```bash
    sudo modprobe binder_linux ashmem_linux
    printf 'binder_linux\nashmem_linux\n' | sudo tee /etc/modules-load.d/redroid.conf
    ```

    `redroid` runs **privileged** and uses the host iGPU so Android can render real WebGL. This stack is Linux-only.

!!! info "Set a solver key in `.env`"
    The sidecar fails fast on an empty key. Create a `.env` next to the compose file:

    ```dotenv
    COMPOSE_PROFILES=android
    GATEWAY_ANDROID_SOLVER_API_KEY=choose-any-shared-secret
    ```

    `COMPOSE_PROFILES=android` turns the sidecars on; `GATEWAY_ANDROID_SOLVER_API_KEY` is shared between the gateway and the sidecar (any value).

Save as `docker-compose.yml`:

```yaml
services:
  gateway:
    image: ghcr.io/devbrian/mangarrgateway:${MANGA_GATEWAY_VERSION:-latest}
    container_name: mangarr-gateway
    ports:
      - "9191:9191"
    env_file:
      - path: .env
        required: false
    environment:
      GATEWAY_ANDROID_SOLVER_URL: "http://android-solver:8080"
      GATEWAY_ANDROID_SOLVER_API_KEY: "${GATEWAY_ANDROID_SOLVER_API_KEY:-}"
    depends_on:
      android-solver:
        condition: service_started
        required: false   # bare `up` (no android profile) still starts the gateway alone
    volumes:
      - ${MANGARR_STATE_DIR:-gateway-state}:/state
      - ${MANGARR_DOWNLOADS_DIR:-gateway-output}:/data/manga
    restart: unless-stopped

  # Android 11 in Docker — the WebView that clears the strict Turnstile.
  redroid:
    image: redroid/redroid:11.0.0-latest
    profiles: ["android"]   # only starts when COMPOSE_PROFILES=android
    privileged: true
    devices:
      - "/dev/dri:/dev/dri"   # host GPU passthrough (real WebGL)
    command:
      - androidboot.redroid_gpu_mode=host
    volumes:
      - ${MANGARR_REDROID_DIR:-redroid-data}:/data
    healthcheck:
      test:
        [
          "CMD-SHELL",
          "[ \"$$(getprop sys.boot_completed 2>/dev/null | tr -d '\\r')\" = \"1\" ]",
        ]
      interval: 15s
      timeout: 10s
      retries: 20
      start_period: 60s
    restart: unless-stopped

  # Drives redroid and serves the authenticated /solve control API.
  android-solver:
    image: ghcr.io/devbrian/mangarrgateway-android-solver:${MANGA_GATEWAY_VERSION:-latest}
    profiles: ["android"]
    depends_on:
      redroid:
        condition: service_healthy
    environment:
      SOLVER_API_KEY: "${GATEWAY_ANDROID_SOLVER_API_KEY:-}"   # MUST match the gateway's key
      SOLVER_ADB_TARGET: "redroid:5555"
      SOLVER_ALLOWED_HOSTS: "mangadot.net,kagane.to,mangaball.net,comix.to"
      SOLVER_SOLVE_TIMEOUT_S: "120"
    restart: unless-stopped

volumes:
  gateway-state:
  gateway-output:
  redroid-data:
```

Bring up the full stack (the `.env` above activates the `android` profile):

```bash
docker compose up -d
```

!!! note "adb is docker-internal only"
    The solver reaches `redroid` over the compose network; `adb` (full device control) is **never** published to the host. If you publish the solver's control API for LAN testing, add a `ports: ["18080:8080"]` block to `android-solver` — and only on a trusted network.

!!! tip "All four sources share one device here"
    In the Full stack above, the four Cloudflare sources take turns on the **single** `redroid` device — simple, and fine for most setups. If one source is heavily used (especially **Kagane**, which re-checks Cloudflare often and competes with its own downloads), it can occasionally make the others slow to respond. **[Dedicated lanes](#dedicated-solver-lanes-advanced)** below fix that.

## Dedicated solver lanes (advanced)

A **lane** is a dedicated Android device for one source. By default every Cloudflare source shares the single `redroid` device; under heavy use they compete for it, and a busy source can briefly starve another's Cloudflare clearance — which shows up as a source being intermittently **unavailable**. Giving the busiest sources their **own** device removes that contention.

This setup runs **five containers** — the gateway, one sidecar, and **three** Android devices: a shared one (for Mangadot + MangaBall) plus a dedicated lane each for **Kagane** and **Comix**.

```
   gateway ── android-solver ─┬─ redroid          (shared: Mangadot, MangaBall)
                              ├─ redroid-kagane   (Kagane only)
                              └─ redroid-comix    (Comix only)
```

!!! info "How it's wired"
    - One **`android-solver`** drives every device listed in `SOLVER_ADB_TARGETS`.
    - **`GATEWAY_ANDROID_LANES`** maps a lane name → its device; **`GATEWAY_ANDROID_SOURCE_LANE_MAP`** routes a source → a lane. Any source **not** mapped stays on the shared `default` device.
    - Each lane gets its **own `/data` volume**, so its Cloudflare cookie jar is never wiped by another source.
    - All devices **share the host GPU** (`/dev/dri`) and run side by side — but each is real extra load on the iGPU, so add a lane only for a source that needs it.

Start from the [Full](#full-with-the-android-solver) host prerequisites (binder/ashmem modules + `/dev/dri`), then use this `.env` — lanes are turned on and routed here:

```dotenv
# Solver + the two dedicated lanes (each lane-* profile adds one redroid device)
COMPOSE_PROFILES=android,lane-kagane,lane-comix
GATEWAY_ANDROID_SOLVER_API_KEY=choose-any-shared-secret

# lane name → device
GATEWAY_ANDROID_LANES={"default":"redroid:5555","kagane":"redroid-kagane:5555","comix":"redroid-comix:5555"}
# source → lane (anything not listed stays on "default")
GATEWAY_ANDROID_SOURCE_LANE_MAP={"kagane":"kagane","comix":"comix"}

# the one sidecar serves all three devices
SOLVER_ADB_TARGETS=redroid:5555,redroid-kagane:5555,redroid-comix:5555
```

Save as `docker-compose.yml`:

```yaml
# Shared healthcheck reused by all three redroid devices (YAML anchor; x- keys are ignored by Compose).
x-redroid-health: &redroid-health
  test:
    [
      "CMD-SHELL",
      "[ \"$$(getprop sys.boot_completed 2>/dev/null | tr -d '\\r')\" = \"1\" ]",
    ]
  interval: 15s
  timeout: 10s
  retries: 20
  start_period: 60s

services:
  gateway:
    image: ghcr.io/devbrian/mangarrgateway:${MANGA_GATEWAY_VERSION:-latest}
    container_name: mangarr-gateway
    ports:
      - "9191:9191"
    env_file:
      - path: .env
        required: false
    environment:
      GATEWAY_ANDROID_SOLVER_URL: "http://android-solver:8080"
      GATEWAY_ANDROID_SOLVER_API_KEY: "${GATEWAY_ANDROID_SOLVER_API_KEY:-}"
    depends_on:
      android-solver:
        condition: service_started
        required: false
    volumes:
      - ${MANGARR_STATE_DIR:-gateway-state}:/state
      - ${MANGARR_DOWNLOADS_DIR:-gateway-output}:/data/manga
    restart: unless-stopped

  # Shared device — every Cloudflare source NOT given its own lane (here: Mangadot, MangaBall).
  redroid:
    image: redroid/redroid:11.0.0-latest
    profiles: ["android"]
    privileged: true
    devices:
      - "/dev/dri:/dev/dri"
    command:
      - androidboot.redroid_gpu_mode=host
    volumes:
      - ${MANGARR_REDROID_DIR:-redroid-data}:/data
    healthcheck: *redroid-health
    restart: unless-stopped

  # Dedicated lane: Kagane only (started by COMPOSE_PROFILES=…,lane-kagane). Own /data volume.
  redroid-kagane:
    image: redroid/redroid:11.0.0-latest
    profiles: ["lane-kagane"]
    privileged: true
    devices:
      - "/dev/dri:/dev/dri"
    command:
      - androidboot.redroid_gpu_mode=host
    volumes:
      - ${MANGARR_REDROID_KAGANE_DIR:-redroid-kagane-data}:/data
    healthcheck: *redroid-health
    restart: unless-stopped

  # Dedicated lane: Comix only (started by COMPOSE_PROFILES=…,lane-comix). Own /data volume.
  redroid-comix:
    image: redroid/redroid:11.0.0-latest
    profiles: ["lane-comix"]
    privileged: true
    devices:
      - "/dev/dri:/dev/dri"
    command:
      - androidboot.redroid_gpu_mode=host
    volumes:
      - ${MANGARR_REDROID_COMIX_DIR:-redroid-comix-data}:/data
    healthcheck: *redroid-health
    restart: unless-stopped

  # One sidecar drives ALL devices in SOLVER_ADB_TARGETS (set in .env above).
  android-solver:
    image: ghcr.io/devbrian/mangarrgateway-android-solver:${MANGA_GATEWAY_VERSION:-latest}
    profiles: ["android"]
    env_file:
      - path: .env
        required: false
    depends_on:
      redroid:
        condition: service_healthy
      redroid-kagane:
        condition: service_healthy
        required: false   # only waited on when the lane-kagane profile started it
      redroid-comix:
        condition: service_healthy
        required: false
    environment:
      SOLVER_API_KEY: "${GATEWAY_ANDROID_SOLVER_API_KEY:-}"   # MUST match the gateway's key
      SOLVER_ADB_TARGET: "redroid:5555"
      SOLVER_ALLOWED_HOSTS: "mangadot.net,kagane.to,mangaball.net,comix.to"
      SOLVER_SOLVE_TIMEOUT_S: "120"
    restart: unless-stopped

volumes:
  gateway-state:
  gateway-output:
  redroid-data:
  redroid-kagane-data:
  redroid-comix-data:
```

Bring it up:

```bash
docker compose up -d
```

The two extra devices take about a minute to boot the first time. To confirm the routing, watch the gateway log on startup — you'll see clearance minted on each lane (`kagane`, `comix`) and on the shared device (`mangadot`).

!!! tip "Add or remove a lane"
    A lane is three small things: a `redroid-<name>` service (gated by a `lane-<name>` profile, with its own volume), the device added to `SOLVER_ADB_TARGETS`, and an entry in both `GATEWAY_ANDROID_LANES` and `GATEWAY_ANDROID_SOURCE_LANE_MAP`. Drop all three to remove it. Set `COMPOSE_PROFILES=android` (no `lane-*`) to fall straight back to the single shared device — no other change needed.

## Pinning a version & updating

```bash
# pin a release (no leading v):
MANGA_GATEWAY_VERSION=1.0.0 docker compose up -d

# update later:
docker compose pull && docker compose up -d
```

Your state and downloads live on the named volumes, so updates never touch them.

## Connecting to Mangarr

Once the gateway is up, add it to Mangarr (Indexer + Download Client) using its base URL `http://<host>:9191` and the API key from `config.toml` — see **[Connecting MangarrGateway](../getting-started/gateway-setup.md)**.
