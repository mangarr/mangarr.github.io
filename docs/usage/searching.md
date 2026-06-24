# Searching & Grabbing

Mangarr finds chapters two ways: **automatically** in the background, and **interactively** when you want to pick a specific result yourself. Both rely on your **[Gateway](../getting-started/gateway-setup.md)** connection.

## Automatic search

Mangarr searches on its own when:

- You **add a title** with *Search on add* enabled.
- A scheduled **chapter refresh** discovers a new monitored chapter.
- You manually trigger **Search Monitored** for a title or for missing chapters.

For each monitored, missing chapter, Mangarr asks the Gateway for results, runs them through the **[Decision Engine](#how-mangarr-chooses-a-release)**, and grabs the best acceptable match. Automatic search covers the full result set across pages so nothing is missed.

To run it manually:

- On a title's detail page, use **Search** (searches all monitored missing chapters).
- From **[Wanted → Missing](wanted.md)**, search individual chapters or everything at once.

## Interactive search

When you want control — to choose a specific scanlation group, language, or source — use interactive search:

1. Open a title's detail page (or a row in **[Wanted](wanted.md)**).
2. Click the **interactive search** (manual search) icon for a chapter.
3. Mangarr queries the Gateway and lists the available releases with their language, source, and group.
4. Each result shows whether it would be **accepted** or **rejected**, and why (e.g. *rejected: language not in profile*).
5. Click a result to **grab** it. It moves to the **[Queue](activity.md)**.

!!! tip "Rejected results"
    A red/rejected result tells you exactly which rule blocked it (translation profile, custom format, already-downloaded, etc.). This is the fastest way to understand why an automatic search isn't grabbing what you expected — adjust your **[profile](../configuration/profiles.md)** or **[custom formats](../configuration/custom-formats.md)** accordingly.

## How Mangarr chooses a release

When several results exist for a chapter, the **Decision Engine** scores and filters them. The main factors:

- **Translation Profile** — is the result's language accepted, and how preferred is it? See **[Profiles](../configuration/profiles.md)**.
- **Custom Formats** — score adjustments for preferred/avoided scanlation groups or attributes. See **[Custom Formats](../configuration/custom-formats.md)**.
- **Upgrade rules** — if a chapter already exists, is the new result actually *better* and below your cutoff?
- **Already grabbed / blocklisted** — results you've blocklisted or that match an existing download are skipped.

The highest-scoring acceptable result wins.

## Upgrades

If you've set your profile to keep looking for a more-preferred language (until the cutoff), Mangarr will replace an existing chapter when a better release appears — automatically during scheduled searches, or whenever you grab a better one interactively. The previous file is removed once the upgrade imports cleanly.

## Search frequency & RSS

Mangarr periodically checks for newly released chapters of monitored titles so it can grab them as they appear, without you doing anything. You can also force a check at any time from a title's page or from **[Wanted](wanted.md)**.

Next: track what's in flight under **[Activity](activity.md)**.
