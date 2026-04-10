---
name: cloudflare-markdown
description: Trigger when fetching from a URL known to be on a Cloudflare Markdown-enabled zone. Fastest method — zero external proxy, edge conversion. Always verify via Content-Type response header before consuming; fall back to markdown-new if the site does not support it.
---

## Cloudflare Markdown for Agents

Fetch pages as Markdown via HTTP content negotiation on Cloudflare-enabled sites. No external service — Cloudflare converts HTML to Markdown at the edge before delivering the response.

### Usage

Use `Bash` with `curl` — response header inspection is required to verify support:

```sh
curl -s "https://example.com/page" \
  -H "Accept: text/markdown"
```

### Verifying Support

Always check the `Content-Type` response header first — if the site does not support this feature it returns HTML silently:

```sh
curl -sI "https://example.com/page" -H "Accept: text/markdown" | grep -i content-type
# Content-Type: text/markdown  ← supported
# Content-Type: text/html      ← not supported, fall back
```

### Response Headers

| Header | Description |
|--------|-------------|
| `Content-Type: text/markdown` | Confirms Markdown response |
| `x-markdown-tokens` | Estimated token count for context window planning |
| `Content-Signal: ai-train=yes, search=yes, ai-input=yes` | Permitted use signals |

### Limitations

- Only works on sites where the owner has explicitly enabled this Cloudflare feature (Pro plan or higher)
- HTML pages only — 2 MB maximum response size
- Does not handle JavaScript-rendered content
- Not a general-purpose fetcher

### Fallback

If the site returns HTML (feature not enabled), fall back to `markdown-new`:

```
https://markdown.new/https://example.com/page
```
