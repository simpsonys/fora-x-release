# FORA Plugin API v2

FORA Plugin API v2 is the native FORA-X plugin model. It is separate from the
best-effort fman compatibility layer and is designed for permission declaration,
controlled command registration, Forge-generated plugins, and offline sharing
through FORA Pack.

Plugin API v2 is not a complete OS sandbox. It enforces permissions on official
FORA API surfaces such as `PluginContext`, `CommandContext`, and FORA Plan
requests. In-process Python plugin code can still import the Python standard
library directly unless a future subprocess or OS sandbox is added. Treat
untrusted plugin code as code.

## Structure

```text
MyPlugin.fora-plugin
  manifest.json
  plugin/
    my_plugin/
      __init__.py
      plugin.py
  README.md
  LICENSE optional
  hashes.json optional
```

Bundle inspection and installation read `manifest.json` and file metadata only.
They must not import plugin code. Plugin code is imported only during
`reload_plugins` or app startup plugin loading.

## Manifest

```json
{
  "schema": "fora.plugin.v2",
  "id": "com.example.my-plugin",
  "name": "My Plugin",
  "version": "0.1.0",
  "description": "Example FORA Plugin API v2 plugin",
  "author": "unknown",
  "entry": "my_plugin.plugin:create_plugin",
  "api": {
    "requires": ["fora.plugin.v2"],
    "min_fora_x": "0.1.11"
  },
  "permissions": ["fs.read.current_folder", "fs.write.current_folder"],
  "contributes": {
    "commands": [
      {
        "id": "com.example.my-plugin.add_prefix",
        "title": "Add Prefix",
        "category": "file",
        "permission_required": ["fs.write.current_folder"]
      }
    ],
    "key_bindings": [],
    "context_menu": [],
    "action_bar": []
  }
}
```

Required fields are `schema`, `id`, `name`, `version`, and `entry`. Unknown
permissions are rejected for v2.

## Runtime

```python
def create_plugin(context):
    context.register_command(
        id="com.example.say_hello",
        title="Say Hello",
        handler=say_hello,
        permissions=["ui.message"],
    )


def say_hello(ctx):
    ctx.show_message("Hello from FORA Plugin API v2")
```

`PluginContext` supports command, context menu, action bar, and key binding
registration. Plugin command handlers receive a limited `CommandContext` with
`active_pane`, `selected_paths`, `base_path`, `other_pane_path`, `show_message`,
`show_error`, `confirm`, `request_file_operation_plan`,
`request_file_operation_plan_from_text`, `request_plan`, `create_plan_from_text`,
and `execute_plan_after_confirmation`.

The v2 context does not expose the raw `MainWindow`, arbitrary QWidget tree,
private pane internals, or unrestricted filesystem helpers.

Plugins can hand off safe file changes to FORA Plan in two ways:

```python
def plan_md_prefix(ctx):
    ctx.request_file_operation_plan_from_text(
        "현재 폴더에서 md 파일들 앞에 YYMMDD_ prefix 를 추가해줘"
    )
```

`request_file_operation_plan(plan)` accepts a constructed `FileOperationPlan`.
For compatibility with generated and test plugins, it also accepts an intent
string and delegates to `request_file_operation_plan_from_text`. The aliases
`request_plan(intent_text)` and `create_plan_from_text(intent_text)` are also
available.

Natural-language plugin requests are parsed into a `FileOperationPlan`,
validated, checked against declared permissions, and then handed to the FORA
Plan preview path. If a UI preview service is available, the dry-run preview
is shown immediately. Plugin plan requests never execute automatically.

## Permissions

Low risk:

- `ui.message`
- `clipboard.write`
- `clipboard.read`
- `settings.read`
- `profile.export`
- `theme.apply`

Filesystem scoped:

- `fs.read.current_folder`
- `fs.write.current_folder`
- `fs.rename.current_folder`
- `fs.copy.current_folder`
- `fs.move.current_folder`
- `fs.delete.recycle_bin`
- `fs.read.selected`
- `fs.write.selected`

Higher risk:

- `fs.read.anywhere`
- `fs.write.anywhere`
- `fs.delete.permanent`
- `process.spawn`
- `network.http`
- `network.local`
- `plugin.install`
- `settings.write`

High-risk permissions generate warnings during inspection/loading.

## Enforcement

The v2 foundation enforces permissions for command execution registered through
`PluginContext`, file reads/writes exposed by `CommandContext`, and FORA Plan
requests made through `CommandContext`.

`PermissionChecker.require_plan` only receives constructed `FileOperationPlan`
objects. Natural-language parsing stays in `CommandContext` or future plan
services so raw text cannot bypass FORA Plan validation.

FORA Plan permission mapping:

- `rename`: `fs.rename.current_folder`, `fs.write.current_folder`, or `fs.write.anywhere`
- `copy`: `fs.copy.current_folder`, `fs.write.current_folder`, or `fs.write.anywhere`
- `move`: `fs.move.current_folder`, `fs.write.current_folder`, or `fs.write.anywhere`
- `mkdir` / `touch`: `fs.write.current_folder` or `fs.write.anywhere`
- `delete`: `fs.delete.recycle_bin` or `fs.delete.permanent`

Permanent delete remains blocked in this foundation even when declared. Network
helpers are not exposed in v1. Process spawning is not exposed in v1.

## Contributions

The contribution registry stores commands, key bindings, context menu items, and
action bar items. Context menu `when` metadata is intentionally simple:
`selection.count > 0`, `selection.single`, `selection.multiple`,
`selection.archive`, `item.directory`, and `item.file`.

Full expression parsing is future work.

## fman Compatibility

Existing fman-compatible plugins continue through the legacy compatibility
loader when no v2 manifest is present. `fora.plugin.v1` bundles remain legacy
metadata and compatibility-oriented. New native plugins and future Forge output
should target `fora.plugin.v2`.

FORA Forge AI provider output also targets Plugin API v2 by default for plugin
drafts. Generated plugin code remains untrusted code and is never executed
during generation, validation, packaging, inspection, or installation.

## Generated Plugin Test Harness

FORA-X includes controlled tests for generated Plugin API v2 bundles. The
harness builds temporary `.fora-plugin` artifacts, inspects and installs them
through FORA Pack, and verifies that plugin modules are not imported until an
explicit Plugin API v2 load/reload path is used.

The harness also checks that:

- generated manifests target `fora.plugin.v2`;
- unknown permissions are rejected before bundle export/install;
- missing declared permissions block official FORA API calls;
- command, context menu, action bar, and key binding contributions register
  through `PluginContext`;
- explicit load behavior is tested only with controlled test plugins.

This is validation coverage, not complete OS-level sandboxing. Once explicitly
loaded, plugin code is still Python code and must be treated as untrusted unless
future subprocess or OS isolation is added.
