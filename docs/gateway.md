# MangarrGateway

**MangarrGateway** is the companion service that does the actual searching and downloading for Mangarr. Mangarr is the *manager* (library, decisions, scheduling); the Gateway is the *downloader* (it scrapes sources, fetches chapters, and returns finished CBZ files). Mangarr ships **no embedded browser and no built-in scrapers** — everything site-facing lives in the Gateway.

```
   ┌────────────┐        search / grab        ┌─────────────────┐     scrapes
   │  Mangarr   │  ───────────────────────▶   │  MangarrGateway │  ──────────▶  sources
   │  (manager) │  ◀───────────────────────   │   (downloader)  │  ◀──────────  (CBZ)
   └────────────┘     finished CBZ files       └─────────────────┘
```

## What it provides

- A searchable index across its configured **[sources](gateway/sources.md)**.
- On-demand **downloading** of a chosen release, returning a finished **CBZ** (page images only — Mangarr adds metadata after hand-off).
- An **API** (with an API key) that Mangarr's Gateway **[indexer](configuration/indexers.md)** and **[download client](configuration/download-clients.md)** talk to.

!!! info "API endpoint"
    The Gateway listens on port **`9191`** and serves its REST API under **`/api/v1`** — e.g. `http://<host>:9191/api/v1/caps`. In Mangarr, the Indexer's **Gateway URL** is the host:port base (`http://<host>:9191`); the Download Client's **URL Base** stays at its default `/api`. See **[Connecting MangarrGateway](getting-started/gateway-setup.md)** for the exact fields.

## Default ports & paths

| Item | Value |
|------|-------|
| API port | `9191` |
| API base path | `/api/v1` |
| App data (Docker) | `/state` — `config.toml` (incl. the auto-generated API key), databases, Cloudflare state, metrics, logs |
| Downloads (Docker) | `/data/manga` — finished CBZ output |
| Config file | `/state/config.toml` |
| Delivery format | `cbz` (default; `cbt` and `folder` also supported) |

## Install (Docker)

Docker is the recommended way to run the Gateway. Two images are published to the GitHub Container Registry:

```
ghcr.io/devbrian/mangarrgateway                 # the gateway API
ghcr.io/devbrian/mangarrgateway-android-solver  # the Cloudflare solver sidecar (optional — see below)
```

Tags: `latest`, the full version (`1.0.0`), and a minor tag (`1.0`).

!!! warning "Image tags drop the leading `v`"
    The git tag is `v1.0.0`, but the **image** tag is `1.0.0` (no `v`). Use `MANGA_GATEWAY_VERSION=1.0.0`, not `v1.0.0`.

!!! tip "Ready-made compose files"
    Full copy-paste `docker-compose.yml` files for **both** the simple and full setups are on the **[Docker Compose](gateway/docker.md)** page. The snippets below are a quick reference.

### Gateway only (simplest)

Most sources work with just the gateway container — no extra setup. This runs the API alone:

```yaml
services:
  gateway:
    image: ghcr.io/devbrian/mangarrgateway:latest
    container_name: mangarr-gateway
    ports:
      - "9191:9191"
    volumes:
      - gateway-state:/state        # config.toml + API key + databases + logs
      - gateway-output:/data/manga  # finished CBZ output
    restart: unless-stopped

volumes:
  gateway-state:
  gateway-output:
```

```bash
docker compose up -d
```

This serves **MangaDex, MangaFire, Atsumaru, Weeb Central, and ProjectSuki** out of the box. The other four sources need the solver stack below.

!!! tip "Share the downloads volume with your library"
    Mount the Gateway's `/data/manga` and Mangarr's library under a **single common parent** so Mangarr can move/hardlink imported chapters instead of slow-copying across mounts — the same "single, common volume" convention used across the *arr ecosystem. The container runs as **non-root uid 10001**; a bound host directory must be writable by that uid (`chown 10001:10001 <dir>`).

### Full stack (sources behind a strict challenge)

Four sources — **Comix, Mangadot, MangaBall, and Kagane** — sit behind a strict Cloudflare challenge that needs a real Android WebView to clear. The project provides a ready-made compose file that adds two sidecars (`redroid` + `android-solver`) under an opt-in **`android`** profile.

Download it from the Gateway repo and bring up the full stack:

```bash
curl -O https://raw.githubusercontent.com/devbrian/MangarrGateway/main/docker-compose.release.yml

# pin a release (recommended) and enable the android profile via .env, then:
COMPOSE_PROFILES=android docker compose -f docker-compose.release.yml up -d
```

!!! warning "Host prerequisites for the solver stack"
    `redroid` (Android-in-Docker) only boots on a Linux host with the **binder/ashmem kernel modules** loaded and the **iGPU** exposed:

    ```bash
    sudo modprobe binder_linux ashmem_linux
    printf 'binder_linux\nashmem_linux\n' | sudo tee /etc/modules-load.d/redroid.conf
    ```

    The host must also expose `/dev/dri` (a GPU). Set `GATEWAY_ANDROID_SOLVER_API_KEY` in `.env` (any value — it's shared between the gateway and the sidecar). If you don't run the solver stack, those four sources simply boot disabled and the rest keep working. Full host details live in the comments at the top of `docker-compose.release.yml`.

### Pin a version

```bash
MANGA_GATEWAY_VERSION=1.0.0 docker compose -f docker-compose.release.yml up -d
```

### Updating

```bash
docker compose pull && docker compose up -d
```

Your state and downloads live on the named volumes, so updates never touch them.

## The API key

The Gateway **auto-generates** its API key into `/state/config.toml` on first start. Read it after the container is up:

```bash
docker compose exec gateway cat /state/config.toml   # find the api_key line
```

Use that value as the **API Key** in *both* the Mangarr Indexer and Download Client.

!!! info "The key is auto-managed"
    Setting `GATEWAY_API_KEY` as an environment variable has **no effect** — the key always comes from `config.toml`. To rotate it, edit the value in `config.toml` and restart the container. The key is required on **every** endpoint.

## Configuring sources and languages

Every registered **[source](gateway/sources.md)** is enabled by default. The Gateway advertises each source's available **languages** to Mangarr; you then pick which sources and languages to use on the **[Indexer](configuration/indexers.md)** — the Gateway returns only what you select.

To disable specific sources on the Gateway itself, set a comma-separated list (these are dropped from the registry at startup and never advertised):

```dotenv
GATEWAY_DISABLED_SOURCES=kagane,mangadot
```

See the **[Sources](gateway/sources.md)** page for the full list with languages, rate limits, and which ones require the solver stack.

## Connecting Mangarr to the Gateway

You need the Gateway's **base URL** (`http://<host>:9191`) and its **API key**, then add two entries in Mangarr that both point at it:

1. A **Gateway Indexer** (for searching).
2. A **Gateway Download Client** (for grabbing/receiving files).

➡️ Full walkthrough: **[Connecting MangarrGateway](getting-started/gateway-setup.md)**.

## Tuning and advanced options

These are set as environment variables (or in `.env` next to the compose file). Most deployments need none of them — the defaults are sensible and per-source rate limits are pre-tuned.

| Variable | Purpose |
|----------|---------|
| `GATEWAY_DISABLED_SOURCES` | Comma-separated source keys to drop at startup. |
| `MANGARR_STATE_DIR` | Bind app data (`/state`) to a host directory instead of a named volume. |
| `MANGARR_DOWNLOADS_DIR` | Bind downloads (`/data/manga`) to a host directory. |
| `GATEWAY_ANDROID_SOLVER_API_KEY` | Shared key for the solver sidecar (required when the `android` profile is active). |
| `GATEWAY_CLOUDFLARE_PROXY_SERVER` / `_USERNAME` / `_PASSWORD` | Route the browser + image fetch through a single proxy — useful when a datacenter IP gets flagged by Cloudflare. |

!!! tip "Datacenter vs residential IP"
    Some Cloudflare-protected sources clear more reliably from a residential IP. If a source intermittently fails to clear on a cloud/datacenter host, routing egress through a residential proxy (the `GATEWAY_CLOUDFLARE_PROXY_*` variables above) is the usual fix.

## Troubleshooting

A quick-reference table is below; for detailed fixes and common questions see **[Troubleshooting & FAQ](gateway/troubleshooting.md)**.

| Symptom | Likely cause & fix |
|---------|--------------------|
| **Indexer/Download Client "Test" fails** | Wrong host/port, or a Docker networking issue. Use the Gateway's **container name** (`http://gateway:9191`) when both run on the same Docker network, or the host's LAN IP otherwise — never `localhost` from inside another container. |
| **401 / Unauthorized** | The API key is wrong or missing. Re-copy it from `config.toml` into both Mangarr entries. |
| **A specific source returns nothing / shows as disabled** | It's one of the four solver sources (Comix, Mangadot, MangaBall, Kagane) and the `android` profile isn't running, or `GATEWAY_ANDROID_SOLVER_API_KEY` isn't set. Bring up the full stack, or disable that source. |
| **Cloudflare sources flaky on a cloud host** | Expected on some datacenter IPs — add a residential proxy (`GATEWAY_CLOUDFLARE_PROXY_*`). |
| **First run fails to write config / permission denied** | A bound `/state` host dir isn't writable by uid `10001`. Run `chown 10001:10001 <dir>`. |
| **redroid won't boot** | The host is missing the `binder_linux`/`ashmem_linux` kernel modules or `/dev/dri`. See the host prerequisites above. |

Logs and the metrics snapshot live under `/state` (`logs/gateway.jsonl`, `metrics.db`).

## Getting help

Ask in the **[Mangarr Discord](https://mangarr.github.io/discord)** for Gateway setup help, or open an issue on the **[MangarrGateway repo](https://github.com/devbrian/MangarrGateway)**.
