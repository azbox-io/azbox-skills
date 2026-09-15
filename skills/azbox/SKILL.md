---
name: azbox
description: Integrate AZbox translations into an app or website and work with its translation keys. Use when the user mentions AZbox, wants to localize or internationalize a Flutter, Node.js, React, Next.js or PHP project with AZbox, needs to pull translations into JSON or ARB files, add or look up translation keys, set up over-the-air translation updates, or wire AZbox into CI.
license: MIT
metadata:
  author: azbox-io
  version: "1.0.0"
  homepage: https://azbox.io/docs/
---

# AZbox

AZbox is a translation management platform. Translation keys ("keywords") live in an
AZbox **project**; apps read them through the AZbox API, an SDK, or files pulled at build
time. Everything below was checked against the published packages and the live API. Do
not invent methods, packages or endpoints that are not listed here.

## Facts to get right first

- **Keys are not created from code.** The API is read-only for keys. New keys are added
  in the AZbox dashboard or by importing a file (ARB, i18next JSON, Apple .xcstrings,
  Android strings.xml, XLSX, CSV, YAML, Laravel PHP arrays). When the user needs new
  strings, add them to the project's source file (for example `app_en.arb` or
  `locales/en.json`) and tell the user to import that file in the dashboard.
- **Credentials never go in the repository.** Use environment variables or CI secrets.
  Prefer a key from **Settings → API keys** in the dashboard (starts with `azb_live_`),
  tied to one project and revocable. The older account key from Settings also works
  but opens every project and cannot be revoked.
- **The project ID** is shown in the dashboard. Language codes are the ones configured
  in the project, in upper case (`EN-US`, `ES`, `PT-PT`); get them from the API before
  assuming. The API compares them exactly: `es` returns every key with no translation.
- In API responses the key name is `data.keyword`. The `id` field is an internal
  document id and means nothing to the app.

## Pick the integration

1. **Flutter** → the `azbox` package on pub.dev, with over-the-air updates.
   Read [references/flutter.md](references/flutter.md).
2. **Any stack that can read files at build time** (React, Next.js, Vue, Node, native
   iOS/Android, Laravel) → pull translations into files with `azbox-cli` and keep using
   the project's i18n library (i18next, next-intl, vue-i18n…).
   Read [references/cli-and-ci.md](references/cli-and-ci.md).
3. **Runtime fetch from a server** (Node, Go, Python…) → call the REST API directly.
   Read [references/rest-api.md](references/rest-api.md).
4. **A PHP website translated page by page** → `azbox/azbox-php` on Packagist.
   Read [references/php-websites.md](references/php-websites.md).

If the project already has an i18n setup, keep it and feed it from AZbox. Do not replace
a working i18n library just to use AZbox.

## Workflow for localizing existing code

1. Find hard-coded user-facing strings. Skip logs, analytics event names, test data and
   anything not shown to users.
2. Before inventing a key, check whether one already exists:
   - If the `azbox` MCP tools are available, use `azbox_search_keywords` (search by the
     visible text with `search_in: "translation"`, or by a key prefix).
   - Otherwise look in the project's pulled locale files.
3. Name new keys by screen and purpose, dot-separated and stable: `checkout.pay_button`,
   `settings.delete_account.confirm`. Never use the English text as the key.
4. Replace the string in code with the lookup for the chosen integration.
5. Add new keys with their source-language text to the source file, and tell the user
   which file to import into AZbox. List the new keys in your summary.
6. Keep placeholders as the i18n library expects them and never concatenate translated
   fragments; one key per full sentence.

## MCP server

If the user wants the agent to read the project's keys directly, the read-only MCP
server is `azbox-mcp-server` on npm. For Claude Code:

```bash
claude mcp add azbox --env AZBOX_TOKEN=azb_live_… -- npx -y azbox-mcp-server
```

Tools: `azbox_list_projects`, `azbox_get_project`, `azbox_list_categories`,
`azbox_list_keywords`, `azbox_search_keywords`, `azbox_find_untranslated`. It cannot
create keys or translate. Setup for other clients: https://azbox.io/docs/api/mcp/

## Common mistakes

- Installing `azbox_localization` or `azbox-localization` for Flutter: the package is
  `azbox`.
- Calling `.tr()` in Flutter: the method is `.translate()`.
- Sending `?token=`: the query parameter is `api_key` (or the `x-api-key` header).
- Sending a lower-case or unknown language code to the REST API: it answers `200` with
  every key and no `translation`, which looks like "nothing is translated". Use the
  project's codes in upper case (`azbox-cli` 0.1.1+ and the MCP server do this for you).
- Treating a `404` with `No keywords found` as an error: the project has no keys yet.
- Writing empty strings for untranslated keys: the API omits `translation` when a key
  has no text in that language. Skip it or fall back to the source language.
- Committing `azbox.json` with a key in it: `azbox-cli` refuses to run if it finds one.

Full documentation: https://azbox.io/docs/
