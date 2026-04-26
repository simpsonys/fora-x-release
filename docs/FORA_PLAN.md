# FORA Plan v1

FORA Plan is the safe planning and execution layer for FORA-X file operations.

```text
User intent
  -> parser/planner
  -> FileOperationPlan
  -> safety validation
  -> dry-run preview
  -> user confirmation
  -> execution
  -> transaction log
  -> optional conservative undo
```

FORA Plan v1 is deterministic. It does not call external AI APIs and natural-language text never executes file operations directly.

## Core Principles

- Planning never mutates files.
- Natural language never bypasses validation, preview, or confirmation.
- Destructive operations require explicit confirmation and are rejected for natural-language delete in v1.
- Operations are constrained to the active pane folder.
- Recursive operations are rejected in v1.
- Protected paths are blocked.
- Collision detection is mandatory.
- Transaction logs are written before mutation.

## Supported Intents

FORA Plan v1 supports a deliberately small deterministic set.

### Prefix Rename

Examples:

- `현재 폴더에서 md 파일들 앞에 YYMMDD_ prefix 를 추가해줘`
- `현재 폴더에서 .md 파일들 앞에 250426_ 붙여줘`
- `md 파일 앞에 YYMMDD_ 붙여줘`
- `add YYMMDD_ prefix to md files in current folder`
- `add 250426_ prefix to .md files`
- `rename .txt files with prefix backup_`
- `txt 파일 앞에 backup_ 붙여줘`

`YYMMDD_` resolves to the local current date, such as `260426_`.

### Create Folder / File

Examples:

- `create folder notes`
- `notes 폴더 만들어줘`
- `create file todo.md`
- `todo.md 파일 만들어줘`

### Copy / Move By Extension

Examples:

- `move jpg files to Images folder`
- `jpg 파일을 Images 폴더로 옮겨줘`
- `copy md files to Backup folder`
- `md 파일을 Backup 폴더로 복사해줘`

If the target folder does not exist, the plan includes a `mkdir` operation before copy/move.

## Unsupported Intents

- Natural-language delete.
- Recursive operations.
- Operations outside the active pane folder.
- Complex wildcard expressions beyond simple extension matching.
- Cross-pane natural-language operations.
- AI-generated plans without the same validation path.

## Plugin API v2 Handoff

Plugin API v2 commands can request FORA Plan previews without mutating files:

- `ctx.request_file_operation_plan(plan)` for an already constructed `FileOperationPlan`.
- `ctx.request_file_operation_plan_from_text(intent_text)` for deterministic natural-language planning.
- `ctx.request_plan(intent_text)` and `ctx.create_plan_from_text(intent_text)` as compatibility aliases.

String intent requests are parsed into a `FileOperationPlan`, validated, checked
against the plugin's declared permissions, and then the dry-run preview UI is
shown immediately if a UI service is available. They never execute automatically.
If a preview UI is not available, the plugin context shows a fallback message
and may copy the intent text only when the plugin declared `clipboard.write`
and a clipboard service is available.

## Safety Validation

FORA Plan validates:

- source and target stay under the base path
- protected paths are rejected
- source exists
- target collisions
- duplicate targets inside one plan
- invalid Windows filename characters
- symlink path components where detectable
- operation count over the safe threshold

Protected path examples include:

- `C:\Windows`
- `C:\Program Files`
- `C:\Program Files (x86)`
- `C:\Users` root
- drive root
- repository `reference/`
- repository `.git/`

## Transaction Logs

Transaction logs are written under:

```text
%APPDATA%/FORA-X/Transactions/
```

File name:

```text
YYYYMMDD_HHMMSS_<plan-id>.json
```

The log includes intent text, base path, operations, status, errors, app version,
and undo metadata for new executed transactions. It is a conservative rollback
record, not full filesystem time travel.

New transaction logs also include undo metadata for supported operations:

- transaction id and plan id
- created/executed timestamps
- base path and operation list
- undo status
- per-operation before/after existence
- source/target state, file size, mtime, and small-file checksum when cheap
- whether the target was created by the transaction
- per-operation undo support and undo status

Old transaction logs that do not contain undo metadata are treated as not
undoable and must not crash the app.

## Undo

`undo_last_plan_transaction` reads the latest executed FORA Plan transaction log,
builds an inverse-operation preview, validates the current filesystem state, and
asks for confirmation before mutating files. `Ctrl+Z` routes to this command
outside text inputs.

Supported undo operations in v1:

- `rename`: move `target_path` back to `source_path`
- `move`: move `target_path` back to `source_path`
- `copy`: delete the copied target only when it was created by the transaction
  and still matches the recorded state
- `mkdir`: remove the folder only when it was created by the transaction and is
  empty, or only contains transaction-created targets that are also being undone
- `touch`: remove the created file only when it still matches recorded metadata

Unsupported undo operations in v1:

- natural-language delete
- permanent delete
- overwritten files
- recursive operations
- operations outside transaction logs
- old transaction logs without undo metadata
- any operation where source or target state no longer matches the transaction
  metadata

Undo preview does not mutate files. Undo execution updates the transaction log
with `undo_status` such as `undone`, `blocked`, or `failed`.

## Commands

- `plan_natural_language_operation`: prompt for intent, validate, preview, confirm, execute.
- `preview_current_plan`: show the current dry-run preview.
- `execute_current_plan`: execute the current validated plan after confirmation.
- `cancel_current_plan`: cancel/clear the current plan.
- `undo_last_plan_transaction`: preview and undo the latest safe executed plan.
- `show_last_plan_transaction`: show the latest executed transaction log path.
- `show_plan_history`: list recent transaction logs.
- `open_transaction_log_folder`: open `%APPDATA%/FORA-X/Transactions/`.

## Future AI Integration

FORA Forge can create plan drafts from text, pasted JSON, or optional AI
provider output, but generated file operations still pass through the same
validator, preview, confirmation, executor, and transaction logger. AI must not
bypass FORA Plan safety. AI-generated plans do not execute automatically.
