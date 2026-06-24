# Metadata

Metadata is the information *about* your manga — title, cover art, description, and the chapter list Mangarr tracks against. Two areas control it.

## Metadata Source

**Settings → Metadata Source** chooses where Mangarr looks up titles when you search to **[add a manga](../usage/adding-manga.md)**, and where it refreshes details from.

Supported sources:

| Source | Notes |
|--------|-------|
| **MangaDex** | Broad catalogue with rich chapter data. |
| **AniList** | Strong metadata and cross-references. |
| **MyAnimeList (MAL)** | Familiar catalogue and IDs. |

The metadata source supplies the canonical title, cover, synopsis, and the list of chapters Mangarr expects to exist — which is what "missing" is measured against.

!!! note "Metadata source vs. the Gateway"
    The **metadata source** describes a title and its chapters. The **[Gateway](../gateway.md)** actually finds and downloads those chapters. They're separate: metadata says *what should exist*, the Gateway *fetches it*.

## Metadata (file/sidecar) settings

**Settings → Metadata** controls what Mangarr writes to disk alongside your files, so other apps can read it:

- **Cover images** — save the title's cover into its folder.
- **Sidecar metadata files** — write metadata files some readers/scanners use.

Enable the formats your reader benefits from. Komga and Kavita can read embedded/sidecar metadata, though they also scan covers and titles on their own.

## Refreshing metadata

- Mangarr periodically refreshes title metadata so new chapters in the source are discovered.
- Force a refresh on a single title from its detail page (**Refresh**), or refresh the whole library from the system tasks.
- A refresh updates the chapter list, which can surface newly **missing** chapters for automatic search.

## Choosing a source

- If you add titles mainly from one ecosystem (e.g. you track everything on AniList), match the metadata source and your **[import lists](import-lists.md)** to it for the cleanest matching.
- Switching sources later is possible, but IDs differ between providers, so existing titles may need a re-match/refresh.
