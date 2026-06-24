# FAQ

## What is Mangarr?

Mangarr is a manga, manhwa, and manhua **library manager and downloader**. It tracks the titles you follow, automatically finds and downloads new chapters, organizes them into a tidy library as CBZ files, and can upgrade chapters when a better translation appears. It's built on the Sonarr foundation, adapted for manga.

## Is Mangarr a reader?

No. Mangarr **manages and downloads**; it doesn't display chapters. Pair it with a reader like **Komga** or **Kavita** (Mangarr can notify them to rescan — see **[Notifications](configuration/notifications.md)**).

## Do I have to run MangarrGateway too?

Yes. Mangarr does not scrape sites or download on its own — it drives **[MangarrGateway](gateway.md)**, which does the searching and downloading. You run both. See **[Connecting MangarrGateway](getting-started/gateway-setup.md)**.

## How is "quality" handled? There's no 1080p for manga.

Instead of video resolution, Mangarr uses a **[Translation Profile](configuration/profiles.md)** (which languages you accept and prefer) plus **[Custom Formats](configuration/custom-formats.md)** (preferred/avoided scanlation groups). Together they decide which release is "best".

## Where does Mangarr store its data?

- **Docker:** config + database in `/config`, library under `/data`.
- **Native:** `~/.config/Mangarr` (Linux/macOS) or `C:\ProgramData\Mangarr` (Windows).

The database is `mangarr.db` and config is `config.xml`.

## What file format are chapters saved as?

**CBZ** — a standard comic archive supported by virtually every manga reader.

## Can it auto-add titles from my MangaDex/AniList/MAL list?

Yes — use **[Import Lists](configuration/import-lists.md)**. Mangarr keeps your library in sync with a list you maintain on those services.

## Why is a chapter stuck as "missing"?

Common causes: it isn't **monitored**, no release matches your **[profile](configuration/profiles.md)**, the only release is **blocklisted**, or it isn't available yet. See **[Troubleshooting](troubleshooting.md)**.

## How do I update Mangarr?

- **Docker:** `docker compose pull && docker compose up -d`.
- **Native:** replace the binaries with the latest [release](https://github.com/devbrian/Mangarr/releases); Mangarr can notify you when one is available.

Your library and settings live in the config directory, so updates don't touch them.

## Is it safe to expose Mangarr to the internet?

No — run it on your LAN. For remote access, use a reverse proxy with HTTPS or a VPN/overlay network. Never port-forward `8989` directly.

## Where can I get help?

Join the **[Mangarr Discord](https://mangarr.github.io/discord)** or open an issue on **[GitHub](https://github.com/devbrian/Mangarr/issues)**.

## How do I contribute to this wiki?

Every page has an **edit icon** in the top-right that links to its source Markdown on GitHub. Open a pull request with your changes and they'll be reviewed.
