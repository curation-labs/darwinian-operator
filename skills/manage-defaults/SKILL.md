---
name: manage-defaults
description: "Use when managing explicit machine-wide Darwinian capability selections and projecting them into user-scope agent configuration."
---

# manage-defaults

## Purpose

Manage machine capability intent through the selected machine profile and
explicit skill or MCP selections. Project capabilities remain project-owned;
machine capabilities are ambient and are never inherited into project config.

Requires `drwn` on PATH. Scope is machine. Mutating defaults changes
`~/.agents/drwn/machine.json`; machine writes project those capabilities into
user-scope Claude, Codex, and Cursor configuration.

## Procedure

1. Read machine capability state with `drwn library defaults list --json` and
   machine diagnostics with `drwn doctor --json` outside a project.
2. Distinguish the requested operation:
   - Inspect machine selections: `drwn library defaults list --json`
   - Select a skill explicitly: `drwn library defaults add skill <name>`
   - Remove an explicit skill: `drwn library defaults remove skill <name>`
   - Select an MCP server explicitly: `drwn library defaults add mcp <name>`
   - Remove an explicit MCP server: `drwn library defaults remove mcp <name>`
3. Confirm that the named capability exists in the Library or selected profile.
4. Preview an add or remove with the corresponding `--dry-run --json` command.
5. Explain whether the capability is profile-provided or explicitly selected.
   Removing an explicit selection does not remove the same capability when the
   selected profile still provides it.
6. Ask for approval, then run the same defaults command without `--dry-run`.
7. Preview machine projection with:

   ```bash
   drwn write --scope machine --dry-run --json
   ```

8. Summarize ownership conflicts and planned user-scope changes. Ask again
   before the real machine write.
9. On approval, run `drwn write --scope machine --json` and verify with
   `drwn library defaults list --json` plus `drwn doctor --json`.

## User-Ask Points

1. Confirm each explicit machine capability selection change.
2. Confirm machine projection after reviewing the dry run.
3. Confirm `--force` separately only for drift in previously drwn-owned output.

## Wraps

`drwn library defaults list --json`,
`drwn library defaults add skill --dry-run --json`,
`drwn library defaults add skill`,
`drwn library defaults remove skill --dry-run --json`,
`drwn library defaults remove skill`,
`drwn library defaults add mcp --dry-run --json`,
`drwn library defaults add mcp`,
`drwn library defaults remove mcp --dry-run --json`,
`drwn library defaults remove mcp`,
`drwn write --scope machine --dry-run --json`,
`drwn write --scope machine --json`, `drwn doctor --json`

## Scope

Machine only. This skill must not mutate project config.

## Failure Modes

- Invalid machine config: stop and surface the strict schema diagnostic; do
  not infer selections from user directories.
- Unknown capability: show available Library/profile inventory and ask for a
  precise name.
- Foreign user-scope destination: report the ownership conflict. Do not claim
  an unrecorded destination, even when bytes happen to match.
- Project directory detected: use explicit `--scope machine`; do not treat
  project capability declarations as machine defaults.

## Related Skills

- `inspect-minds`
- `materialize-minds`
- `repair-minds`
- `recommend-minds`
