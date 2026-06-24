# Manual Import

Most of the time Mangarr imports finished downloads automatically. **Manual Import** is for the cases where it can't — or for bringing an existing collection of files into Mangarr.

## When you'd use it

- A download finished but Mangarr couldn't match it to a chapter automatically (it shows an **import warning** in the **[Queue](activity.md)**).
- You already have a folder of CBZ files you want Mangarr to adopt into a tracked title.
- You moved/renamed files outside Mangarr and want them recognized.

## Importing existing files

1. Open **Manual Import** (also reachable as **Library Import** / the import action in the library).
2. Choose the folder containing the files.
3. Mangarr scans the folder and tries to parse each file into **title + chapter**.
4. Review the proposed matches. For each row, confirm or correct:
   - The **manga** it belongs to.
   - The **chapter** number it represents.
   - The **language / translation** it should be recorded as.
5. Rows that Mangarr couldn't parse are flagged — fix them before importing.
6. Click **Import**.

Mangarr then files the chapters under the correct title, records them in **[History](activity.md)**, and (depending on your **[Media Management](../configuration/media-management.md)** settings) renames them to your scheme.

## Resolving a stuck queue item

If a download is sitting in the Queue with an import warning:

1. Open the **[Queue](activity.md)** and select the item.
2. Choose **Manual Import**.
3. Confirm the title/chapter/language match.
4. Import.

This is common when a release's filename doesn't clearly indicate the chapter number, or when one download contains multiple chapters.

## Tips for clean imports

- **Copy vs. move:** Mangarr can move or copy depending on your media-management settings. Keeping the source folder and your library under one common volume lets it move/hardlink instead of slow-copying. See the volume note in **[Installation](../getting-started/installation.md)**.
- **Naming matters:** files that include a clear chapter number (and language) parse far more reliably. Inconsistent names are the main reason manual fixes are needed.
- **Permissions:** if imports fail to move files, re-check `PUID`/`PGID`/`UMASK` (Docker) — see **[Troubleshooting](../troubleshooting.md)**.
