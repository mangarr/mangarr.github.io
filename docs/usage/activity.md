# Activity: Queue, History & Blocklist

The **Activity** area shows what Mangarr is doing right now and what it has done. It has three tabs.

## Queue

The **Queue** lists chapters currently being downloaded by the **[Gateway](../getting-started/gateway-setup.md)** or waiting to be imported.

For each item you can see:

- The title and chapter.
- Progress and current status (downloading, importing, etc.).
- Any **import warnings** — if Mangarr can't automatically import a finished download (ambiguous match, permissions, etc.) it surfaces it here.

Actions:

- **Remove** an item from the queue. You can optionally **blocklist** it at the same time so the same release isn't grabbed again, and trigger a **search for a replacement**.
- **Manual import** — if an item finished but couldn't be auto-imported, resolve it via **[Manual Import](manual-import.md)**.

## History

**History** is the audit log of everything Mangarr has done per chapter:

- **Grabbed** — a release was sent to the Gateway to download.
- **Imported** — a finished file was added to your library.
- **Upgraded** — a chapter was replaced by a better release.
- **Deleted / Failed** — a file was removed, or a grab/import failed.

Use history to understand *why* a chapter is in its current state, or to find the source/group a particular file came from. History can be filtered and searched.

## Blocklist

The **Blocklist** holds releases you never want Mangarr to grab again. A release lands here when:

- You remove a queue item and choose **blocklist**, or
- A download fails in a way Mangarr decides to avoid retrying.

Blocklisted releases are skipped by both automatic and interactive search. When Mangarr blocklists a failed grab, it can automatically search for an alternative so the chapter still gets filled.

You can **remove** entries from the blocklist at any time to allow that release to be considered again.

!!! tip "Stuck chapter? Check all three tabs"
    If a chapter won't download: look in the **Queue** for an import warning, check **History** for a failed grab, and check the **Blocklist** in case the only available release was blocklisted.

Next: see what's still outstanding under **[Wanted](wanted.md)**.
