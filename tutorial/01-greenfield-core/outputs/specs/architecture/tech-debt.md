<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Architecture Tech Debt

> Architecture debt backlog discovered during SDD/DDD work on ExpenseTracker.

## Debt Items

| Item | Layer | Decision / debt | Impact | Follow-up | Status |
|---|---|---|---|---|---|
| Unicode character counting strategy under i18n | Domain / Presentation | Reported 2026-05-04 from BUG-001. Reject reason bug exposed that user-facing length limits do not yet have a shared character counting strategy. | Scope: `ApprovalReason` VO and any future user-facing length limits. Risk: UI counters, API payloads, and Domain validation can disagree for emoji, composed characters, fullwidth forms, or malformed Unicode. | Proposed approach: evaluate grapheme cluster vs codepoint vs UTF-16 unit semantics; standardize shared helpers; use `Intl.Segmenter` in Presentation where available and evaluate ICU library support for Domain-side counting. Priority: medium. Blocked-by: none. | open |

## Follow-up Notes

- related-feature: SPEC-20260428-001 (completed 2026-05-07)
- BUG-001 only fixes reject reason truncation and Domain malformed-input handling. A broader i18n character-counting policy should be handled as a separate architecture review, not expanded inside the T2 bug-fix.
