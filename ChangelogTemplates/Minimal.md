# Changelog Template — Minimal

Plain markdown only. No tables, no shields.io badges, no HTML. Just headings and bullet lists. Each released version gets its own file under `changelogs/` (e.g. `changelogs/1.4.0.md`), and `CHANGELOG.md` next to that folder links to each version file.

---

## Section vocabulary (closed set)

Use these section headings, in this order. **Omit any section that has no entries** — do not ship empty headings.

1. **Features** — new capabilities
2. **Improvements** — non-bug, non-feature enhancements (perf, ergonomics, polish)
3. **Bugfixes** — defects fixed
4. **Breaking changes** — incompatible behavior, API, config, or CLI changes
5. **Deprecations** — marked for future removal, still works
6. **Removals** — actually deleted (follow-through after deprecation)
7. **Security** — vulnerabilities, hardening, secret handling
8. **Documentation** — docs, examples, READMEs
9. **Internal** — refactors, test infra, build/dependency changes with no user-visible behavior

---

## Authoring rules

1. **Customer-friendly language** when possible — the changelog is read by users, not just contributors. Save jargon for commit messages.
2. **Omit empty sections.** If a release has no bugfixes, do not include the `## Bugfixes` heading. The renderer must drop the section entirely (heading + body).
3. **One entry per change.** If a single change spans two areas, place it in the higher-priority section per the order above.
4. **One bullet per entry.** Keep entries to one line when possible. For changes that need more context, use a sub-bullet for *why* or *how*, not a wall of prose:
   ```markdown
   - Removed N+1 query in `/users` endpoint
     - Was triggered on every paginated response; now batched in a single join.
   ```
5. **Closed vocabulary.** Adding a new section requires a deliberate decision (record it in `DECISIONS.md`).
6. **Hotfix entries** still appear here under the appropriate section for the version they ship in.
7. **Link issues/CVEs** inline where applicable: `(CVE-2026-1234)`, `(#412)`.
8. **Breaking changes** must include a one-line migration hint as a sub-bullet.

---

## File template — copy for each new version

Below is the **maximal** form. **Drop any section with no entries** before committing.

```markdown
# <version> — <YYYY-MM-DD>

<Optional one-line release summary in customer-friendly language.>

## Features

- <Short description of new capability> (#<issue>)

## Improvements

- <Short description>

## Bugfixes

- <Short description> (#<issue>)

## Breaking changes

- <Short description>
  - Migration: <one-line hint>

## Deprecations

- <Short description — include sunset date if known>

## Removals

- <Short description>

## Security

- <Short description> (CVE-<id> if applicable)

## Documentation

- <Short description>

## Internal

- <Short description>
```

---

## Minimal example (after dropping empty sections)

```markdown
# 1.4.0 — 2026-05-07

Security and performance release. One breaking API change.

## Features

- New `/v2/search` endpoint with cursor pagination

## Improvements

- Cache compiled regex patterns in request router

## Bugfixes

- Removed N+1 query in `/users` endpoint (#412)
  - Was triggered on every paginated response; now batched in a single join.

## Breaking changes

- `/v1/users` now returns 410 Gone
  - Migration: switch to `/v2/users`. Response shape is identical except `created_at` is ISO-8601 instead of epoch seconds.

## Deprecations

- All `/v1/*` endpoints sunset 2026-12-01

## Security

- Rotate signing keys on every deploy
- Fix session token leak in refresh-on-logout flow (CVE-2026-1234)
```

Note: this release had no entries for **Removals**, **Documentation**, or **Internal** — those headings are absent, not empty.

---

## PM behavior summary

When the PM writes a release entry using this template:

- Build the entries per section, then **filter out any section with zero entries** before writing the file.
- Never emit an empty `## <Section>` heading followed by a blank line.
- Never emit "None" or "N/A" placeholders — the absence of the section is the signal.
- Preserve section order from the vocabulary list above; do not reorder by frequency.
