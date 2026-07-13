---
name: manage-active-mind-stack
description: "Use when the user asks which Worker is active, wants to select another Worker, or wants to clear Worker selection. Enforces the singular Worker contract with a preview before mutation."
---

# manage-active-mind-stack

## Purpose

Inspect or change the current project's selected Worker. The skill name is
retained for compatibility, but a project does not have a Worker stack: Cards
compose inside a Blueprint, and the project selects at most one declared root
as its active Worker.

Requires `drwn` on PATH. Scope is project. Selection changes project config
and can project downstream files unless `--no-write` is used.

## Procedure

1. Read current project state with `drwn status --json` and Card roots with
   `drwn card status --json`.
2. Present the declared roots and the single selected Worker, or state that no
   Worker is selected.
3. For inspection-only requests, stop without mutation.
4. For selection, resolve the requested root and preview with:

   ```bash
   drwn use <root> --dry-run
   ```

   `drwn use` may install a new root additively before selecting it. Use
   `drwn apply` when the user instead wants to replace the complete root set.
5. For clearing selection without removing roots, preview with:

   ```bash
   drwn use --none --dry-run
   ```

6. Summarize the proposed config, lock, and downstream changes. Ask for
   approval before mutation.
7. On approval, run the same `drwn use` command without `--dry-run`.
   Add `--no-write` only when the user explicitly wants to commit selection
   without projecting downstream files.
8. Verify final state with `drwn status --json`, `drwn card status --json`, and
   `drwn doctor --json`.

## User-Ask Points

1. Confirm selecting or installing a Worker after reviewing the preview.
2. Confirm clearing Worker selection after reviewing the preview.
3. Confirm `--no-write` when project intent should change without downstream
   projection.

## Wraps

`drwn status --json`, `drwn card status --json`,
`drwn use <root> --dry-run`, `drwn use <root>`,
`drwn use --none --dry-run`, `drwn use --none`, `drwn doctor --json`

## Scope

Project only.

## Failure Modes

- Not a drwn project: stop and redirect to `bootstrap-project`.
- Ambiguous or unresolved root: surface the resolver error and ask for a more
  specific Card ref.
- Multiple declared roots with no selection: report the valid unselected
  state; never activate all roots implicitly.
- Write conflict: surface the target and ownership diagnostic. Do not use
  `--force` to bypass invalid project state.

## Related Skills

- `apply-mind-card`
- `bootstrap-project`
- `inspect-minds`
- `materialize-minds`
- `repair-minds`
