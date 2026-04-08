---
name: markdown-new-crawl
description: Use `https://markdown.new/crawl/{target_url}` endpoints to recursively crawl a site section and return markdowns. Trigger this skill when the user asks for multi-page extraction, whole-docs crawl, link-depth crawling, or job-based crawl polling from a URL. Use curl or wget (via the Bash tool) for all requests.
version: 2.0.0
---

# Markdown.New Crawl

Recursively crawl a site section and return Markdown using the `markdown.new` crawl API.

## Required Behavior

1. Use `curl` or `wget` via the Bash tool for all requests (GET, POST, DELETE).
2. Assume no authentication required unless service behavior changes.
3. Start with `POST /crawl` to get a job ID, then poll `GET /crawl/status/{jobId}` until complete.
4. Default to Markdown output; use `?format=json` only when structured output is needed.
5. Keep crawl scope explicit using `limit`, `depth`, subdomain/external toggles, and include/exclude patterns.
6. Default crawl behavior: same-domain links only, up to 500 pages per job.
7. If `/crawl` fails, fall back to another method and state the fallback.

## Core Commands

- **Start crawl** (POST):
  - Method: POST
  - URL: `https://markdown.new/crawl`
  - Header: `Content-Type: application/json`
  - Body: `{"url": "<target_url>", "limit": 50, "depth": 3}`
  - Returns a `jobId`.

- **Poll status** (GET):
  - Method: GET
  - URL: `https://markdown.new/crawl/status/<jobId>`
  - Repeat until `status` is `completed`.

- **Cancel crawl** (DELETE):
  - Method: DELETE
  - URL: `https://markdown.new/crawl/status/<jobId>`

- **Browser-style shortcut** (GET):
  - Method: GET
  - URL: `https://markdown.new/crawl/<target_url>`

## Common Crawl Options

| Parameter | Range / Default | Description |
|-----------|----------------|-------------|
| `url` | required | Target URL to crawl |
| `limit` | 1–500, default 500 | Max pages to crawl |
| `depth` | 1–10, default 5 | Link depth |
| `render` | boolean | Enable JS rendering |
| `includeExternalLinks` | boolean | Follow external links |
| `includeSubdomains` | boolean | Include subdomains |
| `maxAge` | seconds | Max cache age |

## Operational Notes

- Results retained for 14 days.
- Rate limit: ~10 crawls/day (500 units/day, 50 units per crawl).
- Concatenated Markdown returned by default; append `?format=json` for structured output.
