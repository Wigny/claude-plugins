---
name: markdown-new
description: Alternative skill for fetching a single web page as Markdown via `https://markdown.new/<target_url>` when it is already known that the site does not support `Accept: text/markdown`, or when the cloudflare-markdown skill encounters an error or unsupported content type. Use the WebFetch tool with the markdown.new proxy URL. Never use as a fallback after curl already retrieved a response — work with that response instead.
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

1. Invoke this skill **instead of** `cloudflare-markdown` (not after it) when any of the following is already known before fetching:
   - The site was previously confirmed to not support `Accept: text/markdown`
   - The source document is not HTML (PDF, JSON, etc.)
   - The origin response is expected to exceed 2 MB

   Also invoke this skill if `cloudflare-markdown` encountered an HTTP error or timeout (i.e. no usable response was returned).

   Do NOT invoke this skill if `cloudflare-markdown` already returned a response — even if the content-type was HTML. Use that response directly instead of re-fetching.

2. Use `WebFetch` with `https://markdown.new/<target_url>` as the URL:
   - Example: fetch `https://markdown.new/https://example.com/docs/getting-started`

3. If `markdown.new` also fails (HTTP error, timeout, blocked content), fall back to another method and state the fallback.

4. For multi-page or recursive crawling, use the `markdown-new-crawl` skill instead.

## Output Expectation

Static, structured Markdown suitable for reading, summarization, extraction, and downstream processing.
