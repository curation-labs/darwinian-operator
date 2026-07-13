---
name: apply-mind-card
description: "Use when applying, adding, pinning, removing, updating, detaching, or inspecting Mind Cards in the current project before materializing downstream changes."
---

# apply-mind-card

## Purpose

Manage the project's Worker roots: replace the complete set, add or remove one
root, pin exact versions, refresh the lock graph, clear all roots, inspect what
is currently applied, and manage explicit hook consent for locked Cards.

Requires `drwn` on PATH. Scope is project. Blast radius is medium because this
skill mutates project config, `card.lock`, and usually downstream generated
state after `drwn write`.

Cards pin mind state: card versions, bundled skills, MCP definitions,
extension intent, targets, and the project overlay. They do not pin the `drwn`
version, Claude/Codex/Cursor versions, operating-system dependencies, or
unpinned MCP runtime packages.

Card-bundled MCP definitions are optional until the project opts in with
`drwn add mcp <name>`. Card-bundled hooks are skipped by default unless the
locked card has valid hook consent recorded in `card.lock`.

## Procedure

1. Read project card state with `drwn card status --json`.
2. Read published local card inventory with `drwn card list --json`.
3. Disambiguate intent:
   - Apply a full set: `drwn apply <spec> ...`
   - Add one root: `drwn add <spec>`
   - Pin one root: `drwn pin <spec>`
   - Remove one root: `drwn remove <name>`
   - Clear all roots: `drwn apply --none`
   - Refresh the lock graph: `drwn update [name]`
   - Select one Worker: `drwn use <root>`
   - Clear Worker selection while keeping roots: `drwn use --none`
   - Inspect updates: `drwn card outdated --json`
   - Fetch remote update candidates: `drwn card outdated --fetch --json`
   - Trust card hooks: `drwn card trust <card> --hooks [--range <range>]`
   - Clear card hook consent: `drwn card untrust <card> --hooks`
4. Preview root mutations with `--dry-run` and show the exact before/after
   intent. Multiple roots require `--active <root>` or `--none`; they never
   form an implicit stack.
5. On approval, run the selected root command without `--dry-run`.
6. After mutation, run `drwn status --json` and `drwn card status --json`.
   Report the singular selected Worker or the valid unselected state.
7. If a locked card declares hooks, inspect it with
   `drwn card show <card>@<version> --json` and summarize its `hookPolicies`.
   If `card status` or `doctor` reports missing or out-of-range hook consent,
   ask before running `drwn card trust <card> --hooks [--range <range>]`.
   Use `drwn card untrust <card> --hooks` only when the user explicitly wants
   to clear prior hook consent.
8. Preview downstream effect with `drwn write --dry-run --json`.
9. Summarize `changes`, `warnings`, and `optionalMcpReport`. If hooks are
   skipped, explain whether consent is missing or out of range. If optional
   card MCP servers are skipped, tell the user which project opt-in commands
   such as `drwn add mcp <name>` would activate them.
10. If the user wants hook issues to fail closed, rerun the preview as
   `drwn write --strict-hooks --dry-run --json` and resolve any consent issues
   before a real write.
11. On approval, run `drwn write`, adding `--strict-hooks` only when the user
    chose fail-closed hook behavior.
12. Confirm the result with `drwn card status --json` and `drwn status --json`.
13. If the user wants provenance for a newly active item, run
   `drwn status --why <name>`.

## User-Ask Points

1. Confirm the requested root-set mutation after reviewing its dry run.
2. Confirm hook consent before any `drwn card trust <card> --hooks`.
3. Confirm clearing hook consent before any `drwn card untrust <card> --hooks`.
4. Confirm optional card MCP activation before any `drwn add mcp <name>`.
5. Confirm the final `drwn write` after reviewing `changes`, warnings, and
   optional MCP report.

## Wraps

`drwn card status --json`, `drwn card list --json`, `drwn card outdated --json`,
`drwn card outdated --fetch --json`, `drwn apply`, `drwn add`,
`drwn pin`, `drwn remove`, `drwn update`, `drwn use`,
`drwn status --json`, `drwn card show --json`, `drwn card trust --hooks`,
`drwn card untrust --hooks`, `drwn add mcp`, `drwn write --dry-run --json`,
`drwn write --strict-hooks --dry-run --json`, `drwn write`,
`drwn write --strict-hooks`, `drwn status --why`

## Scope

Project only.

## Failure Modes

- Unresolved card spec: surface the exact failure and stop.
- Duplicate root on add: surface the conflict and suggest `pin` or `update`.
- Missing card on remove or pin: tell the user and refer back to the current
  project status.
- No outdated cards: explain that there is nothing to refresh.
- Hook consent missing or out of range: hooks are skipped by default; ask for
  explicit consent before `drwn card trust <card> --hooks`.
- `--strict-hooks` preview fails: resolve hook consent first or rerun without
  strict mode only after the user accepts skipped hooks.
- Optional card MCP skipped: explain that the card made the definition
  available but project activation still requires `drwn add mcp <name>`.
- Multiple roots without a selected Worker: report the unselected state and
  redirect selection requests to `manage-active-mind-stack`.

## Related Skills

- `author-mind-card`
- `install-project`
- `materialize-minds`
- `share-mind-card`
- `inspect-minds`
- `recommend-minds`
- `manage-active-mind-stack`
