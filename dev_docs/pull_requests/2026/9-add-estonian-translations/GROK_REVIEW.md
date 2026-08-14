# PR #9: Add Estonian country name translations

**Author**: @timujinne (Tymofii Shapovalov)  
**Reviewer**: Grok 4.6  
**Status**: Approved, merged, post-merge maintainer follow-up applied  
**Branch**: `i18n/estonian-country-names` → `main`  
**Head reviewed**: `029e10f`  
**Date**: 2026-08-14

---

## What the PR does

Adds Estonian (`et`) as the 16th `Translations` locale:

- `priv/data/locales/et.json` — 250 ISO 3166-1 alpha-2 codes (the existing 249 plus `AN`)
- `"et"` registered in `@supported_locales` (sorted)
- README, CLAUDE.md, and the 2026-03-28 roadmap updated to 16 locales
- Coverage test vs English, plus spot-checks (`EE`/`FI`/`GB`)
- Follow-up: `AN` (Netherlands Antilles) added to `et.json` and `ru.json`

CI `quality` on the PR head: **pass**.

`CHANGELOG.md` and `@version` were intentionally left to the maintainer.

---

## Independent verification (not assumed from the PR text)

- Diffed `et.json` against CLDR 48 `et/territories.json` for the codes this library ships. Shared codes match CLDR (including `Venemaa`, `Holland`, `Svaasimaa`, `Kongo DV`, `Bouvet’ saar`). CLDR has **no** `AN` — that entry is correctly hand-written.
- `et.json` key set equals the 250 country YAML files. No extra keys, no missing country codes, no duplicate keys, NFC, no BOM/NBSP/ZW chars.
- `Hollandi Antillid` matches the et.wikipedia article title.
- `Translations` is the only reader of these JSON files; `get_name/2` and `get_all_names/1` are nil-safe, so extra/missing keys in a single locale cannot crash callers.
- `languages.json` already has `et`; `locales.json` already has the Estonian locale entry. No language/locale wiring was missing.

---

## Findings

### IMPROVEMENT — HIGH — CHANGELOG and version left to the maintainer

Correct for a contributor PR. Required before Hex. Added `## 1.2.0 - 2026-08-14` and bumped `@version` after merge. Historical `1.0.0` "15 languages" line left alone — it was true then.

### IMPROVEMENT — MEDIUM — `AN` only in `et` and `ru`

The PR turns a uniform gap into a two-locale special case. `priv/data/countries/AN.yaml` already lists usable names for `en`, `de`, `fr`, `es`, `ja`, `nl`. Those six files now get `AN` from that source of truth.

The remaining locales (`ar`, `it`, `ko`, `pl`, `pt`, `sv`, `uk`, `zh`) are still missing `AN`. Completing them is a separate data task (no CLDR, and no names in `AN.yaml`).

### IMPROVEMENT — MEDIUM — Coverage test was Estonian-only and its comment was stale

`translated?/1` (nil / empty / whitespace) is the right predicate — Tim proved the old truthiness check was blind to `""`. Two remaining holes:

- The comment still said locale files skip `AN`, but `et.json` now includes it.
- `assert length(supported_locales()) == 16` does not prove `"et"` is the 16th locale.
- No assertion that `AN` actually resolves in Estonian or Russian.

Tests now assert `"et"` is in the list, lock the `AN` names we added, and check every supported locale covers every English-translated country.

### NITPICK — No trailing newline in `et.json`

True of every locale file in this repo. Left as-is so `et.json` matches the corpus.

### Left as-is (recorded, not bugs)

These are CLDR-verbatim, not misspellings:

| Code | Form | Why it stays |
|---|---|---|
| `CD` | `Kongo DV` | Only abbreviation in the 16-locale corpus; CLDR current form |
| `BV` | `Bouvet’ saar` (U+2019) | CLDR; rest of corpus mostly U+0027 |
| `SZ` | `Svaasimaa` | Prevailing Estonian (et.wikipedia redirects `Eswatini` here) |
| `RU` | `Venemaa` | CLDR short form; existing locales lean ISO long names (`Russian Federation`). Deliberate dropdown register, not a data error |
| `NL` / `AN` / `BQ` | `Holland` / `Hollandi Antillid` / `Kariibi Madalmaad` | CLDR; `BQ` is the outlier vs et.wikipedia `Kariibi Holland` |

`fr.json` still uniquely carries `CP` (Clipperton). Pre-existing, nil-safe, not introduced here.

---

## Verdict

**Merge.** Focused, sourced, tested, CI green. Post-merge maintainer work: changelog, 1.2.0 bump, `AN` for the six YAML-backed locales, tighter tests.
