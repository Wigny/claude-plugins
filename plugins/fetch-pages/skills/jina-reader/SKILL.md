---
name: jina-reader
description: Trigger whenever the user asks to fetch, read, open, extract, or summarize content from a URL — and the target is a PDF, requires image captioning, needs CSS-selector scoping, or markdown-new is rate-limited. Also trigger for web search tasks (s.jina.ai). Falls back to markdown-new for general pages when no API key is configured.
---

## Jina Reader

Fetch any URL as clean Markdown. Handles JavaScript rendering, PDFs, images, shadow DOM, and iframes automatically.

### Basic Usage

```sh
curl -s "https://r.jina.ai/https://example.com/page"
```

For single-page applications with hash-based routing:

```sh
curl -s -X POST "https://r.jina.ai/" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com/#/route"}'
```

### Authentication

No API key required at 20 RPM. For higher limits:

```sh
curl -s "https://r.jina.ai/https://example.com" \
  -H "Authorization: Bearer JINA_API_KEY"
```

Rate limits:
- No key: 20 RPM (IP-based)
- Free API key: 500 RPM
- Paid: up to 5,000 RPM

### Output Formats

Control via `Accept` header (default: `text/markdown`):

```sh
# JSON — structured response with url, title, content, timestamp
curl -s "https://r.jina.ai/https://example.com" -H "Accept: application/json"

# Screenshot
curl -s "https://r.jina.ai/https://example.com" -H "Accept: image/png" -o screenshot.png
```

### Useful Request Headers

| Header | Purpose |
|--------|---------|
| `x-with-generated-alt: true` | Generate alt text for images |
| `x-target-selector: .article` | Scope extraction to a CSS selector |
| `x-remove-selector: nav,footer` | Strip elements before extraction |
| `x-no-cache: true` | Bypass the 5-minute cache |

### PDFs

```sh
curl -s "https://r.jina.ai/https://example.com/document.pdf"
```

### Web Search

```sh
curl -s "https://s.jina.ai/your+search+query"
```

### Fallback

If Jina Reader fails or returns incomplete content, use `markdown-new`:

```sh
curl -L --fail --silent --show-error "https://markdown.new/https://example.com/page"
```
