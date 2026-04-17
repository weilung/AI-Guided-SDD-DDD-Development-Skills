# Drift Verification — rules.md ↔ behavior.md Consistency Check

Triggered by `/dflow:verify` or `/dflow:verify <bounded-context>`.

## Purpose

The A+C structure (`rules.md` as index + `behavior.md` as scenario content) introduces a drift risk — the two files can fall out of sync. This command provides a mechanical verification safety net that developers can run at key moments: before a PR, before a release, or after a refactor.

## Scope

### This command does (mechanical layer)

Three string-matching checks that AI can perform deterministically:

1. **BR-ID forward check**: Every `BR-*` declared in `rules.md` has a corresponding section in `behavior.md`
2. **Anchor validity**: If `rules.md` links to `behavior.md#section`, that anchor exists
3. **BR-ID reverse check**: Every `BR-*` referenced in `behavior.md` is declared in `rules.md`

### This command does NOT do (semantic layer — explicitly excluded)

Semantic verification (LLM reads the one-line summary in `rules.md` vs the Given/When/Then in `behavior.md` and judges whether they contradict) is **out of scope**. Reasons:
- Mechanical checks already catch most drift (missing IDs, broken links)
- Semantic judgment costs tokens and requires human review of LLM conclusions
- Deferred to Wave D — revisit after 10+ verify runs show the type distribution of actual drift

## Usage

```
/dflow:verify            # Verify all Bounded Contexts
/dflow:verify Expense    # Verify a single BC (recommended default)
```

When verifying all BCs, run each context independently and report per-context results.

## Verification Steps

For each Bounded Context:

### Step 1: Locate files

- Find `specs/domain/{context}/rules.md`
- Find `specs/domain/{context}/behavior.md`
- If either is missing, report and stop for that context:
  ```
  ✗ Expense: rules.md exists but behavior.md is missing
    → Create behavior.md using templates/behavior.md, or run the completion
      flow to populate it from existing completed specs
  ```

### Step 2: Extract BR-IDs from rules.md

Scan `rules.md` for all `BR-*` identifiers. Record each ID and any anchor link to `behavior.md`.

### Step 3: Extract BR-IDs from behavior.md

Scan `behavior.md` for all `BR-*` identifiers referenced in section headings or body text.

### Step 4: Cross-reference

Run the three checks:

| Check | Pass condition | Fail message |
|---|---|---|
| Forward | Every BR-ID in rules.md appears in behavior.md | `✗ BR-NNN declared in rules.md but missing from behavior.md` |
| Anchor | Every `behavior.md#anchor` in rules.md resolves | `✗ BR-NNN links to behavior.md#section but anchor not found` |
| Reverse | Every BR-ID in behavior.md appears in rules.md | `✗ BR-NNN referenced in behavior.md but not declared in rules.md` |

### Step 5: Report

Output format:

```
Verifying {Context} — rules.md ↔ behavior.md consistency

✓ BR-001 → {section heading} exists and references BR-001
✓ BR-002 → {section heading} exists and references BR-002
✗ BR-003 → behavior.md has no section for BR-003
✓ BR-004 → {section heading} exists and references BR-004
✗ BR-010 → referenced in behavior.md but not declared in rules.md

Summary: 3 passed, 2 issues found

Issues:
1. rules.md declares BR-003, but behavior.md has no corresponding scenario
   → Possible cause: rule was implemented but behavior.md wasn't updated in the completion flow
   → Action: check the implementation, then either add the scenario to behavior.md
     or remove BR-003 from rules.md if it was deprecated

2. behavior.md references BR-010, but rules.md doesn't declare it
   → Possible cause: scenario was added directly to behavior.md without updating rules.md
   → Action: add BR-010 to rules.md with a one-line summary, or remove
     the stale reference from behavior.md
```

## When to Run

Recommended trigger points (not enforced — developer's judgment):
- Before creating a PR (`/dflow:verify` as a pre-PR sanity check)
- Before a release branch cut
- After a refactor that touched multiple specs or domain docs
- When onboarding to an unfamiliar Bounded Context (verify before trusting the docs)

## Interaction with Other Commands

- `/dflow:verify` is a **standalone command** — it does not require an active workflow
- It can be run mid-workflow (e.g., during Step 7 implementation to check you haven't drifted)
- If issues are found, the developer decides whether to fix now or defer — the command does not block other workflows
