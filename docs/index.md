# Mangarr Wiki

**Mangarr** is a manga, manhwa, and manhua library manager and downloader. It monitors your favourite titles for new chapters, automatically downloads and organizes them into a clean library, and can upgrade chapters when a better scan or preferred translation becomes available.

If you've used Sonarr or Radarr, Mangarr will feel instantly familiar — it's built on the same proven foundation, adapted for the manga domain.

!!! tip "New here? Start with installation"
    Head to **[Installation](getting-started/installation.md)**, then **[First-Time Setup](getting-started/first-time-setup.md)**, and finally **[Connecting MangarrGateway](getting-started/gateway-setup.md)**. Those three pages take you from zero to a working, automated library.

## What Mangarr does

- **Library management** — add manga/manhwa/manhua, organize them under root folders, and track every chapter's status at a glance.
- **Automatic monitoring & search** — Mangarr watches for new and missing chapters and grabs them for you.
- **Quality upgrades** — prefer a particular translation language or scanlation group, and Mangarr will replace chapters when a better match appears.
- **Organized files** — finished chapters are imported, renamed to your scheme, and filed as CBZ archives ready for your reader.
- **Import lists** — automatically pull titles from MangaDex, AniList, and MyAnimeList.
- **Notifications** — keep apps like Komga and Kavita in sync as new chapters land.

## How the pieces fit together

Mangarr itself does not scrape websites. All discovery and downloading is handled by a companion service, **MangarrGateway**, which Mangarr talks to over an API.

```
   ┌────────────┐        search / grab        ┌─────────────────┐     scrapes
   │  Mangarr   │  ───────────────────────▶   │  MangarrGateway │  ──────────▶  sources
   │  (manager) │  ◀───────────────────────   │   (downloader)  │  ◀──────────  (CBZ)
   └────────────┘     finished CBZ files       └─────────────────┘
        │
        ▼
   your library  (/data, organized into root folders)
```

You run **both** Mangarr and MangarrGateway. Mangarr is the brain (library, decisions, scheduling); the Gateway is the hands (fetching chapters and returning finished CBZ files).

## Documentation map

| Section | What's inside |
|---------|---------------|
| **[Getting Started](getting-started/installation.md)** | Install, first-run wizard, and connecting the Gateway |
| **[Using Mangarr](usage/adding-manga.md)** | Adding titles, monitoring, searching, activity, manual import |
| **[Configuration](configuration/media-management.md)** | Media management, profiles, custom formats, indexers, download clients, import lists, notifications, metadata, general |
| **[MangarrGateway](gateway.md)** | The companion download service |
| **[FAQ](faq.md)** & **[Troubleshooting](troubleshooting.md)** | Common questions and fixes |

## Community

Join us on **[Discord](https://mangarr.github.io/discord)** to ask questions, report issues, and chat with other users.
