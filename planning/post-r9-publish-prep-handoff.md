# Post-R9 Publish Prep Handoff Notes

> **目的**：讓下一個 Director session 從 publish-ready baseline 接續，處理 push / npm publish / post-publish tutorial cleanup。
> **建立日期**：2026-05-01
> **建立時 HEAD**：`170df25 chore: prepare npm package metadata`
> **Git 狀態**：`main` ahead of `origin/main` by 2 commits；worktree clean before this handoff file is added.
> **接續者**：Codex Director session。

---

## 目前完成狀態

Post-R9 docs cleanup 已完成並提交：

```text
288ab1c docs: clean up post-R9 stale references
```

Proposal closeout metadata 已完成並提交：

```text
faf0705 docs: close implemented proposal statuses
```

這代表：

- P009-P015 皆為 `implemented`。
- P005 / P006 / P007 umbrella proposals 維持 `superseded`。
- `CHANGELOG.md` 中 R7 / R8 對應段的 proposal 標籤已同步為 `implemented`。

Publish prep 已完成並提交：

```text
170df25 chore: prepare npm package metadata
```

這代表：

- npm package name 已改為 `dflow-sdd-ddd`。
- `dflow` 已確認是 npm 上既有 package name，不能使用。
- CLI bin 同時提供：
  - `dflow`
  - `dflow-sdd-ddd`
- Public default init entry 已改為：

```bash
npx dflow-sdd-ddd init
```

- Global install path：

```bash
npm install -g dflow-sdd-ddd
dflow init
```

- 新增 MIT `LICENSE`。
- `package.json` 已補 keywords / repository / bugs / homepage / `publishConfig.access=public`。

---

## 已驗證

已通過：

```bash
env PATH=/home/will/.nvm/versions/node/v22.22.1/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin /home/will/.nvm/versions/node/v22.22.1/bin/node /home/will/.nvm/versions/node/v22.22.1/lib/node_modules/npm/bin/npm-cli.js test
```

結果：

```text
Smoke test passed
```

已通過：

```bash
env npm_config_cache=/tmp/npm-cache-dflow PATH=/home/will/.nvm/versions/node/v22.22.1/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin /home/will/.nvm/versions/node/v22.22.1/bin/node /home/will/.nvm/versions/node/v22.22.1/lib/node_modules/npm/bin/npm-cli.js publish --dry-run
```

結果摘要：

```text
+ dflow-sdd-ddd@0.1.0
total files: 40
package size: 51.0 kB
public access (dry-run)
```

已通過：

```bash
diff -qr sdd-ddd-core-skill/scaffolding templates/core/scaffolding
diff -qr sdd-ddd-core-skill/templates templates/core/templates
diff -qr sdd-ddd-webforms-skill/scaffolding templates/webforms/scaffolding
diff -qr sdd-ddd-webforms-skill/templates templates/webforms/templates
git diff --check
```

---

## 下一步建議

1. Commit this handoff file if keeping it.
2. Push current branch:

```bash
git push
```

3. Confirm npm auth:

```bash
npm whoami
```

Use the same Node/npm path pattern as above if needed.

4. Publish:

```bash
npm publish
```

If sandbox blocks npm cache, use:

```bash
env npm_config_cache=/tmp/npm-cache-dflow PATH=/home/will/.nvm/versions/node/v22.22.1/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin /home/will/.nvm/versions/node/v22.22.1/bin/node /home/will/.nvm/versions/node/v22.22.1/lib/node_modules/npm/bin/npm-cli.js publish
```

5. After publish, verify real install path in a temp dir:

```bash
npx dflow-sdd-ddd --version
npx dflow-sdd-ddd --help
```

Then run an init smoke against the published package in a temp project.

6. Post-publish tutorial pass:
   - Verify tutorial `npx dflow-sdd-ddd init` lines match the actually published package.
   - Prefer the newer scenario docs under:
     - `tutorial/01-greenfield-core/`
     - `tutorial/02-brownfield-webforms/`
   - Older monolithic files (`TUTORIAL_*_tw*.md`) were mechanically updated for package name but may still contain older single-file spec shape in some examples; treat them as candidate cleanup after publish, not a blocker for npm release.

---

## Director session recommendation

換手 Director session 是合理的。

理由：

- Current milestone is clean: proposals implemented, package dry-run passed.
- Next steps involve external state: git push, npm auth, npm publish, then post-publish tutorial verification.
- A fresh Director can start from this handoff without carrying R9 implementation context.
