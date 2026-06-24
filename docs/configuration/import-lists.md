# Import Lists

**Import Lists** automatically add titles to Mangarr from an external list and keep your library in sync — so a list you curate on MangaDex, AniList, or MyAnimeList becomes a self-populating Mangarr library.

Find them under **Settings → Import Lists**.

## Supported lists

| Source | Typical use |
|--------|-------------|
| **MangaDex** | Your MangaDex follows / a custom list. |
| **AniList** | Your AniList manga list (e.g. *Reading*, *Planning*). |
| **MyAnimeList (MAL)** | Your MAL manga list. |

## Adding an import list

1. **Settings → Import Lists → Add (+)** and pick the source.
2. Authenticate / point it at the list (see [authentication](#authentication-oauth) below).
3. Set the **defaults applied to imported titles**:

| Setting | Description |
|---------|-------------|
| **Enable Automatic Add** | Actually add matching titles to Mangarr (off = list is fetched but nothing is added). |
| **Monitor** | Monitoring applied to newly added titles (All / Future / None). |
| **Root Folder** | Where new titles are stored. |
| **Translation Profile** | Profile applied to new titles. |
| **Tags** | Tags applied to new titles (handy for filtering list-added titles). |
| **Search on add** | Immediately search for chapters when a title is added. |

4. **Test**, then **Save**.

## Authentication (OAuth)

AniList and MyAnimeList use OAuth. You'll typically:

1. Register an application on the provider to get a **Client ID** (and, for some app types, a **Client Secret**).
2. Enter those in the import-list settings.
3. Complete the OAuth flow to authorize Mangarr, which stores an access/refresh token.

!!! tip "MAL app type"
    MyAnimeList "web" application types require a **Client Secret** in addition to the Client ID; "other" (public PKCE) types don't. If MAL authorization returns 401, double-check you've supplied the secret for a "web" app.

MangaDex can use your account credentials / a personal API client depending on the list you're pulling.

## List sync & cleanup

- Mangarr refreshes import lists on a schedule and adds any new entries.
- **Clean Library** options can act on titles that *fall off* a list — for example unmonitor or remove them. Use these carefully; the most conservative setting leaves existing titles untouched.
- **Import List Exclusions** let you permanently prevent specific titles from being auto-added (useful for things you removed on purpose).

## Tips

- Start with **Enable Automatic Add** off and **Test** to preview what a list would bring in.
- Tag list-added titles so you can find and manage them separately from hand-added ones.
- Combine with **[Notifications](notifications.md)** so you hear about new arrivals from your lists.
