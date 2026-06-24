# Notifications (Connect)

**Connect** (Settings → Connect) lets Mangarr notify other apps and services when things happen — most importantly, telling your reader to rescan when new chapters land.

## First-class manga integrations

| Connection | What it does |
|------------|--------------|
| **Komga** | Triggers a library scan in Komga so new chapters appear in your reader automatically. |
| **Kavita** | Triggers a library scan/refresh in Kavita. |

These are the recommended integrations for a manga setup: point Mangarr at your Komga/Kavita server so your reader stays current without manual rescans.

### Setting up Komga / Kavita

1. **Settings → Connect → Add (+)** and choose **Komga** or **Kavita**.
2. Enter the server **URL** and the **credentials / API key** for that server.
3. Choose which events trigger it (typically **On Import / On Upgrade**).
4. **Test**, then **Save**.

After this, every time Mangarr imports or upgrades a chapter, your reader is told to pick it up.

## Other notification types

Mangarr inherits the broad *arr notification platform, so general-purpose notifiers (Discord, webhooks, email, and many push services) are available too. Add them the same way and pick the events you care about.

## Trigger events

Each connection lets you choose which events fire it. Common ones:

| Event | Fires when… |
|-------|-------------|
| **On Grab** | A release is sent to the Gateway to download. |
| **On Import** | A finished chapter is added to your library. |
| **On Upgrade** | A chapter is replaced by a better release. |
| **On Health Issue** | Mangarr detects a problem (e.g. Gateway unreachable). |
| **On Application Update** | Mangarr updates itself. |

For a reader integration like Komga/Kavita, **On Import** and **On Upgrade** are the important ones. For a chat/push notifier, you might also want **On Grab** and **On Health Issue**.

## Tips

- Use **Test** on every connection — it sends a sample notification so you can confirm credentials and connectivity before relying on it.
- Pair **On Health Issue** notifications with a chat service so you hear immediately if the **[Gateway](../gateway.md)** goes offline.
- Tag-restrict a connection if you only want notifications for certain titles.
