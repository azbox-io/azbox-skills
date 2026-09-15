# REST API

Base: `https://api.azbox.io/v1`. Read-only for keys.

## Authentication

- Header `x-api-key: azb_live_...` (keys from Settings → API keys), or
- query parameter `?api_key=...` (also accepts the older account key).

`?token=` is not accepted. Keep keys on the server where possible: a query parameter ends
up in access logs and proxies.

## Keywords of a project in one language

```
GET /v1/projects/{projectId}/keywords?language=ES
GET /v1/projects/{projectId}/keywords?language=ES&afterUpdatedAtStr=2026-09-01T00:00:00.000Z
```

Response:

```json
[
  { "id": "8Kd0pQ2m...", "data": { "keyword": "home.title", "translation": "Bienvenido" } },
  { "id": "Xy12...",     "data": { "keyword": "home.cta" } }
]
```

- The key is `data.keyword`. `id` is internal.
- `translation` is **absent** when the key has no text in that language.
- `context`, `comment` and `reference` appear only when set.
- `language` must be one of the project's codes, **in upper case** (`ES`, `EN-US`,
  `PT-PT`). A lower-case or unknown code is not an error: the API answers `200` with
  every key and no `translation`. Read the codes from `GET /v1/projects/{projectId}`.
- `404` with `No keywords found` means the project has no keys; `404` with
  `Language not found` means the `language` parameter is missing.
- `afterUpdatedAtStr` returns only keys updated after that date, for incremental sync.

## Projects

```
GET /v1/projects/                 # projects the credential can see, with their languages
GET /v1/projects/{projectId}      # one project: name, type, languages
```

A key tied to a project only sees that project.

## Node: azbox-node

Use `azbox-node` 0.2.0 or newer. Check `package.json`: 0.1.0 and 0.1.1 never worked
against the API, so if you find one of them, upgrade instead of debugging it.

```ts
import { AzboxClient } from 'azbox-node'

const client = new AzboxClient({
  apiKey: process.env.AZBOX_API_KEY!,
  projectId: process.env.AZBOX_PROJECT_ID!,
  language: 'ES',
})
const es = await client.getTranslations() // { "home.title": "Bienvenido", … }
const changed = await client.getTranslations({ afterUpdatedAt: lastSync })
```

It works with `import` and `require`, and in React Native. Errors are `AzboxError` with
`status`. A project with no keywords returns an empty result, not an error.

## Minimal example without a package

```ts
const res = await fetch(
  `https://api.azbox.io/v1/projects/${process.env.AZBOX_PROJECT_ID}/keywords?language=ES`,
  { headers: { 'x-api-key': process.env.AZBOX_TOKEN! } }
)
const rows = res.status === 404 ? [] : await res.json()
const es = Object.fromEntries(
  rows.filter((r: any) => r.data.translation).map((r: any) => [r.data.keyword, r.data.translation])
)
```

Cache the result; do not call the API on every request.
