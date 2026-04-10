---
name: defuddle
description: Trigger whenever the user asks to fetch, read, clip, or extract content from a URL and metadata context matters — blog posts, articles, documentation pages where the author, publication date, or description are relevant to the task. Prefer over markdown-new and jina-reader when structured YAML frontmatter (title, author, date, description) is needed alongside the content.
---

## Defuddle

Fetch any publicly accessible URL as clean Markdown with YAML frontmatter metadata.

### Usage

Append the target domain and path directly to `defuddle.md`. Omit `https://` from the target URL:

```sh
curl -s "https://defuddle.md/example.com/path/to/page"
```

Examples:

```sh
curl -s "https://defuddle.md/github.com/owner/repo"
curl -s "https://defuddle.md/stephango.com/some-article"
curl -s "https://defuddle.md/docs.example.com/getting-started"
```

No authentication required.

### Output Format

Returns clean Markdown preceded by YAML frontmatter:

```yaml
---
title: Page Title
author: Author Name
date: 2024-01-01
description: Page description
url: https://example.com/page
---

# Article content...
```

### Characteristics

- Strips navigation, sidebars, ads, and boilerplate — main content only
- YAML frontmatter includes title, author, publication date, description, and source URL
- No documented rate limit
- Static pages only via the API — JavaScript-rendered pages require the browser extension

### Limitations

- Target must be publicly accessible
- JavaScript-heavy pages may return incomplete content via the API

### Fallback

If defuddle returns incomplete content or fails, use `markdown-new` with browser rendering:

```sh
curl -s -X POST "https://markdown.new/" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com/page", "method": "browser"}'
```
