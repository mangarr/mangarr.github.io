# First-Time Setup

Once Mangarr is running, open `http://<host>:8989` and work through the steps below. This gets the application secured and ready before you connect a downloader and start adding manga.

## 1. Set up authentication

On first launch Mangarr prompts you to configure authentication. You'll find these options later under **Settings → General → Security**.

- **Authentication method** — *Forms (Login Page)* is recommended. *Basic (Browser Popup)* is also available.
- **Username & password** — create your credentials.
- **Authentication Required** — choose whether authentication is also required for local addresses. Leave it enabled unless you have a specific reason not to.

!!! danger "Don't expose Mangarr directly to the internet"
    Mangarr is designed for your home network. If you need remote access, put it behind a reverse proxy with HTTPS, or use a VPN/overlay network. Never port-forward `8989` straight to the internet.

## 2. Note your API key

**Settings → General → Security → API Key** holds the key used by integrations and the API. It's generated automatically on first run. You'll need it if you connect external tools; you generally do **not** need it for normal UI use.

## 3. Add a root folder

A **root folder** is the top-level directory where a library of manga lives (for example `/data/manga`). Each manga you add gets its own subfolder beneath it.

1. Go to **Settings → Media Management**.
2. Under **Root Folders**, click **Add Root Folder**.
3. Choose the path inside the container/host (e.g. `/data/manga`).

You can add more than one root folder if you keep manga in multiple locations. See **[Media Management](../configuration/media-management.md)** for naming and file-handling options.

## 4. Create a Translation Profile

Where Sonarr uses video quality, Mangarr uses a **Translation Profile** to express which translation language(s) you prefer and in what order. Mangarr ships with sensible defaults; review them under **Settings → Profiles**.

A profile defines:

- The **languages you accept** (e.g. English).
- Their **priority order** — higher languages are preferred and trigger upgrades.
- An optional **upgrade cutoff** — once a chapter reaches this language, Mangarr stops looking for "better".

See **[Profiles](../configuration/profiles.md)** for the full breakdown, and **[Custom Formats](../configuration/custom-formats.md)** if you want to prefer or block specific scanlation groups.

## 5. Connect the Gateway

Mangarr can't search or download on its own — it drives **MangarrGateway**. This is the last required step before adding manga:

➡️ **[Connecting MangarrGateway](gateway-setup.md)**

## 6. (Optional) Notifications, import lists, and tuning

Once the core is working you can layer on:

- **[Notifications](../configuration/notifications.md)** — tell Komga/Kavita (and others) when chapters arrive.
- **[Import Lists](../configuration/import-lists.md)** — auto-add titles from MangaDex, AniList, or MyAnimeList.
- **[Metadata](../configuration/metadata.md)** — choose your metadata source and how covers/details are fetched.
- **[General](../configuration/general.md)** — backups, logging, proxy, and update settings.

## You're ready

With authentication set, a root folder added, a translation profile chosen, and the Gateway connected, you can move on to **[Adding Manga](../usage/adding-manga.md)**.
