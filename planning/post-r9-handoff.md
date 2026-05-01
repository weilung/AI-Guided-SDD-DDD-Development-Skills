# Post-R9 Handoff Notes — 給下一個 Director session 接續用

> **目的**：讓下一個主線 Director session 從 cold start 接續，不必重建 R9 / P014+P015 脈絡。
> **建立日期**：2026-05-01
> **建立時 R9 HEAD**：`e351cb9 fix: clarify WebForms finish-feature tech debt path`
> **R9 遠端狀態**：`main` 已 push，與 `origin/main` 對齊；worktree clean。
> **接續者**：Codex Director session。

---

## 第一句話（給新 session 的開場）

> 「我接續 Post-R9 階段，作為 Codex director。先讀 `AGENTS.md § Director vs Worker session 模式` + `planning/post-r9-handoff.md` + 最新 commit log 建立 baseline。R9 / P014+P015 已完成並 push；下一步是小範圍 docs cleanup，不是新 proposal。」

---

## 目前距離目標完成到哪裡

### 已完成的主目標

R9 / PROPOSAL-014 + PROPOSAL-015 shared-cut implementation 已完成並 push。

這代表：

- `npx dflow init` 已成為 V1 project bootstrap 入口。
- Dflow runtime baseline 已從 root `specs/` 改為 `dflow/specs/`。
- legacy root `specs/` 採 V1 clean cut：warn only，不 dual-read、不搬移、不改寫。
- init CLI 會問必填 project prose language，並寫入 `dflow/specs/shared/_conventions.md`。
- Skill source、package templates、tutorial outputs、root docs、proposals、CHANGELOG 已完成 cascade / closeout。
- P014 / P015 狀態已改 `implemented`。

### 目前不是問題的事

- R9 已可宣告 done。
- 不需要再開 R9 cross-model review，除非 user 想做額外保險。
- `.claude/settings.local.json` 是 ignored local file，不再污染 worktree。
- `.codex` 是 ignored local artifact。

### 下一個自然目標

做一個小範圍 **post-R9 docs cleanup pass**，收掉 reviewer 指出的 M1 / M2 docs drift：

- `AGENTS.md` / `CLAUDE.md` repo tree 和同步規則仍提到舊檔名。
- `README.md` 舊段落仍提到過時檔案與手動 copy `templates/CLAUDE.md`。

這不是新 proposal，也不是 runtime bug。

---

## 已完成 commit baseline

近期重要 commits：

```text
e351cb9 fix: clarify WebForms finish-feature tech debt path
c16c04b fix: address R9 post-review drift
350846d chore: ignore local Claude settings
6796a2e W2c: close root docs and proposals
07bf10d fix: correct WebForms tutorial tech-debt path
9607c85 W2b: cascade tutorial outputs to dflow namespace
59e0eb2 W2a: cascade skill paths to dflow namespace
c782737 W1b: npm package 雛形實作
6380bac Post-W1a handoff + 主線改 Codex director
03fd637 W1a: Init contract specification
5e66363 approve PROPOSAL-014 + PROPOSAL-015
```

Current remote status at handoff creation:

```text
## main...origin/main
```

---

## R9 work summary

### W1a

- Added `planning/init-contract-spec.md`.
- Defined authoritative init contract for W1b.
- Captured P014 / P015 shared-cut decisions:
  - CLI entry
  - `dflow/specs/` namespace
  - preflight and write safety
  - required prose language
  - package template behavior

### W1b

- Added npm CLI skeleton:
  - `package.json`
  - `bin/dflow.js`
  - `lib/init.js`
  - `test/smoke.mjs`
  - `templates/core/**`
  - `templates/webforms/**`
- Supported:
  - `dflow init`
  - `--help`
  - `--version`
  - Node `>=22.0.0`
  - no third-party dependencies
- Smoke test covers Core happy path, WebForms path, legacy `specs/` warning, rerun abort, prose-language persistence, CLAUDE extraction, and post-review shared-link regression.

### W2a

- Cascaded skill source and package templates:
  - runtime paths now use `dflow/specs/`
  - `/dflow:init-project` removed as runtime slash command
  - init flow references repositioned as CLI internal flow / manual fallback
  - prose-generating flows read `dflow/specs/shared/_conventions.md`
  - package templates synced with skill source

### W2b

- Cascaded tutorial:
  - tutorial dialogues use `npx dflow init`
  - outputs moved to `outputs/dflow/specs/**`
  - WebForms brownfield init section added
  - WebForms tech debt path corrected to `dflow/specs/migration/tech-debt.md`

### W2c

- Updated root docs:
  - README adoption section is npm CLI first
  - `TEMPLATE-COVERAGE.md` uses `dflow/specs/...`
  - `TEMPLATE-LANGUAGE-GLOSSARY.md` adds prose-language terminology
  - CHANGELOG R9 section added
- Marked P014 / P015 `implemented`.

### Post-review fixes

- Fixed generated shared scaffolding relative links:
  - files live under `dflow/specs/shared/`
  - links to `domain/`, `architecture/`, `migration/` now use `../...`
- Added smoke assertions to prevent unqualified sibling links from returning.
- Updated planning docs that are still useful as current context:
  - `planning/init-contract-spec.md`
  - `planning/project-review-brief.md`
  - `planning/public-distribution-backlog.md`
  - `planning/post-w1a-handoff.md`
- Fixed WebForms `finish-feature-flow.md` runtime drift:
  - changed ambiguous `tech-debt.md / migration/tech-debt.md`
  - now says `migration/tech-debt.md / models.md / glossary.md`

---

## Verification already performed

Latest meaningful checks before handoff:

- `npm test` passed after post-review fixes.
- `git diff --check` passed before relevant commits.
- Package templates were byte-identical to skill source:
  - `sdd-ddd-core-skill/scaffolding` vs `templates/core/scaffolding`
  - `sdd-ddd-core-skill/templates` vs `templates/core/templates`
  - `sdd-ddd-webforms-skill/scaffolding` vs `templates/webforms/scaffolding`
  - `sdd-ddd-webforms-skill/templates` vs `templates/webforms/templates`
- No remaining `tech-debt.md / migration/tech-debt.md` ambiguous runtime wording.
- Worktree clean and pushed.

Useful command for npm test in this environment:

```bash
env PATH=/home/will/.nvm/versions/node/v22.22.1/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin /home/will/.nvm/versions/node/v22.22.1/bin/node /home/will/.nvm/versions/node/v22.22.1/lib/node_modules/npm/bin/npm-cli.js test
```

Notes:

- `rg` was not available in the observed environment; use `grep`.
- Git commands may need sandbox escalation in Codex CLI.

---

## Known remaining items

### Runtime

No known R9 runtime blockers.

### Docs cleanup candidates

Reviewer left these as non-blocking docs drift:

1. `AGENTS.md` and `CLAUDE.md`
   - repo structure tree still mentions `git-flow-integration.md`
   - repo structure tree still mentions `feature-spec.md`
   - tree omits newer root directories / files such as:
     - `bin/`
     - `lib/`
     - `templates/`
     - `test/`
     - `tutorial/`
     - `planning/`
     - `reviews/`
   - same-file-sync rule still names `git-flow-integration`

2. `README.md`
   - older “WebForms Skill 檔案說明” / “Core Skill 檔案說明” sections still list stale file names:
     - `git-flow-integration.md`
     - `feature-spec.md`
   - “如何使用” still says to manually copy `templates/CLAUDE.md` into root `CLAUDE.md`, which contradicts the newer `npx dflow init` adoption section.

3. Proposal / coverage nits, safe to defer:
   - P015 proposal text mentions non-existent `bug-fix-flow.md`; actual bug-fix entry is covered by `modify-existing-flow.md`.
   - Q numbering differs between CLI contract and old manual init flow text.
   - Smoke coverage can expand in next iteration:
     - invalid custom BCP-47 retry
     - both Git principles selected confirmation
     - existing `_conventions.md` skip / prose-language warning
     - preview rejection `N`
     - edition detection mismatch warning
     - empty `dflow/specs/` continue path

---

## Recommended next task

Do **post-R9 docs cleanup** as one small commit.

Scope:

- `AGENTS.md`
- `CLAUDE.md`
- `README.md`
- optionally P015 proposal one-line nit if desired

Do not touch:

- historical review reports
- historical CHANGELOG R7/R8 entries
- old handoff bodies except adding explicit superseded notes if truly helpful
- runtime skill files unless a real drift is found

Suggested steps:

1. Read current `README.md`, `AGENTS.md`, `CLAUDE.md`.
2. Update repo structure tree to match current root.
3. Replace `git-flow-integration.md` with `git-integration.md`.
4. Replace `feature-spec.md` references with current spec templates:
   - `_index.md`
   - `phase-spec.md`
   - `lightweight-spec.md`
5. Update README usage section to be CLI first:
   - `npx dflow init`
   - then `/dflow:new-feature` or `/dflow:modify-existing`
6. Run grep checks:
   - `grep -n "git-flow-integration\\|feature-spec\\|templates/CLAUDE.md" README.md AGENTS.md CLAUDE.md`
7. Commit:
   - `docs: refresh repo guides after R9`

---

## Prompt for next Director session

```text
我接續 Post-R9 階段，作為 Codex director。請先讀：

- AGENTS.md § Director vs Worker session 模式
- planning/post-r9-handoff.md
- 最新 git log / git status

目前 R9 / P014+P015 已完成並 push。不要重做 R9。下一步是小範圍 post-R9 docs cleanup：

- 更新 AGENTS.md / CLAUDE.md 的 repo structure 與共同檔名規則
- 更新 README 舊的 Skill 檔案說明與「如何使用」段
- 移除或修正 stale references：git-flow-integration.md、feature-spec.md、手動 copy templates/CLAUDE.md

請先確認 worktree clean，再直接實作、驗證、commit。
```
