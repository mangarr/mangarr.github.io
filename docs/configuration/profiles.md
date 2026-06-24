# Profiles

In Sonarr you'd pick a video quality. Manga has no resolution, so Mangarr uses a **Translation Profile** instead: it expresses which translation **language(s)** you accept, how they rank, and when a chapter is "good enough" to stop upgrading.

Find these under **Settings → Profiles**.

## Translation Profile

A Translation Profile has three parts:

1. **Accepted languages** — the languages Mangarr is allowed to grab. A release in a language not on the list is rejected.
2. **Priority order** — languages are ranked. A higher-ranked language is *preferred*, and Mangarr will upgrade a chapter to it if it appears.
3. **Upgrade cutoff** — the point at which a chapter is considered done. Once a chapter reaches the cutoff language, Mangarr stops looking for anything "better".

### Example

```
Accepted (highest → lowest priority):
  1. English (Official)
  2. English
Upgrade cutoff: English
```

With this profile:

- Any English release is acceptable.
- *English (Official)* is preferred over a generic English scanlation.
- The cutoff is *English*, so once any English release is downloaded the chapter is "met" — but because *English (Official)* ranks higher, Mangarr will still upgrade to it if it later appears, until the cutoff is satisfied.

!!! tip "Cutoff controls how aggressive upgrades are"
    Set the cutoff to your **highest** acceptable language to keep chasing the best release. Set it to your **lowest** to treat the first acceptable download as final and avoid upgrades.

## How profiles interact with search

When Mangarr evaluates releases for a chapter (automatic or **[interactive search](../usage/searching.md)**):

1. Releases in a language **not** on the profile are rejected outright.
2. Remaining releases are ranked by language priority, then adjusted by **[Custom Formats](custom-formats.md)** scores.
3. The highest-scoring release that's an actual improvement (and below the cutoff) wins.

Chapters that have a file but haven't reached the cutoff appear under **[Wanted → Cutoff Unmet](../usage/wanted.md)**.

## Assigning a profile

- Choose a Translation Profile when **[adding a manga](../usage/adding-manga.md)**.
- Change it later via **Edit** on a title, or in bulk from the library table.
- Different titles can use different profiles (e.g. a stricter English-only profile for some, a multi-language profile for others).

## Other profile types

Mangarr also supports auxiliary profiles inherited from the *arr platform, such as **Delay Profiles** (wait a set time before grabbing, to let a preferred release appear first) and **Release Profiles**. Most users only need a Translation Profile plus **[Custom Formats](custom-formats.md)**.
