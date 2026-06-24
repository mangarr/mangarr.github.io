# Connecting MangarrGateway

Mangarr does not scrape sites or download images itself. It delegates all of that to **[MangarrGateway](../gateway.md)**, a companion service that searches sources and returns finished CBZ files. You must run the Gateway and connect Mangarr to it before searches or downloads will work.

This involves **two** entries in Mangarr that both point at the same Gateway instance:

1. A **Gateway Indexer** — so Mangarr can *search* for chapters.
2. A **Gateway Download Client** — so Mangarr can *grab* chapters and receive the finished files.

!!! info "Install the Gateway first"
    Make sure a MangarrGateway instance is running and reachable from Mangarr, and have its **base URL** and **API key** handy. See the **[MangarrGateway](../gateway.md)** page for installing and configuring it.

## Step 1 — Add the Gateway Indexer

1. Go to **Settings → Indexers**.
2. Click **Add (+)** and choose **Mangarr Gateway**.
3. Fill in the fields:

| Field | Description |
|-------|-------------|
| **Gateway URL** | The base URL of your Gateway, e.g. `http://gateway:9191` (container name) or `http://192.0.2.10:9191` (IP). |
| **API Key** | The Gateway's API key. |
| **Sources** | Which of the Gateway's configured sources Mangarr may search. |
| **Languages** | Translation language(s) to request from the Gateway. |
| **Result Limit** *(advanced)* | How many results to pull per search page. Leave at the default unless you have a reason to change it. |

4. Click **Test**. A green check means Mangarr reached the Gateway successfully.
5. **Save**.

## Step 2 — Add the Gateway Download Client

1. Go to **Settings → Download Clients**.
2. Click **Add (+)** and choose **Mangarr Gateway**.
3. Fill in the fields:

| Field | Description |
|-------|-------------|
| **Host** | The Gateway host (e.g. `gateway` or `192.0.2.10`) — host only, no `http://`. |
| **Port** | The Gateway port (e.g. `9191`). |
| **Use SSL** | Enable only if your Gateway is served over HTTPS. |
| **URL Base** *(advanced)* | Leave as the default (`/api`) unless your Gateway is hosted under a sub-path. The effective endpoint is `http://[host]:[port]/[urlBase]`. |
| **API Key** | The Gateway's API key (same one as the indexer). |

4. Click **Test**, then **Save**.

## Step 3 — Verify end to end

1. Add a manga you know exists (see **[Adding Manga](../usage/adding-manga.md)**).
2. Open it and run an **[interactive search](../usage/searching.md)** — you should see results returned by the Gateway.
3. Grab one. Watch it progress in **Activity → [Queue](../usage/activity.md)**, then land in your library.

## Networking notes

- **Docker:** if Mangarr and the Gateway are on the same Docker network, use the Gateway's **container name** as the host (e.g. `http://gateway:9191`). Otherwise use the host machine's IP and the published port.
- **`localhost` inside a container refers to the container itself** — not your host. Use a container name or the host's LAN IP, never `localhost`/`127.0.0.1`, when Mangarr and the Gateway are in separate containers.
- Both the indexer and the download client must point at the **same** Gateway instance with the **same API key**.

## Troubleshooting the connection

- **Test fails / connection refused** — check the host and port, confirm the Gateway is running, and verify Docker networking (container name vs IP).
- **401 / unauthorized** — the API key is wrong or missing in one of the two entries.
- **Searches return nothing** — confirm the **Sources** and **Languages** selected on the indexer are actually enabled on the Gateway.

See **[Troubleshooting](../troubleshooting.md)** for more.
