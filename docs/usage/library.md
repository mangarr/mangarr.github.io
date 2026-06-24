# Your Library

The library is your home base — every manga you track, with its monitoring state and chapter progress at a glance.

## Views

Switch between layouts to suit your collection:

- **Posters** — cover-art grid, great for browsing visually.
- **Overview** — larger cards with descriptions and progress.
- **Table** — dense, sortable rows ideal for large libraries.

Use **sort**, **filter**, and **search** to slice the library by monitored status, translation profile, tags, missing chapters, and more. Custom filters can be saved for quick reuse.

## Chapter status at a glance

Each title shows how complete it is. On the detail page, individual chapters carry a status:

| Status | Meaning |
|--------|---------|
| **Downloaded** | The chapter file is present and meets your profile. |
| **Missing** | The chapter is monitored but not yet downloaded. |
| **Unmonitored** | The chapter is ignored by automatic search. |
| **Queued / Downloading** | The chapter is being grabbed via the Gateway (see [Activity](activity.md)). |
| **Upgrade available** | A better match (preferred language/format) exists and will be fetched. |

## The detail page

Open a title to:

- See its cover, description, and metadata (from your **[metadata source](../configuration/metadata.md)**).
- Browse the full **chapter list** with per-chapter status and file info.
- **Monitor / unmonitor** individual chapters or the whole title.
- Trigger an **automatic search** for missing chapters, or open an **[interactive search](searching.md)** for a specific chapter.
- **Refresh** metadata, or **rename** files to match your current naming scheme.

## Monitoring from the library

Monitoring decides what Mangarr chases automatically. You can toggle it:

- For a **whole title** (Edit → Monitor).
- For **individual chapters** on the detail page.
- For **many titles at once** using bulk edit in the table view.

Only monitored chapters are searched automatically and reported under **[Wanted](wanted.md)**.

## Bulk actions

Select multiple titles to:

- Change **monitoring**, **translation profile**, or **root folder**.
- Add or remove **tags**.
- Trigger **searches** or **metadata refreshes**.
- **Delete** (optionally removing files).

## Calendar

The **Calendar** shows upcoming and recent chapter releases for your monitored titles, so you can see at a glance what's expected and when. It can be subscribed to via an iCal feed for use in external calendar apps.

Next: **[Searching & Grabbing](searching.md)**.
