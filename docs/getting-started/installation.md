# Installation

Mangarr runs on Windows, macOS, and Linux. **Docker is the recommended way to run it** — it's the simplest to set up and keep updated, and it's the deployment the project targets first.

!!! info "You also need MangarrGateway"
    Mangarr relies on the companion **MangarrGateway** service to actually find and download chapters. Install Mangarr first using this page, then see **[Connecting MangarrGateway](gateway-setup.md)**.

## Default ports & paths

| Item | Value |
|------|-------|
| Web UI port | `8989` |
| Config + database (Docker) | `/config` |
| Library root (Docker) | `/data` |
| Config dir (Linux/macOS, native) | `~/.config/Mangarr` |
| Config dir (Windows, native) | `C:\ProgramData\Mangarr` |
| Database file | `mangarr.db` |
| Config file | `config.xml` |

## Docker

The image is published to the GitHub Container Registry:

```
ghcr.io/devbrian/mangarr:latest
```

Tags available: `latest`, a major tag (`1`), a minor tag (`1.0`), the full version (`1.0.0`), and the exact build (`1.0.0.<build>`). Pin to `1.0` or `1` for predictable updates, or use `latest` to always get the newest release.

### docker run

```bash
docker run -d \
  --name=mangarr \
  -e PUID=1000 \
  -e PGID=1000 \
  -e UMASK=002 \
  -e TZ=Etc/UTC \
  -p 8989:8989 \
  -v /path/to/config:/config \
  -v /path/to/manga:/data \
  --restart unless-stopped \
  ghcr.io/devbrian/mangarr:latest
```

### docker-compose

```yaml
services:
  mangarr:
    image: ghcr.io/devbrian/mangarr:latest
    container_name: mangarr
    environment:
      - PUID=1000
      - PGID=1000
      - UMASK=002
      - TZ=Etc/UTC
    volumes:
      - /path/to/config:/config
      - /path/to/manga:/data
    ports:
      - 8989:8989
    restart: unless-stopped
```

Start it with `docker compose up -d`.

### Environment variables

| Variable | Default | Purpose |
|----------|---------|---------|
| `PUID` | `1000` | User ID Mangarr runs as — set it to match the owner of your library files |
| `PGID` | `1000` | Group ID Mangarr runs as |
| `UMASK` | `002` | Permission mask applied to created files/folders |
| `TZ` | `Etc/UTC` | Time zone (e.g. `America/New_York`) so schedules and the calendar are correct |

!!! warning "Get PUID/PGID right"
    The most common Docker problem is permissions. `PUID`/`PGID` should match the user that owns the folder you mounted at `/data`. On Linux/macOS, run `id yourusername` to find them. A mismatch means Mangarr can't move or rename downloaded files.

### Volumes & the "single mount" rule

- `/config` — Mangarr's configuration, database, logs, and backups. Keep this on persistent storage.
- `/data` — your manga library root.

If MangarrGateway downloads chapters to a folder that Mangarr then imports, mount that **download folder and your library under a single common parent** (for example, both under `/data`). This lets Mangarr move/hardlink files instead of doing a slow full copy across separate mounts, and it avoids permission surprises. This is the same "single, common volume" convention used across the *arr ecosystem.

## Native install (Windows / macOS / Linux)

Per-platform archives are attached to every [GitHub Release](https://github.com/devbrian/Mangarr/releases). Download the build for your OS/architecture, extract it, and run the Mangarr executable. Mangarr will create its config directory automatically on first launch (see the paths table above) and open the UI on port 8989.

To use a different port:

```bash
Mangarr --port 9090
```

## After installing

1. Open the UI at **`http://<host>:8989`**.
2. Work through the **[First-Time Setup](first-time-setup.md)**.
3. Connect your downloader in **[Connecting MangarrGateway](gateway-setup.md)**.

## Updating

- **Docker:** pull the new image and recreate the container.
  ```bash
  docker compose pull && docker compose up -d
  ```
- **Native:** Mangarr can notify you when an update is available from the **System → Updates** area; replace the binaries with the new release archive.

Your database and settings live in `/config` (or the native config dir), so updates never touch your library.
