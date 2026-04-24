# Git Integration with SDD/DDD

This reference defines the minimal Git coupling Dflow requires for SDD, and
what checks should happen at key branch transitions. Dflow is intentionally
agnostic about which Git *branching strategy* your project adopts (Git Flow,
GitHub Flow, trunk-based, single-`main`, etc.) — it only prescribes the
feature-branch-per-feature convention that SDD traceability depends on.

> If your project adopts Git Flow specifically, see the optional
> `scaffolding/Git-principles-gitflow.md` template (provided by PROPOSAL-010)
> for Git-Flow-specific conventions.

## Branch-to-Workflow Mapping

Dflow only requires that every SDD feature / bug-fix lives on its own branch
that links back to a spec. The *base branch* that feature branches are cut
from (e.g. `main`, `develop`, `trunk`) is a project-level decision that Dflow
does not mandate.

```
main (or your project's base branch)
  │
  ├─ feature/{SPEC-ID}-{slug}       ← Full SDD workflow
  │   Gate: spec must exist BEFORE first commit
  │
  └─ bugfix/{BUG-ID}-{slug}         ← Lightweight SDD workflow
      Gate: at minimum a lightweight spec
```

> If your project adopts Git Flow / GitHub Flow / trunk-based, the choice of
> base branch (and whether you use `develop`, `release/*`, or a single `main`)
> is up to the project. Dflow does not decide this.

## Branch Naming Convention

```
feature/{SPEC-ID}-{slug}
  Examples:
    feature/EXP-001-jpy-currency-support
    feature/HR-003-leave-approval-workflow
    feature/SHARED-002-audit-logging

bugfix/{BUG-ID}-{slug}
  Examples:
    bugfix/BUG-042-rounding-inconsistency
    bugfix/BUG-051-exchange-rate-cache
```

The SPEC-ID / BUG-ID prefix links the branch to its spec document. This is
the traceability chain:

```
Git Branch → Spec Document → Domain Concepts → Code Implementation → Tests
```

> **Slug language** — the branch slug follows the language the developer /
> AI discuss the feature in (Chinese, English, etc.). Exact rules for slug
> conventions are maintained in the feature-directory layer (see
> PROPOSAL-009 for the authoritative slug language guidance once
> implemented).

## Feature Branch per Feature (Required)

This is the one non-negotiable Git coupling Dflow enforces:

- **Every SDD feature must have its own feature branch.** Branch name must
  match its SPEC-ID so that `git log`, PR titles, and spec documents can be
  traced back to one another.
- **Every bug-fix (SDD-tracked) must have its own bugfix branch** following
  the same pattern.
- Commits that span multiple specs (accidentally or deliberately) are
  discouraged; if you notice work on a new spec emerging mid-branch, stop
  and create a new branch off the correct base.

This requirement is independent of the branching strategy — whether you
branch off `develop`, `main`, or something else, the feature-per-branch
convention stays.

## Use `git mv` for Directory / File Renames

When you rename a directory or file that is tracked in Dflow (feature
directories, spec files, domain knowledge files), **always use `git mv`
instead of a plain `mv` + `git add`**. This preserves git's rename
tracking, which in turn preserves `git log --follow` history, `git blame`
chains, and PR diff quality.

Common cases:

```bash
# Renaming a feature's slug or SPEC-ID
git mv specs/features/active/EXP-001-old-slug \
       specs/features/active/EXP-001-new-slug

# Moving a completed feature to the archive
git mv specs/features/active/EXP-001-jpy-currency-support \
       specs/features/completed/EXP-001-jpy-currency-support

# Renaming a reference file (like this one)
git mv sdd-ddd-webforms-skill/references/git-flow-integration.md \
       sdd-ddd-webforms-skill/references/git-integration.md
```

> The full "Directory Moves Must Use `git mv`" rule set (including edge
> cases, commit message conventions for renames, and CI verification
> hooks) is developed in PROPOSAL-009. This section establishes the
> principle; 009 expands the detailed rules.

## Gate Checks by Branch Type

### feature/ branch — Before Creating

AI should verify:
- [ ] Spec exists in `specs/features/active/{SPEC-ID}-*.md`
- [ ] Spec has status: `draft` or `in-progress`
- [ ] Bounded Context is identified
- [ ] At least one Given/When/Then scenario is defined
- [ ] Domain concepts are identified (even if not yet in models.md)

If any are missing, guide the developer through creating them BEFORE the branch.

```
"Before we create the branch, let's set up the spec.
I see this touches the Expense context. Let me help you draft the
spec — it should only take a few minutes and it'll keep us focused
during implementation."
```

### feature/ branch — Before Merging (Pre-PR / Pre-Integration)

AI should verify:
- [ ] Spec status updated to `completed`
- [ ] All new business logic is in `src/Domain/` (not Code-Behind only)
- [ ] New terms added to `glossary.md`
- [ ] `rules.md` and `models.md` updated if applicable
- [ ] Tech debt recorded for any shortcuts taken
- [ ] Domain layer code has no System.Web references

### bugfix/ branch — Before Creating

AI should verify:
- [ ] Lightweight spec exists or is created during this session
- [ ] Root cause is documented
- [ ] Fix approach is noted

### bugfix/ branch — Before Merging (Pre-PR / Pre-Integration)

- [ ] Spec has the fix documented
- [ ] Tech debt recorded if the underlying issue is broader (record in
      `specs/migration/tech-debt.md` if the bug reveals a systemic issue)
- [ ] If business logic was touched, evaluate Domain extraction

> The exact merge strategy (merge commit, squash, rebase, fast-forward)
> is a project-level decision and sits outside Dflow's scope. See your
> project's Git-principles document (e.g. the scaffolding template from
> PROPOSAL-010) for integration commit conventions.

## Commit Message Convention

Tie commits to specs:

```
[SPEC-ID] Short description

Examples:
[EXP-001] Add JPY currency support to Money value object
[EXP-001] Extract exchange rate logic to Domain service
[BUG-042] Fix rounding inconsistency, extract to Money.Round()
```

## Daily Development Flow

```
1. Developer: "I'm starting work on [feature/bug]"
   AI: Check for spec → Guide through spec creation if missing
       → Suggest branch name based on spec ID

2. Developer creates branch
   AI: Confirm branch name matches convention
       → Remind: "Business logic goes in src/Domain/"

3. During development
   AI: Answer questions referencing specs/domain/ knowledge
       → Flag if business logic is going into Code-Behind
       → Suggest Domain layer patterns when appropriate
       → Help maintain thin Code-Behind

4. Before PR/merge
   AI: Run through merge checklist
       → Help update specs, glossary, tech-debt
       → Suggest moving completed spec to completed/

5. After merge
   AI: Confirm all artifacts are updated
       → Suggest next items from backlog/ if relevant
```

## Integration with CI/CD (Future Enhancement)

These checks could eventually be automated in CI:
- Verify no `using System.Web` in `src/Domain/` directory
- Verify spec file exists for any branch with feature/ or bugfix/ prefix
- Verify glossary.md and rules.md were updated when Domain/ files change
- Lint commit messages for spec ID format

For now, the AI handles these checks conversationally during development.
