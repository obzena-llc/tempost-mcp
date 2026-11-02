<p align="center"><img src="logo.png" width="96" alt="Tempost logo"></p>

# Tempost MCP server

Tempost is a social media scheduler for AI agents and people. This is the public documentation for its hosted remote
MCP server, which lets Claude, ChatGPT, Cursor and other MCP clients schedule and publish posts for you.

- **Server URL:** `https://app.tempost.io/mcp`
- **Transport:** Streamable HTTP (stateless, JSON responses), MCP protocol 2025-11-25
- **Auth:** OAuth 2.1 (sign in with your Tempost account) or a Tempost API key
- **Website:** https://tempost.io
- **MCP Registry name:** `io.tempost/tempost`

This repository holds documentation only. The server is hosted by Tempost; there is nothing to install or run locally.

## Platform status

| Platform | Status |
| --- | --- |
| YouTube | Available: upload, schedule, thumbnails, analytics |
| Facebook Pages | Coming soon (Meta app review in progress) |
| Instagram | Coming soon (Meta app review in progress) |

## Tools

| Tool | What it does | Type |
| --- | --- | --- |
| `list_channels` | Lists the channels connected to your workspace, their ids, capabilities and brand group | read-only |
| `list_posts` | Lists scheduled, queued, published and failed posts, filtered by time window, channel and status | read-only |
| `get_post` | Gets one post with status, times, text, media and platform URL (follow an upload to completion) | read-only |
| `create_post` | Creates a post on one or more channels; omit `publish_at` to publish now, set it to schedule | write |
| `update_post` | Changes text, publish time or thumbnail of a post that is not published yet | write, destructive |
| `delete_post` | Deletes or cancels a post (optionally on the platform too) | write, destructive |
| `upload_media` | Downloads an image or video from a public URL into Tempost and returns a `media_id` (up to 1 GB, kept 7 days) | write |
| `get_analytics` | Channel or post analytics (YouTube views, watch time, subscribers, top videos) | read-only |

Every tool has a `title` and `readOnlyHint` / `destructiveHint` annotations.

## Connect

### Option 1: OAuth (Claude, ChatGPT, MCP Inspector)

Add a custom connector with the URL `https://app.tempost.io/mcp`. Your client discovers the authorization server
(RFC 9728 protected resource metadata, RFC 8414, dynamic client registration and Client ID Metadata Documents, PKCE S256),
you sign in to Tempost and choose what the agent may do: read, schedule, delete.

### Option 2: API key (Claude Code, Cursor, scripts)

1. Sign in at https://app.tempost.io and open **Agents**.
2. Create an API key. It is shown once; copy it.
3. Add the server with the key as a bearer token:

```bash
claude mcp add --transport http tempost https://app.tempost.io/mcp \
  --header "Authorization: Bearer YOUR_TEMPOST_API_KEY"
```

Generic client config:

```json
{
  "mcpServers": {
    "tempost": {
      "type": "http",
      "url": "https://app.tempost.io/mcp",
      "headers": { "Authorization": "Bearer YOUR_TEMPOST_API_KEY" }
    }
  }
}
```

A key belongs to one workspace, can be revoked any time in **Agents**, and is stored by Tempost only as a hash.
Creating posts through agents needs an active Tempost plan.

## Limits

- 120 requests per minute and 60 writes per hour per key
- YouTube: 6 uploads per 24 hours per workspace (YouTube API quota)
- Media: up to 1 GB, public http(s) URLs only

## Example prompts

- "List my Tempost channels."
- "Upload this video URL to my YouTube channel as a private video scheduled for Friday 18:00 UTC, title: Spring launch."
- "Show my scheduled posts for next week."
- "How many views did my last 5 YouTube videos get?"

## Privacy and terms

- Privacy policy: https://tempost.io/privacy
- Terms of service: https://tempost.io/terms
- Support: https://tempost.io/contact

## License

Documentation in this repository is released under the MIT License. The Tempost service is proprietary.
