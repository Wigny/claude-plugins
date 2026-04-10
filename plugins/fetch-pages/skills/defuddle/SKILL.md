---
name: defuddle
description: Trigger whenever the user asks to fetch, read, clip, or extract content from a URL and metadata context matters — blog posts, articles, documentation pages, forum threads (including Discourse), or any page where author, publication date, description, or word count are relevant. Prefer over markdown-new and jina-reader when structured YAML frontmatter (title, author, date, description, word count, language) is needed alongside the content.
---

## Defuddle

Fetch any publicly accessible URL as clean Markdown with YAML frontmatter metadata.

### Usage

Append the target domain and path directly to `defuddle.md`. Omit `https://` from the target URL. Use WebFetch:

```
https://defuddle.md/example.com/path/to/page
```

Examples:

```
https://defuddle.md/github.com/owner/repo
https://defuddle.md/stephango.com/some-article
https://defuddle.md/docs.example.com/getting-started
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
- YAML frontmatter includes title, author, publication date, description, word count, language, domain, and source URL
- Code blocks often include language hints (e.g. ```elixir, ```ts) — markdown-new and jina-reader typically omit them; detection is not always accurate
- No documented rate limit
- Works on SSR-capable pages including Discourse forums, blog platforms, and documentation sites

### Limitations

- Target must be publicly accessible
- JavaScript-only SPAs with no SSR may return incomplete or empty content

### Fallback

If defuddle returns incomplete content or fails, use `markdown-new` with browser rendering via `Bash` with `curl` (POST required):

```sh
curl -s -X POST "https://markdown.new/" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com/page", "method": "browser"}'
```
