# Troubleshooting

A checklist for the most common issues. If something here doesn't fix it, grab your logs (**System → Events / Log Files**, or the `logs/` folder in your config directory) and ask in the **[Discord](https://mangarr.github.io/discord)**.

## Searches return nothing

1. **Is the Gateway connected?** Test both the **[indexer](configuration/indexers.md)** and the **[download client](configuration/download-clients.md)** — both must pass.
2. **Sources/languages** selected on the indexer must be ones your **[Gateway](gateway.md)** actually has enabled.
3. **Is the chapter monitored?** Only monitored chapters are searched automatically. Check the title's detail page.
4. Try an **[interactive search](usage/searching.md)** — rejected results tell you *why* (language not in profile, etc.).

## Gateway test fails / connection refused

- Double-check **host** and **port**. In the **download client**, *Host* is host-only — no `http://`.
- Confirm the Gateway is actually running and reachable from Mangarr.
- **Docker networking:** if Mangarr and the Gateway are separate containers, use the Gateway's **container name** (same Docker network) or the host's **LAN IP** — never `localhost`/`127.0.0.1`, which point at Mangarr's own container.
- **401 / unauthorized** → the API key is wrong or missing in the indexer or download client.

## Downloads complete but never import

This is almost always **paths or permissions**:

- **Permissions (Docker):** `PUID`/`PGID` must match the owner of your `/data` library; `UMASK` should allow Mangarr to write. See **[Installation](getting-started/installation.md)**.
- **Single volume:** keep the Gateway's download location and your library under one common mount (e.g. both under `/data`) so Mangarr can move/hardlink. Across separate mounts it must copy, and a path mismatch can block import entirely.
- **Remote path mapping:** if the Gateway reports a path Mangarr can't see, add a **Remote Path Mapping** on the **[download client](configuration/download-clients.md)**.
- Check the **[Queue](usage/activity.md)** for a specific import warning, then use **[Manual Import](usage/manual-import.md)** to resolve it.

## A chapter won't download

- **Blocklist:** the only available release may be blocklisted — check **Activity → [Blocklist](usage/activity.md)** and remove it if needed.
- **Profile too strict:** no release matches your **[Translation Profile](configuration/profiles.md)** languages, or **[Custom Format](configuration/custom-formats.md)** scores reject everything. Confirm via interactive search.
- **It simply isn't available yet** in your preferred language — it'll stay under **[Wanted](usage/wanted.md)** until it appears.

## Permission errors on imported files

- Set `PUID`/`PGID`/`UMASK` correctly (Docker), or the file-permission settings under **[Media Management](configuration/media-management.md)** (native).
- Make sure the user Mangarr runs as owns the root folder and the download folder.

## Can't reach the web UI

- Confirm the container/process is running and port **8989** is published/free.
- If you set a **URL Base**, the UI is under that sub-path (e.g. `http://host:8989/mangarr`).
- Check the bind address and that nothing else is using the port.

## Metadata is wrong or missing

- **Refresh** the title (detail page) to re-pull from your **[Metadata Source](configuration/metadata.md)**.
- If the wrong title was matched, remove and re-add it, selecting the correct search result.

## Lost settings / want to roll back

- Restore a **[backup](configuration/general.md)** from **System → Backup**. Mangarr backs up automatically on a schedule, and you can take a manual one any time.

## Getting more detail

- Set **Log Level** to `Debug` (or `Trace`) under **[General](configuration/general.md)**, reproduce the issue, then review **System → Events / Log Files**. Switch back to `Info` afterwards.
