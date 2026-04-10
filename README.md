# Claude Plugins

A personal [Claude Code](https://claude.ai/code) plugin marketplace.

## Installation

```sh
/plugin marketplace add wigny/claude-plugins
```

Then install individual plugins at user scope:

```sh
/plugin install fetch-pages --scope user
/plugin install elixir --scope user
```

## Plugins

### fetch-pages

Skills for fetching web pages as Markdown:

- **markdown-new** — General-purpose fetcher with 3-tier fallback (Cloudflare native → Workers AI → headless browser). Supports single-page and async site crawling. No auth required.
- **jina-reader** — Feature-rich fetcher with PDF support, image captioning, CSS-selector scoping, and optional API key for higher rate limits.
- **defuddle** — Fetches pages with YAML frontmatter metadata (title, author, date, description). Best for articles and blog posts.
- **cloudflare-markdown** — Fetches Markdown via `Accept: text/markdown` content negotiation on Cloudflare-enabled sites. Zero external proxy.

### elixir

Skills for Elixir development:

- **hex-docs** — Resolves Elixir/Erlang package documentation using a local-first strategy: project `deps/`, local hex.docs cache, then hexdocs.pm.
