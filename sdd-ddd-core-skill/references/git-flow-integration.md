# Git Flow Integration with SDD/DDD — ASP.NET Core

Same branch strategy as WebForms version. Key difference: gate checks now validate
Clean Architecture layer rules instead of WebForms-specific checks.

## Branch-to-Workflow Mapping

```
main (production)
  │
  └─ release/{version}          ← AI Check: all specs archived, context-map current
      │
      └─ develop                ← AI Check: specs reviewed, tech-debt updated
          │
          ├─ feature/{SPEC-ID}-{name}    ← Full SDD + DDD workflow
          │   Gate: spec + Aggregate design before first commit
          │
          ├─ bugfix/{BUG-ID}-{name}      ← Lightweight SDD workflow
          │   Gate: lightweight spec with layer identification
          │
          └─ hotfix/{HOT-ID}-{name}      ← Minimal: document after fix
              Gate: retroactive spec within 24 hours
```

## Branch Naming Convention

```
feature/{CONTEXT-ID}-{short-description}
bugfix/BUG-{NUMBER}-{short-description}
hotfix/HOT-{NUMBER}-{short-description}
```

## Gate Checks

### feature/ — Before Creating
- [ ] Spec exists with at least draft status
- [ ] Bounded Context identified
- [ ] Aggregate design documented (which Aggregate, what invariants)
- [ ] Domain Events identified
- [ ] At least one Given/When/Then scenario

### feature/ — Before Merging to develop
- [ ] Spec status: completed
- [ ] Domain layer: zero external dependencies
- [ ] No business logic outside Domain layer
- [ ] Domain Events documented in events.md
- [ ] Glossary, models, rules updated
- [ ] Domain unit tests pass
- [ ] No ORM/serialization attributes on Domain entities

### Commit Message Convention

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
5. Merge: Update docs → Move spec to completed/
```
