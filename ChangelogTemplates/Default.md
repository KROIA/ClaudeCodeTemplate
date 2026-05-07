# Changelog Template

This template defines the changelog format for this project. Each released version gets its own file under `changelogs/` (e.g. `changelogs/1.4.0.md`), and `CHANGELOG.md` next to that folder links to each version file.

Entries are organized in **two stages**:

1. **Top-level sections** answer *"what area does this touch?"* (Security, Performance, API, …).
2. **Inline tag badges** on each entry answer *"what kind of change is it?"* (`![feature]`, `![bugfix]`, `![breaking]`, …).

One entry, one home. No cross-listing.

Each section is rendered as a 2-column markdown table. The Tag column is **right-aligned** so all badges hug the description column for tight visual scanning. Tag badges use shields.io with reference-style image links — colors are defined once at the bottom of each file and reused everywhere.

Change descriptions support two forms:
- **One-liner** — plain text in the cell. Use this when the change is fully captured in a single line.
- **Collapsible** — `<details><summary>short headline</summary>... details ...</details>`. Use this when the change has root-cause, fix approach, commit hashes, or related tests worth recording without cluttering the default-collapsed view.

---

## Top-level sections (closed set — pick what applies, omit empty ones)

- **Security** — vulnerabilities, auth, secret handling, hardening
- **Performance** — speedups, memory/allocation, query optimizations
- **API** — public API surface (endpoints, library exports, schemas)
- **CLI** — command-line interface changes
- **UI / UX** — user-facing interface changes
- **Build & Dependencies** — build system, packaging, dependency upgrades/pins
- **Documentation** — docs, examples, READMEs
- **Internal** — refactors, test infrastructure, no user-visible behavior change

> Order in the changelog: Security → Performance → API → CLI → UI/UX → Build & Dependencies → Documentation → Internal.

## Tag vocabulary (closed set)

| Tag | Color | Hex | Use for |
|---|---|---|---|
| `![feature]` | emerald | `#10b981` | new capability |
| `![improvement]` | sky | `#0ea5e9` | non-bug, non-feature enhancement |
| `![bugfix]` | rose | `#e11d48` | defect fixed |
| `![breaking]` | slate | `#1e293b` | incompatible behavior/API/config/CLI change |
| `![removal]` | orange | `#f97316` | actually deleted (follow-through after deprecation) |
| `![deprecation]` | amber | `#eab308` | marked for future removal, still works |
| `![hardening]` | violet | `#8b5cf6` | defense-in-depth, no known exploit |

> Multiple badges per row are allowed but should be rare. `![breaking] ![removal]` is fine. `![bugfix] ![improvement]` means the author hasn't decided which it is — pick one.

---

## File template — copy for each new version

```markdown
# <version> — <YYYY-MM-DD>

<Optional one-line release summary in customer-friendly language.>

## Security

| Tag | Change |
|---:|---|
| ![bugfix] | <One-liner change description> (<CVE / issue ID if applicable>) |
| ![hardening] | <details><summary><Short headline></summary><br><Root cause / fix approach / commit refs / related tests></details> |
| ![breaking] | <One-liner change description> |

## Performance

| Tag | Change |
|---:|---|
| ![improvement] | <One-liner change description> |
| ![bugfix] | <details><summary><Short headline></summary><br><Details></details> |

## API

| Tag | Change |
|---:|---|
| ![feature] | <One-liner change description> |
| ![breaking] | <details><summary><Short headline></summary><br><Details — migration steps, deprecation date if applicable></details> |
| ![deprecation] | <One-liner change description — include sunset date if known> |

## CLI

| Tag | Change |
|---:|---|
| ![feature] | <One-liner change description> |
| ![bugfix] | <One-liner change description> |

## UI / UX

| Tag | Change |
|---:|---|
| ![improvement] | <One-liner change description> |

## Build & Dependencies

| Tag | Change |
|---:|---|
| ![improvement] | <One-liner change description> |
| ![removal] | <One-liner change description> |

## Documentation

| Tag | Change |
|---:|---|
| ![improvement] | <One-liner change description> |

## Internal

| Tag | Change |
|---:|---|
| ![improvement] | <One-liner change description> |

<!-- Badge definitions — edit colors here once, applied everywhere. Style: flat-square. -->
[feature]:     https://img.shields.io/badge/feature-10b981?style=flat-square
[improvement]: https://img.shields.io/badge/improvement-0ea5e9?style=flat-square
[bugfix]:      https://img.shields.io/badge/bugfix-e11d48?style=flat-square
[breaking]:    https://img.shields.io/badge/breaking-1e293b?style=flat-square
[removal]:     https://img.shields.io/badge/removal-f97316?style=flat-square
[deprecation]: https://img.shields.io/badge/deprecation-eab308?style=flat-square
[hardening]:   https://img.shields.io/badge/hardening-8b5cf6?style=flat-square
```

---

## Authoring rules

1. **Customer-friendly language** when possible — the changelog is read by users, not just contributors. Save jargon for commit messages.
2. **Omit empty sections.** Don't ship a `## Performance` heading with no entries — drop the whole section (and its table) if there's nothing to report.
3. **One entry per change.** If a single change spans two areas (rare), put it in the higher-priority section per the order above.
4. **Closed vocabularies.** New top-level sections or new tags require a deliberate decision (record it in `DECISIONS.md`) and a new badge definition at the bottom of the file. Don't invent ad-hoc tags.
5. **Hotfix items still appear here** under the appropriate section/tag for the version they ship in.
6. **Link issues/CVEs** inline where applicable: `(CVE-2026-1234)`, `(#412)`.
7. **Badge definitions** live at the bottom of every per-version file. Copy the block as-is so each file is self-contained.
8. **Collapsible details:** use `<details>` for entries with root cause / fix approach / commit refs worth recording. Skip the wrapper for one-liners. Inside a `<details>` body inside a table cell:
   - Whole block must stay on one source line — use `<br>` for line breaks.
   - Use `<ul><li>...</li></ul>` for bullet lists (raw `- item` doesn't render in table cells).
   - Backticks, `**bold**`, `*italic*`, links work normally.
   - Add `<details open>` to expand a row by default (e.g. for breaking changes).
9. **Rendering note:** GitHub renders these tables with light gray borders by default. That's accepted — fighting GitHub's CSS isn't portable. VS Code preview shows the same.

---

## Minimal example

```markdown
# 1.4.0 — 2026-05-07

Security and performance release. One breaking API change — see below.

## Security

| Tag | Change |
|---:|---|
| ![bugfix] | <details><summary>Session token leaked in refresh flow on logout</summary><br>Refresh response cached the previous session's token in process memory; logout did not clear the cache.<br><br>**Fix:** zero the cache slot in the logout path; added regression test.<br>**CVE:** CVE-2026-1234</details> |
| ![hardening] | Rotate signing keys on every deploy |

## Performance

| Tag | Change |
|---:|---|
| ![bugfix] | Removed N+1 query in `/users` endpoint (#412) |
| ![improvement] | Cache compiled regex patterns in request router |

## API

| Tag | Change |
|---:|---|
| ![feature] | New `/v2/search` endpoint with cursor pagination |
| ![breaking] | <details open><summary>`/v1/users` now returns 410 Gone</summary><br>Migrate to `/v2/users`. Response shape is identical except `created_at` is now ISO-8601 instead of epoch seconds.<br><br>**Sunset of all `/v1/*`:** 2026-12-01.</details> |
| ![deprecation] | All `/v1/*` endpoints sunset 2026-12-01 |

## Build & Dependencies

| Tag | Change |
|---:|---|
| ![improvement] | Bump `openssl` to 3.2.1 |
| ![removal] | Drop Node 18 support |

[feature]:     https://img.shields.io/badge/feature-10b981?style=flat-square
[improvement]: https://img.shields.io/badge/improvement-0ea5e9?style=flat-square
[bugfix]:      https://img.shields.io/badge/bugfix-e11d48?style=flat-square
[breaking]:    https://img.shields.io/badge/breaking-1e293b?style=flat-square
[removal]:     https://img.shields.io/badge/removal-f97316?style=flat-square
[deprecation]: https://img.shields.io/badge/deprecation-eab308?style=flat-square
[hardening]:   https://img.shields.io/badge/hardening-8b5cf6?style=flat-square
```
