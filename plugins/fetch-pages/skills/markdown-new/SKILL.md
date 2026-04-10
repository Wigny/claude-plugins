---
name: markdown-new
description: Trigger whenever the user asks to fetch, read, open, extract, or summarize content from a public URL (docs, README, blog post, changelog, landing page). Best general-purpose fetcher — no auth, 3-tier fallback (Cloudflare native markdown → Workers AI → headless browser), site crawling. Fall back to jina-reader for PDFs, large content-heavy pages (truncation risk), or when the daily rate limit is hit.
---

## Markdown.New

Fetch any public URL as structured Markdown using markdown.new's 3-tier pipeline. No authentication required.

### Fetch Pipeline (automatic)

1. **Tier 1** — Requests page with `Accept: text/markdown` (native Markdown via Cloudflare, instant)
2. **Tier 2** — Converts HTML via Cloudflare Workers AI `toMarkdown()` (0.1–0.6s)
3. **Tier 3** — Renders in a headless browser for JavaScript-heavy pages (+1–2s)

### Basic Usage

If the URL already serves raw Markdown (path ends in `.md`), use WebFetch on it directly — no proxy needed.

For HTML pages, use WebFetch with the proxy URL:

```
https://markdown.new/https://example.com/page
```

### POST API

For more control (force a specific tier, retain images), use `Bash` with `curl`:

```sh
curl -s -X POST "https://markdown.new/" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com/page", "method": "auto"}'
```

Parameters:
- `url` — target page (required)
- `method` — `"auto"` (default), `"ai"` (force tier 2), `"browser"` (force tier 3)
- `retain_images` — `true` / `false` (default: `false`)

### Response Headers

- `x-markdown-tokens` — estimated token count, use for context window planning

### Site Crawling

Crawling is async — use `Bash` with `curl`. `POST /crawl` returns a `jobId`, then poll until complete:

```sh
# 1. Start crawl
curl -s -X POST "https://markdown.new/crawl" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com", "limit": 50, "depth": 3}'

# 2. Poll until status is "completed"
curl -s "https://markdown.new/crawl/status/<jobId>"

# 3. Cancel if needed
curl -s -X DELETE "https://markdown.new/crawl/status/<jobId>"
```

Key crawl parameters: `limit` (1–500 pages), `depth` (1–10 levels). Results expire after 14 days.

Rate limit: 50 units per crawl, 500 units/day (~10 crawls/day).

### Limitations

- 500 requests/day per IP (HTTP 429 when exceeded)
- Paywalled and authenticated content unsupported
- Large pages may be truncated
- Images excluded by default

### Fallback

If rate-limited or if the target is a PDF, use `jina-reader`:

```
https://r.jina.ai/https://example.com/page
```
