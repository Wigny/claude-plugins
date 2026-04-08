---
name: markdown-new
description: Fallback skill for fetching a single web page as Markdown via `https://markdown.new/<target_url>` when the cloudflare-markdown skill fails or the site does not support `Accept: text/markdown`. Use the WebFetch tool with the markdown.new proxy URL.
version: 2.0.0
---

# Markdown.New Single-Page Fallback

Use `markdown.new` as a fallback to convert any web page to Markdown when native Cloudflare content negotiation is unavailable.

## How It Works

`markdown.new` is a Cloudflare-powered proxy that handles `Accept: text/markdown` on behalf of any URL. It uses a three-tier pipeline:
1. Requests the target with `Accept: text/markdown` natively
2. Falls back to Cloudflare Workers AI `toMarkdown()` conversion
3. Falls back to headless browser rendering for JS-heavy pages

## Required Behavior

1. Only invoke this skill after the `cloudflare-markdown` skill has failed. Trigger conditions:
   - Response `content-type` was not `text/markdown` (site not on Cloudflare or feature disabled)
   - Origin response exceeded 2 MB
   - Source document is not HTML (PDF, JSON, etc.)
   - HTTP error or timeout from the direct request

2. Use `WebFetch` with `https://markdown.new/<target_url>` as the URL:
   - Example: fetch `https://markdown.new/https://example.com/docs/getting-started`

3. If `markdown.new` also fails (HTTP error, timeout, blocked content), fall back to another method and state the fallback.

4. For multi-page or recursive crawling, use the `markdown-new-crawl` skill instead.

## Output Expectation

Static, structured Markdown suitable for reading, summarization, extraction, and downstream processing.
