# Custom Formats

**Custom Formats** let you fine-tune which releases Mangarr prefers or avoids, beyond what the **[Translation Profile](profiles.md)** language ranking does on its own. They're the manga equivalent of Sonarr's custom formats.

Find them under **Settings → Custom Formats**.

## What they're for

Use a custom format to express preferences like:

- **Prefer** a specific scanlation group you trust.
- **Avoid** (or never grab) releases from a group whose quality you dislike.
- **Prefer** official translations, or releases tagged a certain way.

## How they work

A custom format is a named set of **conditions**. When a release matches a format's conditions, the format's **score** is added to that release's total score during a search. Higher total score = more preferred.

- **Positive score** → preferred. Among acceptable releases, higher scores win.
- **Negative score** → avoided. Still allowed, but only if nothing better exists.
- **Score of a "must not have" format** → set the profile's minimum so matching releases are rejected entirely.

Conditions match on attributes the **[Gateway](../getting-started/gateway-setup.md)** reports for a release — for example the scanlation group/source name or the release title.

## Putting it together

1. Create a Custom Format (e.g. *Preferred Group*) with a condition matching the group name.
2. Give it a score (e.g. `+100`).
3. In your **[Translation Profile](profiles.md)**, set the **score thresholds**:
   - A **minimum score** a release must reach to be accepted.
   - An **upgrade-until score** — once a download reaches it, stop upgrading.

During search, Mangarr sums the language ranking and all matching custom-format scores, then grabs the best acceptable release. See **[Searching & Grabbing](../usage/searching.md)** for the full decision flow.

## Tips

- Start simple — a single "preferred group" format with a modest positive score is enough for most libraries.
- Use **[interactive search](../usage/searching.md)** to see each release's computed score and *why* it was accepted or rejected; tune your formats from there.
- Negative scores are gentler than outright rejection — use them when you'd accept a group only as a last resort.
