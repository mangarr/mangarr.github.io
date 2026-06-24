# Indexers

An **indexer** is how Mangarr *searches* for chapters. In Mangarr there is one indexer: the **Mangarr Gateway**, which proxies searches to your **[MangarrGateway](../gateway.md)** instance.

Find this under **Settings → Indexers**.

!!! info "Mangarr ships no built-in scrapers"
    Unlike a Usenet/torrent setup, Mangarr does not scrape manga sites itself and bundles no embedded browser. All searching is delegated to MangarrGateway. If you haven't set the Gateway up yet, start with **[Connecting MangarrGateway](../getting-started/gateway-setup.md)**.

## Adding the Gateway indexer

1. **Settings → Indexers → Add (+) → Mangarr Gateway**.
2. Configure:

| Field | Description |
|-------|-------------|
| **Gateway URL** | Base URL of your Gateway, e.g. `http://gateway:9191`. |
| **API Key** | The Gateway's API key. |
| **Sources** | Which Gateway sources Mangarr may search. |
| **Languages** | Translation language(s) to request. |
| **Result Limit** *(advanced)* | Results pulled per search page. Default is fine for most users. |

3. **Test**, then **Save**.

## Indexer-wide options

Common options on the indexer (and under indexer settings):

- **Enable RSS / automatic search** — allow this indexer to be used by Mangarr's scheduled search for new and missing chapters.
- **Enable interactive search** — allow it to be used when you manually search a chapter.
- **Priority** — if you ever run more than one indexer entry, lower numbers are tried first.
- **Tags** — restrict an indexer to titles carrying matching tags.

## Search behaviour

- **Automatic search** covers the full result set across pages, so monitored chapters aren't missed.
- **Interactive search** shows the first page of results for you to choose from. See **[Searching & Grabbing](../usage/searching.md)**.

## Sources & languages

The **Sources** and **Languages** you select here must be ones your Gateway actually has enabled. If a search returns nothing, confirm the Gateway supports the selected source/language combination — see the **[MangarrGateway](../gateway.md)** page.

!!! note "Indexer vs. download client"
    The indexer is only half of the Gateway connection — it handles *searching*. You also need the **[Gateway Download Client](download-clients.md)** to actually *grab* and receive files. Both point at the same Gateway.
