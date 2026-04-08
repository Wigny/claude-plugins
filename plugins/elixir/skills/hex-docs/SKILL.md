---
name: hex-docs
description: Fetch and read Elixir package documentation. Trigger this skill whenever the user asks about an Elixir/Erlang package's API, functions, options, or behavior. Resolves docs through a local-first strategy: project deps folder, then local hex.docs cache, then hexdocs.pm.
version: 1.0.0
---

# Hex Package Documentation

Resolve Elixir and Erlang package documentation using a local-first strategy, falling back to the network only when necessary.

## Resolution Strategy

Check sources in this priority order and stop at the first one that yields useful content:

### 1. Local project `deps/` folder

Look inside the project's `deps/<package>/` directory for documentation or source:

- `deps/<package>/doc/` — pre-built ExDoc HTML/Markdown output
- `deps/<package>/lib/` — source `.ex` files with `@moduledoc` / `@doc` attributes

Read `.md` files first when both formats are present. Source files in `lib/` are often richer than generated docs — `@doc` strings are authoritative.

### 2. Local hex.docs cache via `mix hex.docs fetch`

If the deps folder is absent or contains no docs, fetch and cache the documentation locally:

```
mix hex.docs fetch PACKAGE
mix hex.docs fetch PACKAGE VERSION
```

This stores docs under `$HEX_HOME/docs/hexpm/<package>/<version>/` (default `~/.hex/docs/hexpm/`). After fetching, read the files directly from disk. Prefer `.md` files over `.html` files when both exist.

To open the docs interactively (browser), use:
```
mix hex.docs online PACKAGE
mix hex.docs online PACKAGE VERSION
```

### 3. hexdocs.pm (network fallback)

When local options are unavailable, fetch directly from `https://hexdocs.pm`. Always request the `.md` version of a page instead of `.html`:

- Module page: `https://hexdocs.pm/<package>/<Module>.md`
- Versioned: `https://hexdocs.pm/<package>/<version>/<Module>.md`
- Guide/extra: `https://hexdocs.pm/<package>/<guide-slug>.md`

Examples:
```
# Prefer:
https://hexdocs.pm/phoenix_live_view/Phoenix.LiveView.md

# Avoid:
https://hexdocs.pm/phoenix_live_view/Phoenix.LiveView.html
```

Use the `cloudflare-markdown` or `markdown-new` fetch skills when fetching from hexdocs.pm to get clean Markdown output.

## Version Resolution

Determine the package version to use in this order:

1. `mix.lock` — the locked version is authoritative for a running project
2. `mix.exs` — the declared requirement
3. Latest stable release if no project context is present

## Required Behavior

- Always prefer `.md` over `.html` when both exist, whether on disk or on hexdocs.pm.
- Do not fetch from the network if a local source already provides the needed information.
- When fetching with `mix hex.docs fetch`, read from the resulting local files rather than re-fetching from the network.
- For source files (`lib/`), surface `@moduledoc` and relevant `@doc` strings directly — they are more accurate than derived HTML.
