# General & Security

**Settings → General** holds host, security, proxy, logging, and update options. Below are the settings you're most likely to touch, plus backups and the API.

## Security

| Setting | Recommendation |
|---------|----------------|
| **Authentication** | *Forms (Login Page)* recommended; *Basic* also available. |
| **Authentication Required** | Keep enabled, including for local addresses, unless you have a specific reason. |
| **Username / Password** | Set strong credentials. |
| **API Key** | Auto-generated; used by integrations and the API. Regenerate if it's ever exposed. |
| **Certificate Validation** | Leave at the default unless you're connecting to services with self-signed certs. |

!!! danger "Don't expose Mangarr to the internet directly"
    Run it on your LAN. For remote access use a reverse proxy with HTTPS or a VPN/overlay network — never a raw port-forward of `8989`.

## Host

| Setting | Description |
|---------|-------------|
| **Port** | Web UI port (default `8989`). |
| **URL Base** | Sub-path to host Mangarr under, for reverse-proxy setups (e.g. `/mangarr`). |
| **Bind Address** | Which interface to listen on. |

Changing host settings requires a restart.

## Proxy

If Mangarr needs to reach the internet through a proxy (for metadata lookups, updates, etc.), configure it here — HTTP/SOCKS proxy with optional credentials and a bypass list.

## Logging

- **Log Level** — `Info` is the default. Switch to `Debug` or `Trace` when diagnosing a problem, then switch back (verbose levels are noisy and grow log files quickly).
- Logs are viewable under **System → Events / Log Files** and live in the config directory's `logs/` folder.

## Updates

- Mangarr can check for and notify you about new releases under **System → Updates**.
- **Docker users** should update by pulling a new image rather than using the in-app updater:
  ```bash
  docker compose pull && docker compose up -d
  ```

## Backups

Mangarr automatically backs up its database and settings on a schedule (configurable), and you can take a manual backup any time from **System → Backup**.

- Backups live in the config directory and can be downloaded from the UI.
- They contain `config.xml` (including your API key) and the database — treat them as sensitive.
- **Restore** a backup from the same screen if you ever need to roll back or migrate.

!!! tip "Before any big change"
    Take a manual backup before bulk edits, naming-scheme changes, or upgrades. Restoring is a one-click safety net.

## The API

Mangarr exposes a REST API (the same one the UI uses) for automation and integrations:

- **Base path:** `/api/v5`
- **Authentication:** send your API key as the `X-Api-Key` header, or `?apikey=…` as a query parameter.
- Find your key under **Settings → General → Security**.

Most users never need the API directly — but it's there for scripts, dashboards, and third-party tools.
