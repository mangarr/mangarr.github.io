# Gateway Sources

A **source** is one manga site the Gateway can search and download from. Every source is enabled by default; in Mangarr you choose which ones (and which languages) to use on the **[Indexer](../configuration/indexers.md)**. The Gateway normalizes them all behind one API, so a release from any source downloads the same way — into a finished CBZ.

This page is a reference for what's available. For install and configuration, see **[MangarrGateway](../gateway.md)**.

## Source reference

| Source | Type | Protection | Languages | Rate limit (req/min) | Android solver |
|--------|------|-----------|-----------|:--------------------:|:--------------:|
| **MangaDex** | Official API | None | `en` `es` `es-la` `fr` `de` `pt-br` `ru` `ja` `ko` `zh` | 300 | — |
| **MangaFire** | Aggregator | Cloudflare¹ | `en` `fr` `es` `es-la` `pt-br` `ja` | 60 | — |
| **Atsumaru** | Aggregator | None | `en` | 480 | — |
| **Weeb Central** | Aggregator | None | `en` | 60 | — |
| **ProjectSuki** | Aggregator | None | `en` | 480 | — |
| **Comix** | Aggregator | Cloudflare | `en` | 120 | ✅ Required |
| **Mangadot** | Aggregator | Cloudflare | `en` | 480 | ✅ Required |
| **MangaBall** | Aggregator | Cloudflare | `ar` `ca` `de` `en` `es` `es-la` `fr` `id` `it` `ja` `ko` `pt-br` `ru` `vi` | 480 | ✅ Required |
| **Kagane** | Aggregator | Cloudflare | `en` | 24 | ✅ Required |

¹ MangaFire is behind Cloudflare, but the Gateway clears it with its **built-in browser** — no solver stack needed.

!!! info "Reading the table"
    - **Type** — *Official API* is MangaDex's first-party API (it also accepts external-ID lookups; see below). *Aggregator* sources are third-party sites the Gateway scrapes or queries.
    - **Protection** — whether the site sits behind Cloudflare. *None* sources are the most reliable everywhere.
    - **Languages** — translation languages the source offers (`es-la` = Latin-American Spanish, `pt-br` = Brazilian Portuguese, `zh` = Chinese). You pick which to request per-indexer in Mangarr.
    - **Rate limit** — the maximum requests-per-minute the Gateway sends to that source. These are pre-tuned; you don't normally change them.
    - **Android solver** — whether the source needs the optional solver stack (see below).

## Which sources need the solver stack?

Four sources — **Comix, Mangadot, MangaBall, and Kagane** — sit behind a strict Cloudflare challenge that only a real Android WebView can clear. They require the Gateway's optional **`android` profile** (the `redroid` + `android-solver` sidecars, plus a few Linux host prerequisites).

!!! tip "You don't have to run them all"
    If you don't enable the solver stack, those four sources simply boot **disabled** and everything else keeps working. The other five — **MangaDex, MangaFire, Atsumaru, Weeb Central, ProjectSuki** — run with just the gateway container. See **[Install (Docker)](../gateway.md#install-docker)** for both paths.

## Notes

- **MangaDex external IDs** — beyond title search, MangaDex releases can be matched by `mangadexId`, `anilistId`, or `malId`, which makes it the most reliable source for exact-match grabbing.
- **Language availability is per-title** — a source advertising a language doesn't guarantee every series has it. If a search returns nothing, widen the languages or try another source.
- **Regional/IP differences** — a few sources behave differently from datacenter vs residential IPs (Cloudflare is stricter on cloud hosts). If a Cloudflare source is flaky on a cloud server, route egress through a residential proxy — see **[Tuning and advanced options](../gateway.md#tuning-and-advanced-options)**.
- **Disabling sources** — to drop a source on the Gateway entirely, set `GATEWAY_DISABLED_SOURCES` (see the [Gateway page](../gateway.md#configuring-sources-and-languages)).

## Adding more sources

The Gateway is built to add sources without touching its core — the list grows over time. The set above reflects the current release; check the **[MangarrGateway repo](https://github.com/devbrian/MangarrGateway)** for the latest, and your Gateway's own `/api/v1/caps` response always lists exactly what your instance has registered.
