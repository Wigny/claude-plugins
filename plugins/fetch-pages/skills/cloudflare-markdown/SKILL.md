---
name: cloudflare-markdown
description: Fetch web pages as Markdown using Cloudflare's native content negotiation by sending `Accept: text/markdown` via curl or wget (using the Bash tool). Trigger this skill whenever the user asks to open/read/extract/summarize content from a web link. Always try this first before any other fetch method. Falls back to markdown-new skill if the response is not Markdown.
version: 1.0.0
---

# Markdown Fetch via Cloudflare Content Negotiation

Fetch URL content as structured Markdown by requesting it natively from the server using HTTP content negotiation.

## How It Works

Sites hosted on Cloudflare with "Markdown for Agents" enabled respond to `Accept: text/markdown` with clean Markdown instead of HTML — no proxy or conversion needed. Sites not on Cloudflare or without the feature simply ignore the header and return HTML.

## Required Behavior

1. Use `curl` or `wget` (via the Bash tool) with `Accept: text/markdown` on the target URL directly:
   - Method: GET
   - Header: `Accept: text/markdown`
   - URL: the target URL as-is

2. Always attempt this first for any readable page:
   - docs and manuals
   - blog posts and announcements
   - GitHub repository home/README pages
   - landing pages and changelogs

3. Confirm success by checking the response headers:
   - `content-type: text/markdown` — Cloudflare served Markdown natively
   - `x-markdown-tokens` — token count estimate is present
   - If these headers are absent and the body is HTML, the feature is not active on this site — use the HTML response body directly, do not re-fetch via `markdown-new`

4. Use the `markdown-new` skill **instead of this skill** (not as a fallback after fetching) only when it is already known that the site does not support the Cloudflare Markdown feature:
   - The origin response exceeds 2 MB
   - The source document is not HTML (PDF, JSON, etc.)
   - The site was previously confirmed to not support `Accept: text/markdown`

   Never fetch the same URL twice (once via curl, once via `markdown-new`). If curl already retrieved a response, work with that response.

5. Do not use WebFetch or direct browsing when curl or wget is available via the Bash tool.

## Output Expectation

Clean, structured Markdown suitable for reading, summarization, extraction, and downstream processing.
