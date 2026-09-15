# azbox-cli: translations as files

Package: `azbox-cli` on npm. Node 18+. No dependencies. It only downloads; there is no
push.

```bash
npm install --save-dev azbox-cli      # or run once with npx azbox-cli
```

## Credentials

```bash
export AZBOX_PROJECT_ID=your-project-id
export AZBOX_TOKEN=azb_live_...
```

Never put the key in `azbox.json` (the CLI refuses to start if it finds `token`,
`apiKey` or `api_key` there).

## azbox.json

```json
{
  "projectId": "your-project-id",
  "languages": ["EN", "ES"],
  "out": "locales/{language}.{ext}",
  "format": "json"
}
```

Flags override environment variables, which override the file.

Language codes are the project's (`EN-US`, `ES`, `PT-PT`). From 0.1.1 the case does not
matter: `-l es` requests `ES` and still writes `es.json`. With 0.1.0, use upper case.

## Commands

```bash
azbox pull -l ES                                     # locales/ES.json
azbox pull -l EN -l ES
azbox pull -l es -f arb -o "lib/l10n/app_{language}.{ext}"
azbox pull -l ES --since 2026-09-01                  # only keys changed since then
azbox pull -l ES --dry-run
azbox status                                         # counts per language, writes nothing
```

Options: `-p/--project`, `-t/--token`, `-l/--language` (repeatable), `-o/--out`,
`-f/--format` (`json` | `arb`), `--flat`, `--since`, `--dry-run`, `--base-url`.

- `json` nests dotted keys (`home.title` → `{ "home": { "title": ... } }`), which is
  what i18next expects. Use `--flat` to keep dots. If keys collide (`home` and
  `home.title`), the CLI writes flat and says so.
- `arb` is always flat, with `@@locale` set.
- Keys without a translation are skipped, never written as empty strings.
- Keys are sorted, so diffs only show real changes.
- Exit codes: `0` ok, `1` the API failed for a language, `2` bad usage.

## CI (GitHub Actions)

```yaml
- run: npx azbox-cli pull -l EN -l ES
  env:
    AZBOX_PROJECT_ID: ${{ vars.AZBOX_PROJECT_ID }}
    AZBOX_TOKEN: ${{ secrets.AZBOX_TOKEN }}
```

## Wiring the files

Keep the project's existing library and point it at the pulled files. With i18next:

```ts
import i18n from 'i18next'
import en from './locales/EN.json'
import es from './locales/ES.json'

i18n.init({ resources: { en: { translation: en }, es: { translation: es } }, lng: 'en', fallbackLng: 'en' })
```

File names use the language code as configured in the project; map them to the locale
names the library expects.
