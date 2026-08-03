# AGENTS.md — Bullring Finance API Documentation

## Project Overview

Mintlify-powered documentation site for the Bullring Finance API. Content is authored
in MDX (Markdown + JSX) with an OpenAPI 3.0 specification for auto-generated API
reference pages. The site is bilingual: English (`en/`) and Portuguese (`pt/`).

**Key technologies:** Mintlify, MDX, OpenAPI 3.0, npm

## Build / Dev / Deploy Commands

```bash
# Install dependencies
npm install

# Install Mintlify CLI globally (required for local preview)
npm i -g mintlify

# Start local dev server (run from repo root where docs.json lives)
mintlify dev

# Reinstall Mintlify dependencies if dev server breaks
mintlify install
```

**Deployment:** Automatic via Mintlify GitHub App on push to `main`. No manual
build or deploy step required.

**There are no tests, linters, or formatters configured in this repo.** The only
formatting guidance is `.editorconfig`.

## Project Structure

```
docs/
├── docs.json                  # Mintlify site config (nav, theme, branding)
├── api-reference/
│   └── openapi.json           # OpenAPI 3.0 spec (~11k lines) — auto-generates API ref
├── en/                        # English documentation
│   ├── introduction.mdx
│   ├── authentication.mdx
│   ├── environments.mdx
│   ├── accounts.mdx
│   ├── multi-currency-balances.mdx
│   ├── supported-currencies.mdx
│   ├── webhook-intro.mdx
│   ├── *-events.mdx           # Webhook event docs
│   └── use-cases/             # Integration guides
├── pt/                        # Portuguese (structural mirror of en/)
├── snippets/                  # Reusable Mintlify snippets
├── images/                    # PNG assets
├── logo/                      # SVG logos (dark.svg, light.svg)
├── .editorconfig              # Formatting rules (2-space indent, UTF-8, LF)
└── package.json
```

## Content Style Guidelines

### MDX Frontmatter

Every `.mdx` file must start with YAML frontmatter containing at least a `title`.
Use double quotes around values consistently:

```yaml
---
title: "Multi-currency Balances"
description: "Understanding how subaccounts manage multiple currency balances."
---
```

### Headings

- Never use `#` (H1) in the body — the `title` frontmatter serves as H1.
- Use `##` (H2) for major sections and `###` (H3) for subsections.
- Numbered steps in headings: `## 1. Receive Funds (NGN)`.

### Mintlify Components

Use built-in Mintlify JSX components where appropriate:

- `<CardGroup>` / `<Card>` — for navigation card grids
- `<CodeGroup>` — for tabbed code examples (curl, JS, etc.)
- `<Note>` — informational callouts
- `<Warning>` — important warnings
- `<Snippet file="snippet-intro.mdx" />` — reusable content

### Code Examples

- **curl:** Primary format for all API examples. Use `bash` fence.
- **JavaScript:** Use `javascript` fence. Prefer `const`, named imports,
  camelCase variables, `SCREAMING_SNAKE_CASE` for constants.
- Wrap multi-language examples in `<CodeGroup>`:

````mdx
<CodeGroup>
```bash cURL
curl -X POST https://api.bullring.finance/v1/ramp/subaccount \
  -H "Content-Type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  -d '{"name": "Treasury"}'
```
```javascript Node.js
const response = await fetch("https://api.bullring.finance/v1/ramp/subaccount", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "x-api-key": "YOUR_API_KEY",
  },
  body: JSON.stringify({ name: "Treasury" }),
});
```
</CodeGroup>
````

### Authentication in Examples

Use the `x-api-key` header style consistently across all examples:

```
-H "x-api-key: YOUR_API_KEY"
```

Avoid mixing with `Authorization: Bearer` style.

### Links

- Internal links use absolute paths from doc root: `[Accounts](/en/accounts)`
- API reference links: `[Create Subaccount](/api-reference/subaccounts/create-subaccount)`

## File and Directory Naming

- **All files and directories:** `kebab-case` (`multi-currency-balances.mdx`,
  `use-cases/`, `api-reference/`)
- **Images:** Descriptive kebab-case (`hero-light.png`, `checks-passed.png`)

## Bilingual Content Rules

- `en/` and `pt/` must be exact structural mirrors — same file names, same
  directory layout.
- When adding or renaming a page in `en/`, always create/update the corresponding
  file in `pt/`.
- Update `docs.json` navigation for both languages when adding new pages.

## Editing docs.json

`docs.json` is the Mintlify site configuration. Key sections:

- `navigation.languages[].tabs[].groups[].pages` — page ordering for each
  language and tab
- `navigation.global.anchors` — top-level external links
- `theme`, `colors`, `logo` — branding (rarely changed)

When adding a new page:
1. Create the MDX file in both `en/` and `pt/`.
2. Add the page path to the correct group in `docs.json` for both languages.

## Editing the OpenAPI Spec

The API reference is auto-generated from `api-reference/openapi.json`. This file
is large (~11k lines). When editing:

- Follow OpenAPI 3.0 specification strictly.
- The `postman-to-openapi` dependency can convert Postman collections:
  ```bash
  npx postman-to-openapi <collection.json> -f api-reference/openapi.json
  ```
- After changes, verify with `mintlify dev` that API reference pages render
  correctly.

## Formatting Rules (from .editorconfig)

| File type          | Indent | Size | Trailing whitespace |
|--------------------|--------|------|---------------------|
| `*.md`, `*.mdx`   | spaces | 2    | preserved           |
| `*.json`, `*.yaml` | spaces | 2    | trimmed             |
| `*.js`, `*.ts`    | spaces | 2    | trimmed             |
| All files          | UTF-8, LF line endings, final newline required |

## Common Gotchas

- **404 on local dev:** Ensure you run `mintlify dev` from the directory
  containing `docs.json` (the repo root).
- **New page not appearing:** You must add it to `docs.json` navigation — MDX
  files alone are not enough.
- **Broken API reference:** Validate `openapi.json` against the OpenAPI 3.0
  schema before committing large changes.
- **Portuguese out of sync:** Always check that `pt/` mirrors `en/` after any
  structural change.

This codebase will outlive you. Every shortcut you take becomes
someone else's burden. Every hack compounds into technical debt
that slows the whole team down.

You are not just writing code. You are shaping the future of this
project. The patterns you establish will be copied. The corners
you cut will be cut again.

Fight entropy. Leave the codebase better than you found it.
