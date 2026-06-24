# MangarrGateway

!!! warning "Placeholder page"
    This page is a stub. Detailed MangarrGateway installation and configuration documentation is maintained by the Gateway project and will be filled in here. For now, this page explains how the Gateway relates to Mangarr and links to the connection steps.

**MangarrGateway** is the companion service that does the actual searching and downloading for Mangarr. Mangarr is the *manager* (library, decisions, scheduling); the Gateway is the *downloader* (it scrapes sources, fetches chapters, and returns finished CBZ files). Mangarr ships **no embedded browser and no built-in scrapers** — everything site-facing lives in the Gateway.

```
   ┌────────────┐        search / grab        ┌─────────────────┐     scrapes
   │  Mangarr   │  ───────────────────────▶   │  MangarrGateway │  ──────────▶  sources
   │  (manager) │  ◀───────────────────────   │   (downloader)  │  ◀──────────  (CBZ)
   └────────────┘     finished CBZ files       └─────────────────┘
```

## What it provides

- A searchable index across its configured **sources**.
- On-demand **downloading** of a chosen release, returning a finished CBZ.
- An **API** (with an API key) that Mangarr's Gateway **[indexer](configuration/indexers.md)** and **[download client](configuration/download-clients.md)** talk to.

## Connecting Mangarr to the Gateway

You need the Gateway's **base URL** and **API key**, then add two entries in Mangarr that both point at it:

1. A **Gateway Indexer** (for searching).
2. A **Gateway Download Client** (for grabbing/receiving files).

➡️ Full walkthrough: **[Connecting MangarrGateway](getting-started/gateway-setup.md)**.

## Installation & configuration

*(To be completed.)* This section will cover installing the Gateway (Docker and otherwise), configuring its sources and languages, obtaining its API key, and tuning its behaviour.

- **Default API port:** commonly `9191` (confirm with your Gateway's own configuration).
- **Sources & languages:** whatever you enable on the Gateway is what Mangarr can select on the indexer.

## Getting help

Until this page is complete, ask in the **[Mangarr Discord](https://mangarr.github.io/discord)** for Gateway setup help.
