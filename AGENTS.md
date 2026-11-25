# JASP module guidance for Codex

This is a JASP module with QML user interfaces and R backend computations. Be concise but clear.

## Detailed rule sources

Before changing files, read the detailed instructions that exist in this repository:

- Prefer `.codex/rules/` when present.
- Otherwise read `.claude/CLAUDE.md` and the relevant files under `.claude/rules/` when present.
- For `R/*.R`, read the module architecture, dependency, state, R, table, plot, container/error, output, and translation rules.
- For `inst/qml/*.qml`, read the module architecture, QML, and translation rules.
- For `tests/testthat/*.R`, read the testing and output-structure rules.
- Before Git operations, read the Git workflow rules.

This `AGENTS.md` takes precedence over every delegated `.codex` or `.claude` file when instructions conflict. In particular, the workflow and verification rules below supersede older requirements to run full test suites or use Claude-specific models, tools, and attribution.

Translate Claude-specific tool or model names to the available Codex equivalents. Do not add Claude attribution to commits or pull requests.

## Standing workflow rules

- Preserve user changes and unrelated dirty worktree content.
- Work on a feature branch, never directly on `master`.
- Keep related follow-up fixes on the existing PR branch. Do not create another branch without need.
- Do not push, open or edit a PR, merge, approve, or post comments without explicit authorization. An explicit request to fix a merge conflict on an existing PR includes committing and normally pushing that resolution to the same PR branch. Never force-push.
- When asked to look through or scan code, use read-only inspection only. Do not run tests or set up environments unless separately requested.
- Report and fix only issues realistically reachable through JASP with plausible data. Omit artificial, GUI-blocked, dead-code, or developer-only edge cases unless asked.
- After changing files, run `/Users/julius/.codex/bin/claude-review "<goal>" "<verification performed>" "<known pre-existing failures>"` before reporting completion. This invokes Claude Opus as the primary independent reviewer and supplies the current branch and diff automatically. Address valid findings and explain rejected ones.
- If the Claude reviewer is unavailable or its subscription limit is reached, use the project `code-reviewer` Codex subagent as fallback and state that fallback explicitly.

## JASP development rules

- Treat QML `name` values as the exact R option API. Keep QML names, R `options[["..."]]` reads, and `$dependOn()` vectors synchronized.
- Do not add R aliases, backup defaults, or normalization for missing ordinary GUI options in unreleased work. Validate datasets and arbitrary `TextField`/`FormulaField` input only.
- For checkbox options use `if (options[["flag"]])`, not `isTRUE()`, so missing options remain visible as errors.
- Add `$dependOn()` to every output, container, and state object; children inherit container dependencies.
- Never instantiate jaspResults C++ objects directly. Use `jaspTools::runAnalysis()` or the project test helpers.
- Always call `jaspTools::runAnalysis(..., view = FALSE)` and inspect the returned object.
- Use `package::function()` rather than `library()` or `require()` in analysis code.
- Wrap user-visible R strings in `gettext()`/`gettextf()` and QML strings in `qsTr()`. Use numbered placeholders and escape `%` as `%%` in format strings.
- After editing QML, run `qmllint`; ignore missing JASP-module import warnings but fix syntax errors.
- Avoid new dependencies unless necessary. If one is required, update `DESCRIPTION` and `renv.lock`.
- For released analyses, option renames require an `inst/Upgrades.qml` migration. Do not add compatibility layers for unreleased analyses.

## Verification

- Run only affected tests locally; leave the full suite to CI unless the user explicitly asks for it. State that the full suite was left to CI.
- Load the working tree before tests (`pkgload::load_all(".")` or the project equivalent) so tests do not use an installed copy.
- Never automatically accept or replace snapshots. Report new or changed snapshots for manual inspection.
- Never cancel a test that is already running; if an MCP call times out, inspect the continuing process or use the documented fallback rather than starting a duplicate run.
- For every fix, state whether a manual JASP Desktop check is needed. It is normally needed for QML behavior, `$dependOn()`/state invalidation, `Upgrades.qml`, interactive plots, and Desktop-only behavior. Give short exact steps when needed.

## Pull requests and commits

- Keep commit messages and PR descriptions short and plain: what was wrong, what changed, what reviewers should inspect, and how it was checked.
- Show a PR description draft and wait for approval before posting it.
- Do not add `Co-Authored-By: Claude` or other inaccurate model attribution.
