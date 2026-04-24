# Git Integration with SDD/DDD — ASP.NET Core

Same minimal Git coupling as the WebForms version. Key difference: gate
checks validate Clean Architecture layer rules instead of WebForms-specific
checks. Dflow remains agnostic about the project's Git *branching strategy*
(Git Flow, GitHub Flow, trunk-based, etc.) — it only prescribes the
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
  ├─ feature/{SPEC-ID}-{slug}       ← Full SDD + DDD workflow
  │   Gate: spec + Aggregate design before first commit
  │
  └─ bugfix/{BUG-ID}-{slug}         ← Lightweight SDD workflow
      Gate: lightweight spec with layer identification
```

> If your project adopts Git Flow / GitHub Flow / trunk-based, the choice of
> base branch (and whether you use `develop`, `release/*`, or a single `main`)
> is up to the project. Dflow does not decide this.

## Branch Naming Convention

```
feature/{SPEC-ID}-{slug}
bugfix/{BUG-ID}-{slug}
```

Examples:

```
feature/EXP-001-expense-submission-aggregate
feature/HR-003-leave-approval-workflow
bugfix/BUG-042-money-rounding
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
git mv specs/features/active/EXP-001-expense-submission \
       specs/features/completed/EXP-001-expense-submission

# Renaming a reference file (like this one)
git mv sdd-ddd-core-skill/references/git-flow-integration.md \
       sdd-ddd-core-skill/references/git-integration.md
```

> The full "Directory Moves Must Use `git mv`" rule set (including edge
> cases, commit message conventions for renames, and CI verification
> hooks) is developed in PROPOSAL-009. This section establishes the
> principle; 009 expands the detailed rules.

## Gate Checks by Branch Type

### feature/ — Before Creating
- [ ] Spec exists with at least draft status
- [ ] Bounded Context identified
- [ ] Aggregate design documented (which Aggregate, what invariants)
- [ ] Domain Events identified
- [ ] At least one Given/When/Then scenario

### feature/ — Before Merging (Pre-PR / Pre-Integration)
- [ ] Spec status: completed
- [ ] Domain layer: zero external dependencies
- [ ] No business logic outside Domain layer
- [ ] Domain Events documented in events.md
- [ ] Glossary, models, rules updated
- [ ] Domain unit tests pass
- [ ] No ORM/serialization attributes on Domain entities

### bugfix/ — Before Creating
- [ ] Lightweight spec exists or is created during this session
- [ ] Root cause is documented
- [ ] Fix approach is noted (which layer the fix belongs in)

### bugfix/ — Before Merging (Pre-PR / Pre-Integration)
- [ ] Spec has the fix documented
- [ ] Tech debt recorded if the underlying issue is broader (record in
      `specs/architecture/tech-debt.md` if the bug reveals a systemic issue)
- [ ] Fix is in the correct architectural layer (Domain / Application /
      Infrastructure / Presentation) — no business logic leaked outside
      Domain

> The exact merge strategy (merge commit, squash, rebase, fast-forward)
> is a project-level decision and sits outside Dflow's scope. See your
> project's Git-principles document (e.g. the scaffolding template from
> PROPOSAL-010) for integration commit conventions.

## Commit Message Convention

```
[SPEC-ID] Short description

[EXP-001] Define ExpenseReport Aggregate with submission invariants
[EXP-001] Add CreateExpenseReport command and handler
[EXP-001] Implement EF Core configuration for ExpenseReport
[BUG-042] Fix rounding in Money value object
```

## Daily Development Flow

```
1. Start: Check spec → Create if missing → Design Aggregate → Create branch
2. Implement: Domain first → Application → Infrastructure → Presentation
3. Test: Domain unit tests → Application tests → Integration tests
4. Review: Layer compliance → Spec compliance → Architecture score
5. Before PR/merge: Run through merge checklist → Update docs
6. After merge: Confirm artifacts updated → Move spec to completed/
```

## Integration with CI/CD (Future Enhancement)

These checks could eventually be automated in CI:
- Verify Domain project has zero external NuGet dependencies (beyond allowed list)
- Verify no ORM/serialization attributes on Domain entities
- Verify spec file exists for any branch with feature/ or bugfix/ prefix
- Verify glossary.md / rules.md / events.md updated when Domain/ files change
- Lint commit messages for spec ID format

For now, the AI handles these checks conversationally during development.
