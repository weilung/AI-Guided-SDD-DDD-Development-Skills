# Git Flow Integration with SDD/DDD

This reference defines how Git Flow branches map to SDD workflow stages,
and what checks should happen at each transition.

## Branch-to-Workflow Mapping

```
main (production)
  │
  └─ release/{version}          ← AI Check: all specs archived to completed/
      │
      └─ develop                ← AI Check: specs reviewed, tech-debt updated
          │
          ├─ feature/{SPEC-ID}-{name}    ← Full SDD workflow
          │   Gate: spec must exist BEFORE first commit
          │
          ├─ bugfix/{BUG-ID}-{name}      ← Lightweight SDD workflow
          │   Gate: at minimum a lightweight spec
          │
          └─ hotfix/{HOT-ID}-{name}      ← Minimal: document after fix
              Gate: create spec retroactively within 24 hours
```

## Branch Naming Convention

```
feature/{CONTEXT-ID}-{short-description}
  Examples:
    feature/EXP-001-jpy-currency-support
    feature/HR-003-leave-approval-workflow
    feature/SHARED-002-audit-logging

bugfix/{BUG-ID}-{short-description}
  Examples:
    bugfix/BUG-042-rounding-inconsistency
    bugfix/BUG-051-exchange-rate-cache

hotfix/{HOT-ID}-{short-description}
  Examples:
    hotfix/HOT-007-production-calc-error
```

The ID prefix links the branch to its spec document. This is the traceability chain:
```
Git Branch → Spec Document → Domain Concepts → Code Implementation → Tests
```

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

### feature/ branch — Before Merging to develop

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

### bugfix/ branch — Before Merging to develop

- [ ] Spec has the fix documented
- [ ] Tech debt recorded if underlying issue is broader
- [ ] If business logic was touched, evaluate Domain extraction

### hotfix/ branch — Expedited Process

Hotfixes are urgent. Don't block the developer:
1. Help with the fix immediately
2. Create a retroactive spec after the fix is merged
3. Record in tech-debt.md if this hotfix reveals a systemic issue

```
"Let's fix the production issue first. After it's deployed, I'll help
you create a spec documenting what happened and what we changed."
```

### release/ branch — Release Checklist

Before creating a release branch from develop:
- [ ] All specs in `active/` have status `completed` or are moved to `backlog/`
- [ ] `specs/migration/tech-debt.md` is reviewed and prioritized
- [ ] No orphan branches without specs
- [ ] Domain `glossary.md` is consistent with code

## Commit Message Convention

Tie commits to specs:

```
[SPEC-ID] Short description

Examples:
[EXP-001] Add JPY currency support to Money value object
[EXP-001] Extract exchange rate logic to Domain service
[BUG-042] Fix rounding inconsistency, extract to Money.Round()
[HOT-007] Emergency fix for production calculation error
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
