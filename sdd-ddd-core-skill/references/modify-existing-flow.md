# Modify Existing Feature Workflow — ASP.NET Core

Step-by-step guide for changing or fixing existing functionality.

## Step 1: Assess the Change

1. **What needs to change?** Get specifics.
2. **Why?** Bug fix, new requirement, or behavior change?
3. **Which layer is affected?**
   - Domain invariant broken → Domain fix + full spec
   - Application orchestration issue → Application fix + lightweight spec
   - Infrastructure bug (DB, external service) → Infrastructure fix + lightweight spec
   - API contract change → Presentation fix + spec if behavior changes

## Step 2: Check Documentation

- Spec in `specs/features/completed/`?
- Domain model in `specs/domain/{context}/models.md`?
- Business rules in `rules.md`?
- Domain events in `events.md`?

If no documentation exists, capture current behavior BEFORE changing:

```markdown
## Current Behavior
Given [current state]
When [action]
Then [current result]

## Changed Behavior (this PR)
Given [state]
When [action]
Then [NEW result]

## Reason for Change
[Why]
```

## Step 3: Assess DDD Impact

### Is the Aggregate design still correct?

Changes that require Aggregate redesign:
- New invariant that spans objects currently in different Aggregates
- Performance issue from too-large Aggregate
- Concurrency conflict from too-large Aggregate
- Business rule that now crosses Aggregate boundary

```
"This change affects [invariant]. Does the current Aggregate
boundary still make sense, or do we need to split/merge?"
```

### Do we need new Domain Events?

If the behavior change means other parts of the system need to react differently:
- Add new events
- Modify event payloads (careful: backward compatibility)
- Add new event handlers

### Are Value Objects still valid?

If constraints change:
- Update Value Object validation
- Check all usages of the Value Object

## Step 4: Implement

Follow the layer order: Domain → Application → Infrastructure → Presentation.

Even for bug fixes, verify:
- [ ] Fix is in the correct layer
- [ ] Aggregate invariants still hold
- [ ] Domain Events still fire correctly
- [ ] Tests updated to cover the fix
- [ ] No business logic leaked to wrong layer

## Step 5: Update Documentation

1. Update or create spec
2. Update `models.md` if Aggregate structure changed
3. Update `rules.md` if business rules changed
4. Update `events.md` if events changed
5. Update `glossary.md` if terms changed
6. Record tech debt if shortcuts were taken
