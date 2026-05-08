# Version Control Template — Default

This template defines version-control conventions for the project. The Project Manager defers to this file for commit-message format, branch naming, and repository-artifact policy (see §6 of the PM spec). Anything this file leaves unspecified falls back to the PM spec's inline defaults.

Git-flavored throughout. SVN notes are inline where behavior differs.

---

## 1. Commit message format

### Subject line

```
<short summary>
```

- **No type prefix on the subject.** The subject is plain text. Type prefixes (`+`, `~`, `-`) appear only on body entries (see below).
- **Summary** — imperative mood, lowercase start, no trailing period, ≤ 60 chars.
- **One coherent change per commit.** If a change touches unrelated areas, split the commit. The body breakdown shows the type mix; the subject names the overall change.
- The body's type set is:
  - `+` new feature / new capability / new file
  - `~` change to existing behavior or content (refactor, bugfix, docs update, tweak, improvement)
  - `-` removal / deletion

Examples:
```
add cursor pagination to /v2/search
extract retry policy into RetryConfig
drop Node 18 support
tighten commit-type set in default version-control template
```

### Body (optional)

- Blank line after subject.
- **Keep it compact.** No prose paragraphs explaining the change — the bullets and their sub-bullets carry the meaning. Wrap at 72 chars.
- For grouped changes, mirror the changelog category split (Security / Performance / API / …) as plain-text sub-headings (no prefix on the heading line itself, e.g. `API:`).
- **Every entry — top-level and sub-bullet alike — starts with a type prefix** from the set `+`, `~`, `-`. One prefix per entry, single character, no colon.
- **The type prefix IS the list character.** Never stack a markdown dash in front of it. Write `+ add foo`, never `- + add foo`, and never use a bare `-` as a generic list bullet — `-` always means *removal*.
- Sub-bullets do **not** inherit the parent's type. Each sub-bullet carries its own prefix reflecting what that line is — usually `+` (added detail/clarification) or `~` (rationale/note about a change), and `-` only when the sub-point is itself a removal.
- Issue references and breaking-change declarations are body entries, not a separate footer. Prefix them like any other entry — typically `~`.
- **Never reference an issue without describing what was actually done for it.** A bare `Closes #412` is not enough — the reader should not have to open the tracker to learn what changed. Either:
  - inline the issue ID into the entry that resolves it: `~ retry on 503 instead of 500 (closes #412)`, or
  - use the dedicated form `~ #412: <short description of the fix>` — the issue ID first, then a colon, then a short description.

Example (correct):

```
+ add cursor pagination to /v2/search
  + cursor encoding is opaque base64; clients must not parse it
~ extract retry policy into RetryConfig
  ~ shared between /v2/search and /v2/users for consistency
- remove /v1/search
~ #412: retry on 503 instead of 500 in the search request layer
```

Wrong:

```
- + add cursor pagination to /v2/search    ← double prefix on top level
+ add cursor pagination to /v2/search
  - cursor encoding is opaque base64       ← bare `-` used as list bullet,
                                              but the sub-point is not a removal
```

### What does NOT belong in commit messages

- Prose paragraphs (the "why" is captured in entry wording and sub-bullets).
- Implementation walkthroughs (use the changelog's `<details>` block).
- Author tool attribution unless the user has explicitly opted in.
- "WIP", "fix typo in last commit", "address review comments" — squash these before merging.

### Full example

```
overhaul the /v2/search endpoint and retire its v1 sibling

API:
+ add cursor pagination to /v2/search
  + cursor encoding is opaque base64; clients must not parse it
~ extract retry policy into RetryConfig
  ~ shared between /v2/search and /v2/users for consistency
- remove /v1/search

Build & Dependencies:
~ bump openssl to 3.2.1

~ Breaking-Change: /v1/search now returns 410 Gone; clients must migrate to /v2/search
```

Note: subject is plain text (no prefix), every entry — top-level and sub-bullet — starts with one of `+ ~ -` as its list character, body is grouped by changelog category, and breaking-change / issue refs are just body lines with their own prefix (no separate footer).

---

## 2. Branching

### Naming convention

```
<type>/<short-kebab-summary>
```

Types:
- `feat/` — new feature
- `fix/` — bugfix
- `chore/` — tooling, deps, infra
- `refactor/` — internal restructuring, no behavior change
- `docs/` — documentation only
- `release/` — release prep (see §4)
- `hotfix/` — urgent production fix (bypasses normal flow)

Examples: `feat/cursor-pagination`, `fix/users-n-plus-one`, `hotfix/session-token-leak`.

Rules:
- Lowercase, kebab-case after the slash.
- Max 50 chars total.
- No author names in branch names — owners belong in PR metadata, not the branch.
- One branch per task/issue. Reuse is fine for follow-up commits but not for unrelated work.

### Default branch

- `main` is the integration branch. It is always releasable.
- Direct commits to `main` are forbidden once the project has more than one contributor. Until then, the PM may commit to `main` if commit permission is granted.
- Long-lived feature branches: avoid. If a feature needs > 2 weeks, split it.

### Merging

- **Squash-merge** by default — keeps `main` history linear and one commit per logical change.
- **Merge commit** when preserving multi-commit history matters (e.g. a refactor with reviewable intermediate steps).
- **Rebase-merge** is acceptable but not preferred — it makes `git bisect` harder when intermediate commits are broken.
- Never merge with failing CI.

---

## 3. Repository artifacts

Decide per-project, default opinions below:

| Artifact | Default | Rationale |
|---|---|---|
| `CLAUDE.md` | **commit** | Onboards future sessions and human contributors. |
| `.claude/PROJECT_STATUS.md`, `TASKS.md`, `ISSUES.md` | **commit** | Shared work state. |
| `.claude/ProjectManager/` | **commit** | Durable PM context (decisions, glossary, templates). |
| `.claude/settings.json` | **commit** | Shared harness config. |
| `.claude/settings.local.json` | **gitignore** | Per-machine overrides; may contain local paths. |
| `.env`, `credentials.*`, `*.key`, `*.pem` | **gitignore** | Secrets. Never commit. |
| Build outputs (`dist/`, `build/`, `target/`) | **gitignore** | Reproducible from source. |
| Lockfiles (`package-lock.json`, `Cargo.lock`, `poetry.lock`) | **commit** | Reproducible builds. Exception: library projects may omit. |

The PM must ask before changing this policy.

---

## 4. Release flow

The release workflow (§7 of the PM spec) interacts with version control:

1. PM creates `release/<version>` branch.
2. Final tasks land on the release branch.
3. Changelog file for the version is completed (per the changelog template).
4. Tag the release commit: `git tag -a v<version> -m "Release <version>"`. Annotated tags only — lightweight tags lose author/date metadata.
5. Merge `release/<version>` into `main` (merge commit, no squash — preserve the release-prep history).
6. Push `main` and the tag (only if push permission is granted).
7. Open the next version: bump the version file, commit `open v<next> development`.

### Hotfix flow

1. Branch from the latest release tag: `git checkout -b hotfix/<summary> v<version>`.
2. Fix + minimal test.
3. Bump patch version.
4. Tag the hotfix release.
5. Merge into `main` (and any other affected long-lived branches).
6. The hotfix entry appears in the changelog under the patch version.

---

## 5. SVN notes

If the project uses SVN instead of git:

- Branches and tags live under `branches/` and `tags/` in the repo layout. Same naming convention as §2 applies.
- Squash-merge has no native equivalent — use `svn merge` with `--reintegrate` for feature branches and accept that history is preserved.
- `svn commit` requires the same permission tier as `git commit` per the PM spec §6.
- No equivalent to `.gitignore` — use `svn:ignore` properties on directories.

---

## 6. PM behavior summary

The Project Manager:

- **Always** prefixes body entries (not subjects) with the type chars from §1.
- **Never** commits without commit permission (PM spec §6).
- **Never** pushes without push permission (separate tier, PM spec §6).
- **Always** runs the pre-commit ritual from PM spec §6 (changelog update, finished-task move, version check, secret scan).
- **Only** the PM commits — subagents never commit (PM spec §6).
- **Confirms** before destructive operations: force-push, branch deletion, history rewrite, tag deletion.
- **Refuses** to bypass hooks (`--no-verify`, `--no-gpg-sign`) unless the user explicitly opts in for a specific commit.
