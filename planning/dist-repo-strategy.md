# Dist Repo Strategy

> **Purpose**: define how to split the current development repo from a future public distribution repo.
> **Date**: 2026-05-01
> **Status**: draft decision note; no repo split has been executed yet.

---

## Current State

This repo has become the **development repo** for Dflow:

- It contains the public runtime package (`bin/`, `lib/`, `templates/`, `package.json`).
- It contains the AI-facing skill source (`sdd-ddd-core-skill/`, `sdd-ddd-webforms-skill/`).
- It contains human-readable tutorial material (`tutorial/`).
- It also contains development-only material (`planning/`, `archive/`, `proposals/`, `reviews/`, handoff notes).

The npm package `dflow-sdd-ddd@0.1.0` has already shipped from this repo. That does **not** automatically make this repo a clean public distribution repo; npm only ships the files selected by `package.json#files`.

---

## Recommendation

Keep this repo as the **dev repo**.

Create a separate **dist repo** after the current tutorial closeout and archive cleanup, using a deliberate export list rather than a full clone.

Reasoning:

- The dev repo should preserve planning history, handoffs, proposals, review artifacts, and working notes.
- The dist repo should be small enough for users and AI agents to read without accidentally treating historical proposals as active behavior.
- The npm package artifact and the public GitHub repo serve different audiences:
  - npm package: executable CLI + packaged init templates.
  - dist repo: public source, skill files, tutorial, installation guidance, release notes.

---

## Proposed Dist Repo Contents

### Include

| Path | Reason |
|---|---|
| `README.md` | Public overview and adoption instructions. Needs a public-facing pass before first dist push. |
| `LICENSE` | Required for public distribution. |
| `CHANGELOG.md` | Public release history. May later be trimmed or split from dev changelog if it becomes noisy. |
| `package.json` | npm package metadata and scripts. |
| `bin/` | CLI entrypoint. |
| `lib/` | CLI implementation. |
| `templates/` | npm packaged template source. |
| `test/` | Public smoke tests for contributors and release verification. |
| `sdd-ddd-core-skill/` | AI-facing Core skill source. |
| `sdd-ddd-webforms-skill/` | AI-facing WebForms skill source. |
| `tutorial/` | Human-readable tutorial scenarios and outputs. |
| `TEMPLATE-COVERAGE.md` | Template/source coverage map useful for contributors. |
| `TEMPLATE-LANGUAGE-GLOSSARY.md` | Human-readable glossary for canonical English template terms. |

### Exclude

| Path | Reason |
|---|---|
| `planning/` | Dev-only handoffs, strategy notes, and internal sequencing. |
| `archive/` | Historical proposals/reviews; too much context noise for public readers and AI agents. |
| `proposals/` | Active design workspace for dev repo only. |
| `reviews/` | Active review workspace for dev repo only. |
| `AGENTS.md` | Codex collaboration rules for this dev repo, not public product documentation. |
| `CLAUDE.md` | Claude Code collaboration rules for this dev repo, not public product documentation. |
| `.claude/`, `.codex`, `.agents` | Local/tool state. |
| `EVALUATION-RESULT.md` | Historical/internal evaluation output; archive-only unless later rewritten as public case study. |

---

## Dist Repo Shape

Suggested initial layout:

```text
dflow-sdd-ddd/
├── README.md
├── CHANGELOG.md
├── LICENSE
├── package.json
├── bin/
├── lib/
├── templates/
├── test/
├── sdd-ddd-core-skill/
├── sdd-ddd-webforms-skill/
├── tutorial/
├── TEMPLATE-COVERAGE.md
└── TEMPLATE-LANGUAGE-GLOSSARY.md
```

The dist repo should not contain proposal/review/planning directories at first publish.

If public contribution starts, add public-specific files later:

- `CONTRIBUTING.md`
- `RELEASE.md`
- `SECURITY.md` if needed
- `.github/workflows/` for CI once release automation exists

---

## Sync Policy

### Short Term: Manual Export

For the first dist repo creation, use a manual export from a clean dev repo commit.

Recommended process:

1. Confirm dev repo is clean.
2. Run smoke tests in dev repo.
3. Export only the include list into a fresh dist repo checkout.
4. Run `npm test` in dist repo.
5. Run `npm pack --dry-run` in dist repo and compare package contents with the dev repo package.
6. Commit the dist repo with a message like `chore: publish dflow-sdd-ddd 0.1.0 source`.
7. Push dist repo.

### Medium Term: Scripted Export

After the first manual export proves the shape, add a dev-only script such as:

```text
scripts/export-dist.sh
```

The script should:

- Copy the approved include list.
- Refuse to run with a dirty dev repo unless explicitly overridden.
- Delete files in the dist worktree that are no longer part of the include list.
- Run `npm test`.
- Run `npm pack --dry-run`.

Do not add release automation before the manual dist shape is proven.

---

## Release Ownership

Recommended split:

- **Dev repo** remains the source of truth for design, planning, proposals, reviews, and implementation.
- **Dist repo** is a clean public projection of selected dev repo files.
- **npm publish** can remain from the dev repo initially, because `package.json#files` already protects the package contents.

Later, if the dist repo becomes the public source of truth for npm consumers, we can move `npm publish` to the dist repo. That should be a separate decision because it changes release ownership and CI assumptions.

---

## Open Decisions

| Decision | Recommendation | Rationale |
|---|---|---|
| Dist repo name | `dflow-sdd-ddd` | Matches npm package and avoids the occupied unscoped `dflow` npm name. |
| Dev repo rename | Do not rename yet | Current repo is already stable and pushed; rename adds low-value churn before public dist exists. |
| Include skill source in dist | Yes | npm init alone seeds baseline docs; the AI-facing workflows still need skill source for `/dflow:*` guidance. |
| Include tutorial outputs | Yes | They are the main human review/onboarding artifact. |
| Include planning notes | No | They are useful to maintainers but noisy and misleading for public users. |
| Publish npm from dist | Not initially | Avoid changing two variables at once. First create clean public source; then revisit release ownership. |

---

## First Dist Repo Checklist

- [ ] Pick / create GitHub repo name.
- [ ] Confirm whether the dist repo is public immediately or private during first validation.
- [ ] Create clean export using the include list above.
- [ ] Run `npm test`.
- [ ] Run `npm pack --dry-run`.
- [ ] Verify `npx dflow-sdd-ddd init` docs in README still match the published package.
- [ ] Push initial dist repo.
- [ ] Add dist repo URL to dev repo README / package metadata if ownership changes.
- [ ] Decide whether future `v0.1.x` tags are mirrored in dist repo or only kept in dev repo until release automation exists.

