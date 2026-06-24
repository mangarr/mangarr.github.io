# Troubleshooting & FAQ

Common problems running MangarrGateway and connecting Mangarr to it, plus quick answers. For the Mangarr-side connection steps see **[Connecting MangarrGateway](../getting-started/gateway-setup.md)**; for install see **[Docker Compose](docker.md)**.

## Troubleshooting

### The Indexer or Download Client "Test" fails

Almost always a networking or address problem:

- **Use the right host.** When Mangarr and the Gateway run as separate containers on the same Docker network, use the Gateway's **container name** (`http://gateway:9191`). Otherwise use the host's **LAN IP** and published port.
- **Never use `localhost`/`127.0.0.1`** from inside another container — it points at *that* container, not the Gateway.
- **Indexer** wants the full base URL (`http://<host>:9191`); the **Download Client** takes **Host** and **Port** separately, with **URL Base** left at its default `/api`.
- **Use SSL** should be **off** unless you put the Gateway behind an HTTPS reverse proxy.

### 401 / Unauthorized

The API key is wrong or missing. Re-copy it from the Gateway's config and paste it into **both** the Indexer and the Download Client (they must match):

```bash
docker compose exec gateway cat /state/config.toml   # the api_key line
```

See **[The API key](../gateway.md#the-api-key)**.

### A source is missing, disabled, or returns nothing

- It may be one of the four **solver sources** (Comix, Mangadot, MangaBall, Kagane). Those boot **disabled** unless the **`android` profile** is running and `GATEWAY_ANDROID_SOLVER_API_KEY` is set — see **[Full setup](docker.md#full-with-the-android-solver)**.
- It may be turned off via `GATEWAY_DISABLED_SOURCES` — see **[Configuring sources and languages](../gateway.md#configuring-sources-and-languages)**.
- The **language** you requested may not exist for that title. Widen the languages on the Indexer or try another source. Check what your instance actually has with `GET /api/v1/caps`.

### A Cloudflare source works only sometimes

Cloudflare is stricter on cloud/datacenter IPs than on residential ones. If a protected source clears intermittently on a server, route the Gateway's egress through a residential proxy (`GATEWAY_CLOUDFLARE_PROXY_*`) — see **[Tuning and advanced options](../gateway.md#tuning-and-advanced-options)**.

### redroid won't start / the solver stack won't come up

The solver stack is **Linux-only** and has host requirements:

- The host kernel must load the **`binder_linux`** and **`ashmem_linux`** modules.
- The host must expose a GPU at **`/dev/dri`**, and `redroid` runs **privileged**.

```bash
sudo modprobe binder_linux ashmem_linux
printf 'binder_linux\nashmem_linux\n' | sudo tee /etc/modules-load.d/redroid.conf
```

If you can't meet these, skip the stack — those four sources disable themselves and everything else keeps working.

### "Permission denied" on first start

If you bound `/state` (or `/data/manga`) to a **host directory**, it must be writable by the container's non-root user (**uid 10001**):

```bash
sudo chown -R 10001:10001 /your/state/dir /your/downloads/dir
```

Default **named volumes** don't have this problem.

### Downloaded chapters don't import into Mangarr

Mount the Gateway's `/data/manga` and Mangarr's library under a **single common parent** so Mangarr can move/hardlink instead of slow-copying across mounts, and make sure both sides can read/write the files (matching uid/gid). This is the same "single, common volume" convention used across the *arr ecosystem.

### Searches are slow or time out

Each source has a built-in, pre-tuned request rate, and some sites are simply slow or briefly degraded. A single sluggish source doesn't affect the others. If one source is consistently failing, disable it (`GATEWAY_DISABLED_SOURCES`) and report it.

### `docker pull` fails or the image isn't found

- The image tag has **no leading `v`** — use `ghcr.io/devbrian/mangarrgateway:1.0.0`, not `:v1.0.0`.
- The published images are **public**, so no `docker login` is needed to pull them.

### Where are the logs?

JSON-lines logs and the metrics snapshot live on the `/state` volume:

```bash
docker compose logs -f gateway                       # live container logs
docker compose exec gateway tail -f /state/logs/gateway.jsonl
```

Still stuck? See the site-wide **[Troubleshooting](../troubleshooting.md)** or ask in the **[Mangarr Discord](https://mangarr.github.io/discord)**.

## Frequently asked questions

### Do I need MangarrGateway?

Yes. Mangarr ships no scrapers and no browser — it delegates all searching and downloading to the Gateway. You run both.

### Do I have to run the Android solver stack?

No. It's optional. Five sources (**MangaDex, MangaFire, Atsumaru, Weeb Central, ProjectSuki**) work with just the gateway container. The stack only adds the four strict-Cloudflare sources.

### Can I run it on Windows or macOS?

The **gateway** container runs anywhere Docker does. The **solver stack** (redroid) needs a real **Linux host** with the kernel modules and a GPU above, so it generally won't run under Docker Desktop on Windows/macOS. Run the gateway-only setup there, or host the full stack on Linux.

### Where's my API key?

Auto-generated into `/state/config.toml` on first start — read it with `docker compose exec gateway cat /state/config.toml`. Setting `GATEWAY_API_KEY` as an env var has no effect.

### What port and base path does it use?

Port **`9191`**, API base path **`/api/v1`** (e.g. `http://<host>:9191/api/v1/caps`).

### What format are downloads?

**CBZ** by default (page images only). Mangarr adds metadata to the archive after it receives the file.

### Can I run more than one instance, or scale it out?

No — run a **single** instance as a **single process**. The shared site session, caches, and job queue all assume one process; never run it with multiple workers/replicas.

### Does it need a GPU?

Only the **solver stack** does (redroid needs `/dev/dri`). The gateway-only setup has no GPU requirement.

### How do I update?

```bash
docker compose pull && docker compose up -d
```

Your state and downloads live on volumes, so updates never touch them.

### How do I add or remove sources?

Sources are built into the Gateway — the list grows with releases. You can't add arbitrary sites yourself, but you can **disable** any with `GATEWAY_DISABLED_SOURCES=key1,key2`. See **[Sources](sources.md)**.

### Is it safe to expose to the internet?

The API key is required on **every** endpoint, but bind the Gateway to a trusted network or put it behind an authenticated reverse proxy. Never publish the solver's `adb`/control ports to an untrusted network.

### Why is the image tag `1.0.0` and not `v1.0.0`?

The git release tag is `v1.0.0`, but image tags follow Docker convention and drop the `v`. Use `MANGA_GATEWAY_VERSION=1.0.0`.
