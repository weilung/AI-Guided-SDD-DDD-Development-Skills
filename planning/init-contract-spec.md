# Dflow Init Contract Specification

This document is the W1a implementation contract for the P014 + P015
shared-cut implementation. W1b should translate this contract into the
first `lib/init.js` implementation without re-deciding the init behavior.

Source references:

| Source | Purpose in this contract |
|---|---|
| `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md` | CLI entry, `dflow/specs/` namespace, V1 clean cut, npm package skeleton |
| `proposals/PROPOSAL-015-templates-prose-language-convention.md` | Required project prose-language question and `_conventions.md` persistence |
| `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md` | Existing init Q&A, file preview, existing-file protection, baseline layout |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | Current WebForms init flow baseline |
| `sdd-ddd-core-skill/references/init-project-flow.md` | Current Core init flow baseline |
| `sdd-ddd-webforms-skill/scaffolding/_conventions.md` | Current WebForms conventions scaffolding |
| `sdd-ddd-core-skill/scaffolding/_conventions.md` | Current Core conventions scaffolding |
| `sdd-ddd-webforms-skill/scaffolding/_overview.md` | Current WebForms overview scaffolding |
| `sdd-ddd-core-skill/scaffolding/_overview.md` | Current Core overview scaffolding |
| `tutorial/01-greenfield-core/outputs/dflow/specs/` | Current tutorial output shape after P014 W2 cascade |

Node.js support reference: the Node.js Release Working Group schedule lists
Node 20 end-of-life on 2026-04-30, Node 22 end-of-life on 2027-04-30, and
Node 24 end-of-life on 2028-04-30:
`https://github.com/nodejs/Release`.

## 1. Scope And Design Constraints

### 1.1 Formal Entry Point

The V1 formal entry point is:

```bash
npx dflow init
```

This is the only formal V1 init entry. `npm create dflow` is not supported
in V1. Global installation may work if the package is installed globally,
but documentation should still treat `npx dflow init` as the default path.

### 1.2 Execution Location

The command is executed from the user's project root. The current working
directory is the project root for all checks, prompts, relative output
paths, and reports.

The CLI must not assume the Dflow skill repository is present in the current
working directory. It must use templates packaged with the npm package.

### 1.3 Write Namespace

All Dflow spec documents created by init are written under:

```text
{cwd}/dflow/specs/
```

`dflow/` is the tool namespace. `specs/` is the SDD document namespace.

The project-root `CLAUDE.md` special case remains outside `dflow/specs/`
because it is a project AI-guide file, not a spec document. It is optional
and must never be required for Dflow to work.

### 1.4 V1 Clean Cut

V1 does not provide:

| Excluded behavior | Reason |
|---|---|
| Dual-read from both `specs/` and `dflow/specs/` | P014 F-02 chooses a V1 clean cut |
| `dflow migrate` | Out of V1 scope |
| Automatic detection and move of legacy root `specs/` | Too risky; existing projects may use `specs/` for other tools |
| Runtime compatibility with `/dflow:init-project` | Init moves from skill slash command to npm CLI |

If `{cwd}/specs/` exists, the CLI only warns. It must not read, rewrite,
copy, move, rename, or infer ownership of that directory.

### 1.5 AI Tool Neutrality

The init result must be usable by Codex, Claude Code, Gemini, GitHub
Copilot, and other AI tools. Init may optionally seed a `CLAUDE.md` snippet
because P010 already has that scaffolding, but this file is not required,
must not be selected by default solely because the user has Claude Code,
and must not be treated as the canonical runtime contract.

The canonical project convention created by init is:

```text
dflow/specs/shared/_conventions.md
```

## 2. Pre-flight Checks

### 2.1 Check Order

Run pre-flight checks before prompting for project information. Checks are
ordered to fail fast on already-initialized projects before doing secondary
environment validation.

| Priority | Check | Condition | Action |
|---:|---|---|---|
| 1 | Existing Dflow init | `{cwd}/dflow/specs/` exists and contains any file or non-empty subtree | Abort with stderr: `Dflow already initialized at dflow/specs/.` Exit non-zero. |
| 2 | Legacy root specs | `{cwd}/specs/` exists and contains any file or non-empty subtree | Warn but continue: `Detected legacy specs/. Dflow V1 will not migrate or modify it; new files will be created under dflow/specs/.` |
| 3 | Writable project root | CLI cannot create and remove a temporary file in `{cwd}` or otherwise prove write access | Abort with stderr: `Project root is not writable: {cwd}`. Exit non-zero. |
| 4 | Node.js version | `process.versions.node` is lower than the configured minimum | Abort with stderr: `Dflow init requires Node.js {minimum}+.` Exit non-zero. |

If `{cwd}/dflow/specs/` exists but is empty, the CLI may continue and use
existing-file protection for individual targets. It should still warn:
`Found empty dflow/specs/. Continuing because no initialized files were found.`

### 2.2 Minimum Node.js Version

The minimum Node.js version for W1b should be:

```text
Node.js 22.0.0+
```

Rationale:

| Candidate | Decision | Reason |
|---|---|---|
| Node 18+ | Reject | Node 18 is already end-of-life and should not be the floor for a new public CLI. |
| Node 20+ | Reject for W1b default | Node 20 reaches end-of-life on 2026-04-30, the current planning date, so it is a poor V1 minimum. |
| Node 22+ | Adopt | Node 22 is still supported through 2027-04-30, covers current LTS users, and avoids requiring the newest Node 24 line. |
| Node 24+ | Defer | Good long-term target, but stricter than necessary for a scaffolding CLI. |

The package `engines.node` should match this contract in W1b.

## 3. Q&A Flow

### 3.1 Primary Prompt Table

The CLI prompts are displayed in English. This is interface language.
The prose-language answer is project data, not CLI localization.

The table keeps the P010 intake concerns but updates them for P014/P015:
edition is explicit, output paths move to `dflow/specs/`, and the old
"document language" concern is replaced by the required project
prose-language convention because V1 keeps canonical English structural
language instead of shipping localized template sets.

| ID | CLI question text | Type | Options | Validation | Default / Required | Stored in |
|---|---|---|---|---|---|---|
| Q1 | `What kind of project is this?` | select | `Greenfield - fresh project adopting Dflow`; `Brownfield - existing codebase adopting Dflow` | Must select exactly one option. | Required. Suggested default: `Greenfield` when no obvious source tree is detected; otherwise no default. | Internal answer `{project-type}`. Used in `dflow/specs/shared/_overview.md` project context prose and final next-step hints. |
| Q2 | `Which Dflow edition should initialize this project?` | select | `ASP.NET Core - Clean Architecture + DDD`; `ASP.NET WebForms - progressive domain extraction` | Must select exactly one option. | Required. No automatic default unless detection is confident. | Internal answer `{edition}` and `{project-type}`. Selects packaged template set and output layout. |
| Q3 | `Confirm the main tech stack details for placeholders.` | text | N/A | Non-empty text unless user enters `unknown`. Maximum 1,000 characters. | Required, but `unknown` is accepted and leaves detailed placeholders unresolved. | Internal answer `{tech-stack-summary}`. Parsed only lightly for edition-specific placeholders such as `{ASP.NET Core version}`, `{EF Core version}`, `{MediatR version}`, `{Test framework}`, `{ASP.NET WebForms version}`, `{.NET Framework version}`, and `{ORM / Data Access}`. |
| Q4 | `Is there migration or legacy context Dflow should note?` | text | N/A | Maximum 1,000 characters. Empty is allowed and stored as `none`. | Optional. Default: `none`. | Internal answer `{migration-context}`. Used in `dflow/specs/shared/_overview.md` and final next-step hints. For WebForms this represents WebForms-to-Core or other modernization plans. For Core this represents origin from prior stack or legacy concerns. |
| Q5 | `Project prose language for generated spec content?` | select | `zh-TW - Traditional Chinese`; `en - English`; `ja-JP - Japanese`; `Custom BCP-47 tag` | Must select exactly one explicit language option. `any`, `skip`, `later`, and empty values are invalid. Custom selection triggers Q5a. | Required. Display `zh-TW` first as the suggested hint per P015 tutorial baseline; do not silently choose a value without a prompt answer. | `dflow/specs/shared/_conventions.md` under `## Prose Language` as `{prose-language}`. |
| Q6 | `Which optional starter files should Dflow seed?` | multiselect | `_overview.md - system overview`; `Git principles - trunk-based`; `Git principles - Git Flow`; `CLAUDE.md snippet / project AI guide` | Zero or more selections. If both Git principles are selected, ask for confirmation once. | Optional. Recommended default for first-time users: `_overview.md` and `Git principles - trunk-based`. `_conventions.md` is not listed because P015 makes it mandatory. | Output selection for optional files under `dflow/specs/shared/` and optional repo-root `CLAUDE.md` handling. |

### 3.2 Conditional Custom Language Prompt

| ID | CLI question text | Type | Options | Validation | Default / Required | Stored in |
|---|---|---|---|---|---|---|
| Q5a | `Enter the explicit BCP-47 language tag for project prose.` | text | N/A | Must match the prose-language validation rule in section 4.1. Reject `any`, `skip`, `later`, blank input, and obvious prose descriptions such as `Traditional Chinese`. | Required only when Q5 is `Custom BCP-47 tag`. No default. | Same as Q5: `{prose-language}` in `dflow/specs/shared/_conventions.md`. |

### 3.3 Edition Effects

| Edition | Template set | Mandatory layout effect |
|---|---|---|
| `core` | Packaged Core templates and Core scaffolding, derived from `sdd-ddd-core-skill/templates/` and `sdd-ddd-core-skill/scaffolding/` | Creates `dflow/specs/domain/context-map.md`, `dflow/specs/architecture/tech-debt.md`, and `dflow/specs/architecture/decisions/README.md`. |
| `webforms` | Packaged WebForms templates and WebForms scaffolding, derived from `sdd-ddd-webforms-skill/templates/` and `sdd-ddd-webforms-skill/scaffolding/` | Creates `dflow/specs/migration/tech-debt.md`. Does not create `domain/context-map.md` at init. |

## 4. Validation Rules

### 4.1 Prose-language Validation

The prose-language value must be explicit. The CLI must reject:

| Invalid value class | Examples |
|---|---|
| Missing value | blank, whitespace-only |
| Non-explicit preference | `any`, `auto`, `same as user`, `detect`, `skip`, `later` |
| Natural-language description instead of tag | `Traditional Chinese`, `English`, `Japanese` |

Built-in values:

| Value | Meaning |
|---|---|
| `zh-TW` | Write free prose in Traditional Chinese |
| `en` | Write free prose in English |
| `ja-JP` | Write free prose in Japanese |

For custom values, use a conservative BCP-47 validation. W1b may use a
well-maintained package for full validation. If W1b uses regex validation
only, use this as the minimum acceptable hint:

```text
^[A-Za-z]{2,3}(?:-[A-Za-z]{4})?(?:-(?:[A-Za-z]{2}|[0-9]{3}))?(?:-(?:[A-Za-z0-9]{5,8}|[0-9][A-Za-z0-9]{3}))*$
```

This accepts common tags such as `en`, `en-US`, `zh-TW`, `zh-Hant-TW`,
and `ja-JP`. It is intentionally not a full BCP-47 implementation.

Store custom tags exactly as entered after trimming whitespace. Do not
lowercase or uppercase subtags unless W1b deliberately uses a BCP-47
normalizer.

### 4.2 Project Type

`project-type` must be one of:

```text
greenfield
brownfield
```

If source-tree detection contradicts the user's answer, trust the user but
print a warning in the preview, for example:

```text
Warning: src/ already exists, but project type is Greenfield. Continuing with your selected project type.
```

### 4.3 Edition

`edition` must be one of:

```text
core
webforms
```

If detection is confident, the CLI may preselect the likely edition:

| Detection signal | Suggested edition |
|---|---|
| `*.csproj` references `Microsoft.NET.Sdk.Web` and `TargetFramework` starts with `net6.0` or newer | Core |
| `*.csproj` references `System.Web`, `.aspx`, `.ascx`, or `.master` files exist | WebForms |

Detection is only a hint. The user selection wins.

### 4.4 Tech-stack Details

The tech-stack answer is free text. W1b should avoid fragile parsing. Use
simple extraction only for obvious patterns:

| Placeholder | Extraction examples |
|---|---|
| `{ASP.NET Core version}` | `ASP.NET Core 8`, `ASP.NET Core 9` |
| `{EF Core version}` | `EF Core 8`, `Entity Framework Core 9` |
| `{MediatR version}` | `MediatR 12` |
| `{Test framework}` | `xUnit`, `NUnit`, `MSTest` |
| `{ASP.NET WebForms version}` | `WebForms 4.8`, `ASP.NET WebForms` |
| `{.NET Framework version}` | `.NET Framework 4.8` |
| `{ORM / Data Access}` | `EF6`, `Entity Framework 6`, `Dapper`, `ADO.NET` |

If extraction is uncertain, leave the original placeholder token in the
file and report it in the results.

### 4.5 Optional Starter Files

The multiselect answer must be an array. If both Git-principles templates
are selected, the CLI asks:

```text
You selected both Git principles templates. Most projects choose one. Keep both? (y/N)
```

`N` removes both Git-principles selections and returns to Q6. `y` keeps both.

### 4.6 Failure Handling During Prompts

For validation failures, re-ask the current question. The maximum retry
count is:

```text
3 failed attempts per question
```

After the third failed attempt, abort gracefully with stderr and a non-zero
exit code. Do not create files after an aborted prompt session.

## 5. File-list Preview And Confirm

### 5.1 Preview Requirement

Before writing files, the CLI must display the full file plan. The preview
must include:

| Field | Requirement |
|---|---|
| Relative path | Relative to `{cwd}` |
| Action | `create`, `skip`, or `defer` |
| Source | Packaged template source or generated directory placeholder |
| Size estimate | Estimated final byte size after placeholder substitution |
| Notes | Optional explanation such as `mandatory`, `selected`, `already exists`, or `not created by design` |

Size estimate may be shown as bytes or rounded units such as `842 B`,
`1.8 KB`, or `<1 KB`. Use the final substituted content where available.
For directory placeholders such as `.gitkeep`, estimate `0 B`.

### 5.2 Confirm Prompt

After the preview:

```text
Create these files? (y/N)
```

Default is `N`. Any answer other than an affirmative `y` or `yes` is a
graceful abort.

On `N`, the CLI must not leave a half-created `dflow/` directory. The
implementation should avoid creating directories before confirmation. If a
temporary pre-flight file was created, remove it before returning.

### 5.3 Preview Example Shape

```text
Will create:

| Path | Source | Size | Notes |
|---|---|---:|---|
| dflow/specs/shared/_conventions.md | packaged:core/scaffolding/_conventions.md | 5.2 KB | mandatory, includes Prose Language |
| dflow/specs/domain/glossary.md | packaged:core/templates/glossary.md | 430 B | mandatory |
| dflow/specs/architecture/decisions/README.md | packaged:core/scaffolding/architecture-decisions-README.md | 1.1 KB | mandatory |

Will defer:

| Path | Reason |
|---|---|
| dflow/specs/domain/{context}/behavior.md | Created later when the first bounded context exists. |

Create these files? (y/N)
```

## 6. Write Actions

### 6.1 Existing-file Protection

Before every target write, re-check whether the target path exists.

| Target state | Behavior |
|---|---|
| Target does not exist | Create parent directories, then write the file. |
| Target exists as file or directory | Skip with warning. Do not overwrite. |
| Parent path exists as file | Abort write phase with error because a directory cannot be created. |

No W1 write action may overwrite an existing file. There is no `--force`
contract in W1.

### 6.2 Template Source

All templates must be loaded from the installed npm package. The CLI must
not read source templates from the user's current working directory.

Acceptable logical source model:

```text
package-root/templates/core/...
package-root/templates/webforms/...
```

or another packaged layout chosen by W1b. The implementation detail is open,
but the contract is fixed: packaged source, not working-directory source.

If a packaged template is missing, treat it as an internal packaging bug and
abort.

### 6.3 Placeholder Substitution

W1b should substitute only init-level placeholders that have reliable
answers. Domain placeholders in templates should remain as placeholders for
future SDD workflows.

| Placeholder | Source |
|---|---|
| `{YYYY-MM-DD}` | Current local date in ISO format at runtime |
| `{System Name}` | Basename of `{cwd}` unless W1b adds an explicit edit prompt in a later wave |
| `{系統名稱}` | Same as `{System Name}` |
| `{project-type}` | Q1 normalized value: `greenfield` or `brownfield` |
| `{edition}` | Q2 normalized value: `core` or `webforms` |
| `{tech-stack-summary}` | Q3 raw trimmed text |
| `{migration-context}` | Q4 raw trimmed text, or `none` |
| `{prose-language}` | Q5 or Q5a validated language tag |
| `{ASP.NET Core version}` | Extract from Q3 when obvious; otherwise leave token |
| `{EF Core version}` | Extract from Q3 when obvious; otherwise leave token |
| `{MediatR version}` | Extract from Q3 when obvious; otherwise leave token |
| `{Test framework}` | Extract from Q3 when obvious; otherwise leave token |
| `{ASP.NET WebForms version}` | Extract from Q3 when obvious; otherwise leave token |
| `{.NET Framework version}` | Extract from Q3 when obvious; otherwise leave token |
| `{ORM / Data Access}` | Extract from Q3 when obvious; otherwise leave token |
| `{gitflow\|trunk}` | Q6 Git-principles selection when exactly one is selected; otherwise leave token |

For unresolved placeholders that remain in emitted files, add them to the
results report. Do not insert noisy TODO comments into every unresolved
placeholder in W1b unless the packaged template already contains such TODOs.

### 6.4 Mandatory Output Paths

#### Core Edition

| Output path | Source | Notes |
|---|---|---|
| `dflow/specs/features/active/.gitkeep` | generated empty file | Directory placeholder |
| `dflow/specs/features/completed/.gitkeep` | generated empty file | Directory placeholder |
| `dflow/specs/features/backlog/.gitkeep` | generated empty file | Directory placeholder |
| `dflow/specs/shared/_conventions.md` | packaged Core `_conventions.md` plus P015 Prose Language section | Mandatory because prose language must be persisted |
| `dflow/specs/domain/glossary.md` | packaged Core `templates/glossary.md` | Mandatory |
| `dflow/specs/domain/context-map.md` | packaged Core `templates/context-map.md` | Mandatory for Core |
| `dflow/specs/architecture/tech-debt.md` | packaged Core `templates/tech-debt.md` | Mandatory |
| `dflow/specs/architecture/decisions/README.md` | packaged Core `scaffolding/architecture-decisions-README.md` | Mandatory ADR directory documentation |

#### WebForms Edition

| Output path | Source | Notes |
|---|---|---|
| `dflow/specs/features/active/.gitkeep` | generated empty file | Directory placeholder |
| `dflow/specs/features/completed/.gitkeep` | generated empty file | Directory placeholder |
| `dflow/specs/features/backlog/.gitkeep` | generated empty file | Directory placeholder |
| `dflow/specs/shared/_conventions.md` | packaged WebForms `_conventions.md` plus P015 Prose Language section | Mandatory because prose language must be persisted |
| `dflow/specs/domain/glossary.md` | packaged WebForms `templates/glossary.md` | Mandatory |
| `dflow/specs/migration/tech-debt.md` | packaged WebForms `templates/tech-debt.md` | Mandatory |

### 6.5 Optional Output Paths

| Q6 selection | Output path | Source | Condition |
|---|---|---|---|
| `_overview.md` | `dflow/specs/shared/_overview.md` | Packaged edition-specific `scaffolding/_overview.md` | Create if selected |
| `Git principles - trunk-based` | `dflow/specs/shared/Git-principles-trunk.md` | Packaged edition-specific `scaffolding/Git-principles-trunk.md` | Create if selected |
| `Git principles - Git Flow` | `dflow/specs/shared/Git-principles-gitflow.md` | Packaged edition-specific `scaffolding/Git-principles-gitflow.md` | Create if selected |
| `CLAUDE.md snippet / project AI guide` | `CLAUDE.md` | Packaged edition-specific `scaffolding/CLAUDE-md-snippet.md`, snippet body only | Create only if selected and repo-root `CLAUDE.md` is missing |
| `CLAUDE.md snippet / project AI guide` | `dflow/specs/shared/CLAUDE-md-snippet.md` | Packaged edition-specific `scaffolding/CLAUDE-md-snippet.md`, full scaffolding file | Create only if selected and repo-root `CLAUDE.md` already exists |

### 6.6 Explicitly Deferred Paths

| Path | Reason |
|---|---|
| `dflow/specs/domain/{context}/behavior.md` | Needs a real bounded context; created later by feature completion or baseline capture. |
| `dflow/specs/domain/{context}/models.md` | Needs a real bounded context. |
| `dflow/specs/domain/{context}/rules.md` | Needs a real bounded context. |
| `dflow/specs/domain/{context}/events.md` | Core only, but still needs a real bounded context. |
| `dflow/specs/architecture/decisions/ADR-*.md` | ADRs are created when a real architecture decision exists. |

## 7. Post-write Actions

### 7.1 Prose Language Section

After writing `dflow/specs/shared/_conventions.md`, ensure it contains
this section exactly once. Prefer inserting it before `## Filling the
Templates`.

```markdown
## Prose Language

Project prose language: `{prose-language}`

Dflow templates keep canonical English structural language: headings,
table headers, fixed labels, placeholders, IDs, anchors, and code-facing
terms remain English.

Free prose written inside those sections should follow the project prose
language:

- `en`: write free prose in English.
- `zh-TW`: write free prose in Traditional Chinese.
- `{xx-XX}`: write free prose in that explicit BCP-47 language.

Do not translate code identifiers, DDD pattern names, BR IDs, SPEC IDs,
file paths, branch names, anchors, or inline code only to satisfy the
prose-language setting.
```

The emitted file must replace `{prose-language}` with the validated Q5/Q5a
answer.

If `_conventions.md` was skipped because the target already existed, report
a warning that prose language was not written and that the user must ensure
`## Prose Language` exists manually before running prose-generating flows.

### 7.2 Results Report

At the end of a successful write phase, print:

| Report section | Contents |
|---|---|
| `Created` | Relative paths actually written |
| `Skipped` | Relative paths skipped due to existing-file protection |
| `Warnings` | Legacy `specs/` warning, detection mismatches, unresolved placeholders, skipped prose-language persistence |
| `Deferred` | Paths not created by design, such as context-level behavior files |

### 7.3 Next-step Hint

The next-step hint may mention future commands even if W1 only implements
`init`, but it must be clear that init itself is complete.

Suggested text:

```text
Dflow init complete.

Recommended next steps:
- For a new feature, use the Dflow new-feature workflow when it becomes available as a CLI command.
- For brownfield changes, use the Dflow modify-existing workflow when it becomes available as a CLI command.
- Before generating more specs, make sure dflow/specs/shared/_conventions.md has the correct Prose Language section.
```

Avoid requiring a specific AI tool in this hint.

## 8. Error Handling

| Error class | Behavior |
|---|---|
| Pre-flight failure | Abort before prompts. Print a stderr message and exit non-zero. |
| User presses Ctrl+C during Q&A | Graceful abort. Print `Dflow init aborted.` Do not leave generated files or a half-created `dflow/` directory. |
| Validation failure | Re-ask the current question. After 3 failed attempts for the same question, abort with stderr and non-zero exit code. |
| User rejects preview | Graceful abort. Do not create `dflow/` or any target files. Exit zero unless W1b chooses non-zero for user cancellation consistently across the CLI. |
| Write permission failure during write phase | Abort with stderr and non-zero exit code. Keep files already written. Tell the user to manually clean up partial output. |
| Existing target file | Skip with warning. Do not overwrite. Continue writing other targets. |
| Template missing from package | Abort with stderr and non-zero exit code. Message should include `Internal error: packaged template not found`. |
| Invalid packaged template content | Abort if the template cannot be read as UTF-8 text. This is an internal packaging error. |

For write-phase failures, W1b does not need rollback in V1. Rollback would
increase implementation risk and could accidentally remove user-created
files in a race. The safer contract is to keep already-written files and
report the partial state clearly.

## 9. Out Of Scope For W1

| Feature | W1 decision |
|---|---|
| `dflow new-feature` | Not implemented in V1 W1. May be mentioned as future next step. |
| `dflow modify-existing` | Not implemented in V1 W1. |
| `dflow new-phase` | Not implemented in V1 W1. |
| `dflow finish-feature` | Not implemented in V1 W1. |
| `dflow bug-fix` | Not implemented in V1 W1. |
| `dflow doctor` | Not implemented in V1. |
| `dflow migrate` | Not implemented in V1. |
| Multi-language CLI prompts | Not implemented. V1 CLI prompts are English. |
| Localized template heading sets | Not implemented. Structural language remains canonical English. |
| File-level prose-language directive | Not implemented as V1 default. Candidate A from P015 remains future work. |
| Heading localization | Not implemented. Backlog section 1.1 remains separate. |
| Legacy root `specs/` migration | Not implemented. Warn only. |
| Reading templates from the user project | Not allowed. Templates are packaged. |
| `--force` overwrite behavior | Not implemented in W1. |

## 10. W1b Implementation Handoff

### 10.1 Spec-to-module Mapping

| Contract section | Suggested W1b module responsibility |
|---|---|
| Section 2, Pre-flight checks | `lib/init/preflight.js` or equivalent helper |
| Section 3, Q&A flow | Prompt orchestration inside `lib/init.js` or `lib/init/prompts.js` |
| Section 4, Validation rules | Small validation helpers, especially `validateProseLanguage()` |
| Section 5, File-list preview | Plan builder and preview renderer |
| Section 6, Write actions | Template loader, placeholder substitution, safe writer |
| Section 7, Post-write actions | Results reporter and next-step message |
| Section 8, Error handling | Shared init error handling and exit-code policy |

This mapping is guidance only. W1b may keep a flatter structure if the first
implementation stays small.

### 10.2 npm Package Skeleton Recommendation

Minimum skeleton:

```text
package.json
bin/
└── dflow.js
lib/
└── init.js
templates/
├── core/
└── webforms/
```

Recommended `package.json` fields:

| Field | Requirement |
|---|---|
| `name` | Intended package name, pending publish availability |
| `version` | Initial semver |
| `type` | Either `module` or omitted for CommonJS; choose one deliberately |
| `bin.dflow` | Points to `bin/dflow.js` |
| `engines.node` | `>=22.0.0` |
| `files` | Include `bin/`, `lib/`, `templates/`, and docs needed at runtime |
| `scripts.test` | At least a smoke-test placeholder or real smoke test |

`bin/dflow.js` should parse `dflow init`, `--help`, and `--version`, then
delegate to `lib/init.js`. Do not add other subcommands in W1.

### 10.3 Dependency Guidance

W1b may choose dependencies, but should keep them narrow:

| Need | Dependency guidance |
|---|---|
| Prompts | Use a maintained prompt library that supports select, multiselect, text, defaults, and Ctrl+C handling. |
| File operations | Node built-in `fs/promises` is enough for W1. |
| Path handling | Node built-in `path`. |
| BCP-47 validation | Prefer a small maintained BCP-47 / language-tag validator if available; otherwise use the regex hint in section 4.1. |
| CLI argument parsing | A tiny parser or simple manual parsing is enough for `init`, `--help`, and `--version`. |

Avoid dependencies that require native compilation for W1.

### 10.4 Smoke Test Acceptance Criteria

Run this in a clean temporary directory:

```bash
npx dflow init
```

Selections:

| Prompt | Selection |
|---|---|
| Q1 | `Greenfield` |
| Q2 | `ASP.NET Core` |
| Q3 | Example: `ASP.NET Core 9, EF Core 8, MediatR 12, xUnit` |
| Q4 | `none` |
| Q5 | `zh-TW` |
| Q6 | `_overview.md`, `Git principles - trunk-based` |

Expected structure:

```text
dflow/specs/
├── shared/
│   ├── _conventions.md
│   ├── _overview.md
│   └── Git-principles-trunk.md
├── domain/
│   ├── glossary.md
│   └── context-map.md
├── architecture/
│   ├── tech-debt.md
│   └── decisions/
│       └── README.md
└── features/
    ├── active/
    │   └── .gitkeep
    ├── completed/
    │   └── .gitkeep
    └── backlog/
        └── .gitkeep
```

Acceptance criteria:

| Criterion | Expected result |
|---|---|
| Namespace | No root-level `specs/` is created. |
| Conventions | `dflow/specs/shared/_conventions.md` contains exactly one `## Prose Language` section. |
| Prose language | That section contains `Project prose language:` and the validated value `zh-TW` in the emitted Markdown. |
| Core baseline | Structure aligns with the Tutorial scenario 1 Core baseline after applying the P014 path prefix: `tutorial/01-greenfield-core/outputs/dflow/specs/`. Byte-level equality is not required. |
| Existing-file protection | Re-running in the same directory aborts at pre-flight because `dflow/specs/` is already initialized. |
| Legacy warning | If a root `specs/` directory exists in a clean test, init warns but still writes to `dflow/specs/`. |

The current checkout has tutorial outputs under
`tutorial/01-greenfield-core/outputs/dflow/specs/` after the P014 W2 cascade.
The smoke test should compare structure conceptually rather than require
byte-level equality.
