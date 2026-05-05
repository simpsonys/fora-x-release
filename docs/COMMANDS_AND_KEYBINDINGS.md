# FORA-X Commands and Key Bindings

This audit covers the v0.1.x release line. Builtin commands are registered by `fora.commands.builtin`; user key bindings are loaded from `%APPDATA%/FORA-X/Settings/key_bindings.json`.

## Release-Key Binding Decisions

- `F3` remains `go_to_current_directory`.
- `Alt+D` also opens `go_to_current_directory`.
- `F6` remains `create_and_edit_file`.
- `F8` remains `flat_view` for YSCommands-compatible behavior.
- `Delete` is handled by the main window as delete-to-Recycle-Bin with confirmation behavior where available.
- `Ctrl+E` enters the configurable Export Tool Mode.
- `Ctrl+Shift+F` enters the configurable Filter Tool Mode.
- `Ctrl+Shift+G` enters the configurable Git Tool Mode.
- `file_list` and `tree_view` remain available from Export mode and the Command Palette, not high-frequency navigation shortcuts.
- `Ctrl+Shift+P` opens the Command Palette.
- `Ctrl+B` toggles the optional Navigation Tree Pane and reveals the current active file pane path without moving focus.
- `Ctrl+Shift+B` shows/focuses the optional Navigation Tree Pane and reveals the last active file pane path.
- `toggle_navigation_tree_pane` is the preferred command id for the optional keyboard-focusable Navigation Tree Pane. `toggle_navigation_sidebar` remains compatible.
- Alt drive shortcuts are removed. Use `Shift+DriveLetter` for drive jumps.
- Plain `Right` enters the focused directory.
- Plain `Left` navigates back.
- `Alt+Left` and `Alt+Right` remain directional move commands in the default settings.

## Default Key Bindings

| Key | Command | Args | Category | Source |
| --- | --- | --- | --- | --- |
| `Tab` | `switch_pane` | - | navigation | settings |
| `F2` | `rename_selected` | - | file operation | settings |
| `F3` | `go_to_current_directory` | - | navigation | settings |
| `Alt+D` | `go_to_current_directory` | - | navigation | settings |
| `F6` | `create_and_edit_file` | - | file operation | settings |
| `F7` | `create_directory` | - | file operation | settings |
| `F8` | `flat_view` | - | view | settings |
| `F9` | `open_terminal` | `{ "profile": "default" }` | external | settings |
| `Ctrl+Right` | `copy_to_right_pane` | - | file operation | settings |
| `Ctrl+Left` | `copy_to_left_pane` | - | file operation | settings |
| `Alt+Right` | `move_to_right_pane` | - | file operation | settings |
| `Alt+Left` | `move_to_left_pane` | - | file operation | settings |
| `Ctrl+E` | Export Tool Mode | - | mode trigger | `tool_modes.json` |
| `Ctrl+Shift+F` | Filter Tool Mode | - | mode trigger | `tool_modes.json` |
| `Ctrl+Shift+G` | Git Tool Mode | - | mode trigger | `tool_modes.json` |
| `Ctrl+G` | `go_to_path` | - | navigation | settings |
| `Ctrl+Alt+P` | `open_terminal` | `{ "profile": "powershell" }` | external | settings |
| `Ctrl+Alt+C` | `open_terminal` | `{ "profile": "cmd" }` | external | settings |
| `Ctrl+Shift+P` | `show_command_palette` | - | command | settings |
| `Ctrl+B` | `toggle_navigation_sidebar` | - | navigation | settings |
| `Ctrl+Shift+B` | `focus_navigation_sidebar` | - | navigation | settings |
| `Ctrl+Z` | `undo_last_plan_transaction` | - | plan/undo | settings |
| `Ctrl+Shift+C` | `clipboard_mode` | - | mode trigger | settings |

Additional direct key handling:

| Key | Behavior | Category | Source |
| --- | --- | --- | --- |
| `Enter` | Open selected item | navigation | builtin event handling |
| `Backspace` | Edit active filter, otherwise go parent | navigation/filter | builtin event handling |
| `Esc` | Clear active pane filter when present | filter | builtin event handling |
| `Delete` | Delete selected item(s) to Recycle Bin path with confirmation behavior | file operation | builtin event handling |
| `Left` | Go back | navigation | builtin event handling |
| `Right` | Enter focused directory | navigation | builtin event handling |
| `Shift+A-Z` | Navigate active pane to that drive root (e.g. `Shift+D` -> `D:\`). Unavailable drives show a status message. Alt drive shortcuts are intentionally removed. | navigation | builtin event handling |

## Tool Modes

Tool Modes are JSON-configurable command groups stored at
`%APPDATA%/FORA-X/Settings/tool_modes.json`. A mode has an id, label, trigger,
and action keys that invoke registered command ids. Loading Tool Modes never
executes commands; invalid actions are skipped with a warning/status message.

Default Export mode:

| Key | Action | Command |
| --- | --- | --- |
| `Ctrl+E` | Enter Export mode | mode trigger |
| `L` | File List report | `file_list` |
| `T` | Tree report | `tree_view` |
| `P` | Copy selected paths | `copy_paths_to_clipboard` |
| `N` | Copy selected names | `copy_names_to_clipboard` |
| `Esc` | Cancel Export mode | - |

`tree_view` is a text report/export command using `tree /a`; it is not the
optional Navigation Sidebar folder tree.

Default Filter mode:

| Key | Action | Command |
| --- | --- | --- |
| `Ctrl+Shift+F` | Enter Filter mode | mode trigger |
| `N` | Prompt for Name column filter | `filter_by_name` |
| `T` | Prompt for Type column filter | `filter_by_type` |
| `S` | Prompt for Size column filter | `filter_by_size` |
| `M` | Prompt for Modified column filter | `filter_by_modified` |
| `C` | Clear column filters | `clear_column_filters` |
| `Esc` | Cancel Filter mode | - |

Default Git mode:

| Key | Action | Command |
| --- | --- | --- |
| `Ctrl+Shift+G` | Enter Git mode | mode trigger |
| `C` | TortoiseGit Commit | `git_commit` |
| `L` | TortoiseGit Log | `git_log` |
| `D` | TortoiseGit Diff | `git_diff` |
| `S` | TortoiseGit Status | `git_status` |
| `A` | TortoiseGit Add | `git_add` |
| `R` | TortoiseGit Revert | `git_revert` |
| `U` | TortoiseGit Pull | `git_pull` |
| `P` | TortoiseGit Push | `git_push` |
| `Esc` | Cancel Git mode | - |

Git mode uses curated `TortoiseGitProc.exe` launches. It does not import or
enumerate the full Windows Explorer shell context menu.

Default Clipboard mode:

| Key | Action | Command |
| --- | --- | --- |
| `Ctrl+Shift+C` | Enter Clipboard Mode | mode trigger |
| `C` | Copy text content | `copy_file_text_to_clipboard` |
| `P` | Copy full path | `copy_paths_to_clipboard` |
| `N` | Copy file name | `copy_names_to_clipboard` |
| `R` | Copy relative path | `copy_selected_relative_path` |
| `L` | Copy selected list | `copy_paths_to_clipboard` |
| `M` | Copy Markdown link | `copy_selected_markdown_link` |
| `Esc` | Exit Clipboard Mode | `exit_tool_mode` |

The direct `copy_file_text_to_clipboard` command also remains available from the
Command Palette without entering Clipboard Mode. Normal `Ctrl+C` file-copy behavior
is not affected.

Use `reload_tool_modes` after editing `tool_modes.json`. Use
`open_tool_mode_settings` to open the settings file.

Action Bar items may use structured `keys` metadata, for example
`{ "command": "go_to_current_directory", "label": "GoTo", "keys": ["Alt+D"] }`.
The rendered hint is `[Alt+D] GoTo`, keeping hints closer to actual shortcuts.

Items may also use `preferred_hint_key` to override the rendered key for compact
modifier-state hints. For example, `"keys": ["Ctrl+Shift+F"], "preferred_hint_key": "F"`
renders as `[F] Filter` when `Ctrl+Shift` is already held.

## Action Bar Modifier Hints

The Action Bar updates automatically when modifier keys are held:

| Modifier | State | What shows |
| --- | --- | --- |
| (none) | `default` | Rename, GoTo, New File/Folder, VSCode, Terminal, Explorer |
| `Ctrl` | `ctrl` | Copy→, ←Copy, Export, Tree |
| `Alt` | `alt` | GoTo, SameDir, Move←, Move→, Props |
| `Ctrl+Shift` | `ctrl+shift` | `[F] Filter`, `[G] Git`, `[P] Palette`, `[B] Tree` |
| `Shift` alone | `shift` | Drive jump hints (dynamic, shows available drives) |

`ctrl+shift` defaults are injected into existing user `action_bar.json` at load
time if the section is absent, so existing installs receive these hints without
reinstalling or deleting settings.

Low-priority items (VSCode, Terminal, Explorer, Flat) are hidden automatically
when the window is too narrow to show all hints. High-priority items (Rename,
GoTo, New File, New Folder, active Tool Mode actions) remain visible.

Entering a Tool Mode replaces the modifier hints with that mode's action hints.
Releasing all modifiers or exiting the mode restores the default Action Bar.

## Command Catalog

Tool Mode actions can call any currently registered command id. Use
`show_command_catalog` from the Command Palette for a quick in-app list, or
`export_command_catalog` to write:

- `%APPDATA%/FORA-X/CommandCatalog/commands.json`
- `%APPDATA%/FORA-X/CommandCatalog/COMMANDS.md`

The catalog is best-effort and includes command id, label, category,
description, args, selection requirement, destructive flag, and Tool Mode safety
flag when known. Dynamic plugin commands appear after they are registered.

### Go To Directory MRU History

The `go_to_current_directory` (`F3` or `Alt+D`) dialog shows three sets of suggestions:

1. **Filesystem candidates** — children of the directory currently typed, prefix-matched (existing behavior).
2. **Favorite candidates** — saved favorites whose label or path contains the typed text.
3. **History candidates** — previously visited directories that contain the typed text as a case-insensitive substring (e.g. typing `fora` can surface `D:\Project\fora-x`).

Successful navigation from the Go To Directory dialog saves the destination to a local MRU list (max 50 entries, stored at `%APPDATA%/FORA-X/Settings/nav_history.json`). Only valid directories are saved; no secrets are stored.

### Favorites

Favorites are core navigation data, not plugins. They are stored as ids, labels,
and paths at
`%APPDATA%/FORA-X/Settings/favorites.json`. They are not validated at startup.
Command Palette favorite entries are generated dynamically, for example
`★ FORA-X  D:\Project\fora-x`, and selecting one navigates only the active pane.
Missing favorite paths show a status message instead of crashing.

Favorites are deduplicated by normalized path and sorted deterministically by
`label.casefold()`, then normalized path. Manual reorder is intentionally not
implemented. History remains a separate MRU list; the Go To Directory prompt
orders suggestions as current-directory matches, favorites, then history.

### Optional Navigation Tree Pane

The default startup UI remains the normal dual-pane layout with no tree pane.
Use `toggle_navigation_tree_pane` or the compatible `toggle_navigation_sidebar`
command from the Command Palette, or `Ctrl+B`, to create/show the optional left
Navigation Tree Pane. Showing the tree reveals/selects the current active file
pane path and keeps focus in the file pane. Use `focus_navigation_tree` or
`Ctrl+Shift+B` to show/focus it directly.

When visible, `Tab` / `Shift+Tab` cycles keyboard focus through Tree, left file
pane, and right file pane. The tree contains favorites and available Windows
drive roots. Folder children are loaded only when a node is expanded, and only
direct child directories are enumerated.

First-pass tree actions:

- `Enter`: navigate the last active file pane to the selected folder.
- `Space`: expand/collapse the selected tree item.
- `C` / `copy_tree_selected_path`: copy selected folder path.
- `F` / `add_tree_selected_to_favorites`: add selected folder to Favorites.
- `R` / `reveal_tree_selected_in_explorer`: reveal selected folder in Explorer.
- `Esc`: return focus to the last active file pane.

Tree Delete, Move, Copy-to-pane, Rename, and New Folder operations are
intentionally not exposed in this pass. The `tree_view` command remains a text
report/export command and is distinct from the Navigation Tree Pane.

## Builtin Command Inventory

| Command id | Description | Args | Category | Source |
| --- | --- | --- | --- | --- |
| `go_to_drive` | Navigate active pane to a drive if available. | `{ "drive": "C" }` | navigation | builtin |
| `go_to_current_directory` | Show Go To Directory prompt (`F3` or `Alt+D`). | - | navigation | builtin |
| `go_parent` / `go_up` | Navigate active pane to parent. | - | navigation | builtin |
| `go_back` | Navigate pane history back. | - | navigation | builtin |
| `go_forward` | Navigate pane history forward. | - | navigation | builtin |
| `reload_pane` | Reload active pane. | - | navigation | builtin |
| `switch_pane` | Switch active pane. | - | navigation | builtin |
| `go_to_path` | Prompt for an explicit path. | - | navigation | builtin |
| `open_selected` | Open selected file or directory. | - | navigation | builtin |
| `enter_focused_directory` | Enter selected directory only. | - | navigation | builtin |
| `reveal_in_explorer` | Reveal selected path in Explorer. | - | external | builtin |
| `show_command_palette` / `command_palette` | Open Command Palette. Supports token-based fuzzy search. | - | command | builtin |
| `show_command_catalog` | Show currently registered command ids and categories. | - | command | builtin |
| `export_command_catalog` | Export command catalog JSON and Markdown. | - | command | builtin |
| `open_command_catalog_folder` | Open `%APPDATA%/FORA-X/CommandCatalog`. | - | command | builtin |
| `enter_tool_mode` | Enter a configured Tool Mode by `mode_id`. | `{ "mode_id": "export" }` | command | builtin |
| `reload_tool_modes` | Reload `%APPDATA%/FORA-X/Settings/tool_modes.json`. | - | settings | builtin |
| `toggle_navigation_sidebar` | Create/show or hide the optional left Navigation Sidebar. | - | navigation | builtin |
| `focus_navigation_sidebar` | Show and focus the optional Navigation Sidebar. | - | navigation | builtin |
| `toggle_navigation_tree_pane` | Create/show or hide the optional left Navigation Tree Pane. | - | navigation | builtin |
| `focus_navigation_tree` | Show and focus the optional Navigation Tree Pane. | - | navigation | builtin |
| `go_to_selected_tree_folder` | Navigate the last active file pane to the selected tree folder. | - | navigation | builtin |
| `copy_tree_selected_path` | Copy the selected tree folder path. | - | navigation | builtin |
| `add_tree_selected_to_favorites` | Add the selected tree folder to Favorites. | - | navigation | builtin |
| `reveal_tree_selected_in_explorer` | Reveal the selected tree folder in Explorer. | - | navigation | builtin |
| `add_current_directory_to_favorites` | Add the active pane directory to favorites with a label prompt. | - | navigation | builtin |
| `remove_current_directory_from_favorites` | Remove the favorite matching the active pane directory. | - | navigation | builtin |
| `rename_favorite` / `edit_favorite` | Rename a saved favorite label. | - | navigation | builtin |
| `open_favorites_settings` | Open `favorites.json`. | - | settings | builtin |
| `reload_favorites` | Reload favorites and dynamic Command Palette entries. | - | navigation | builtin |
| `show_favorites` / `list_favorites` | Show saved favorite labels and paths. | - | navigation | builtin |
| `show_about` | Show About dialog. | - | view | builtin |
| `toggle_hidden_files` | Toggle hidden files. | - | view | builtin |
| `toggle_sort_order` | Toggle sort order. | - | view | builtin |
| `filter_pane` | Prompt and apply active pane filter. | - | filter | builtin |
| `filter_by_name` | Prompt and apply Name column filter on the active pane. | - | filter | builtin |
| `filter_by_type` | Prompt and apply Type column filter on the active pane. | - | filter | builtin |
| `filter_by_size` | Prompt and apply Size column filter on the active pane. | - | filter | builtin |
| `filter_by_modified` | Prompt and apply Modified column filter on the active pane. | - | filter | builtin |
| `clear_column_filters` | Clear active pane column filters. | - | filter | builtin |
| `clear_pane_filter` | Clear active pane filter. | - | filter | builtin |
| `git_commit` | Launch TortoiseGit Commit for selected paths or current directory. | - | git | builtin |
| `git_log` | Launch TortoiseGit Log for selected paths or current directory. | - | git | builtin |
| `git_diff` | Launch TortoiseGit Diff for selected paths or current directory. | - | git | builtin |
| `git_status` | Launch TortoiseGit repository status for selected paths or current directory. | - | git | builtin |
| `git_add` | Launch TortoiseGit Add for selected paths or current directory. | - | git | builtin |
| `git_revert` | Launch TortoiseGit Revert for selected paths or current directory. | - | git | builtin |
| `git_pull` | Launch TortoiseGit Pull for selected paths or current directory. | - | git | builtin |
| `git_push` | Launch TortoiseGit Push for selected paths or current directory. | - | git | builtin |
| `focus_left_pane` | Focus left pane. | - | navigation | builtin |
| `focus_right_pane` | Focus right pane. | - | navigation | builtin |
| `create_directory` / `create_folder` | Create folder in active pane. | - | file operation | builtin |
| `create_and_edit_file` | Create file and open it. | - | file operation | builtin |
| `rename_selected` / `rename` | Rename selected item. | - | file operation | builtin |
| `copy_selected_to_other_pane` | Copy selected item(s) to inactive pane. | - | file operation | builtin |
| `move_selected_to_other_pane` | Move selected item(s) to inactive pane. | - | file operation | builtin |
| `copy_to_right_pane` | Copy selected item(s) to right pane. | - | file operation | builtin |
| `copy_to_left_pane` | Copy selected item(s) to left pane. | - | file operation | builtin |
| `move_to_right_pane` | Move selected item(s) to right pane. | - | file operation | builtin |
| `move_to_left_pane` | Move selected item(s) to left pane. | - | file operation | builtin |
| `open_terminal` | Open configured terminal profile in active path. | `{ "profile": "default" }` | external | builtin |
| `open_folder_in_vscode` | Open active path in VS Code when `code` exists. | - | external | builtin |
| `copy_paths_to_clipboard` | Copy selected paths. | - | clipboard | builtin |
| `copy_names_to_clipboard` | Copy selected names. | - | clipboard | builtin |
| `clipboard_mode` | Enter Clipboard Mode (`Ctrl+Shift+C`). Exposes C/P/N/R/L/M clipboard actions. | - | clipboard | builtin |
| `copy_file_text_to_clipboard` | Copy text content of selected file to clipboard. Decodes UTF-8 and CP949. Rejects folders, binary files, and files over 2 MB. Also available as `C` inside Clipboard Mode. | - | clipboard | builtin |
| `copy_selected_relative_path` | Copy the relative path of the selected item (relative to active pane directory). Single selection only. | - | clipboard | builtin |
| `copy_selected_markdown_link` | Copy a Markdown link `[name](<rel-path>)` for the selected file. Single selection only. | - | clipboard | builtin |
| `compress_selected` | Open compression dialog. | - | archive | builtin |
| `compress_selected_to_zip` | Compress selected item(s) to ZIP. | - | archive | builtin |
| `compress_selected_to_7z` | Compress selected item(s) to 7z when provider exists. | - | archive | builtin |
| `compress_each_selected` | Placeholder for per-item compression. | - | archive | builtin |
| `extract_here` | Extract selected archive(s) into sibling folders. | - | archive | builtin |
| `extract_to_folder` | Prompt and extract archive(s) to a folder. | - | archive | builtin |
| `tree_view` | Write `tree /a` report and open it. | - | external/report | builtin |
| `file_list` | Write `dir /s /o /b` report and open it. | - | external/report | builtin |
| `flat_view` | Planned flat view placeholder. | - | view | builtin |
| `delete_selected_to_recycle_bin` | Delete selected item(s) through Recycle Bin path when supported. | - | file operation | builtin |
| `delete_selected_permanently` | Permanent delete with explicit confirmation. | - | file operation | builtin |
| `show_properties` / `show_explorer_properties` | Show simple properties dialog. | - | info | builtin |
| `list_plugins` | List discovered/loaded plugins. | - | plugin | builtin |
| `list_plugin_permissions` | Show declared permissions for loaded plugins. | - | plugin | builtin |
| `inspect_installed_plugin` | Inspect an installed plugin folder and loaded metadata. | - | plugin | builtin |
| `reload_plugins` | Reload plugins. | - | plugin | builtin |
| `install_plugin_from_folder` | Install plugin folder into FORA-X user plugins. | - | plugin | builtin |
| `import_fman_plugins` | Import selected user/third-party fman plugin folder. | - | plugin | builtin |
| `remove_plugin` | Remove installed FORA-X plugin under app data. | - | plugin | builtin |
| `open_user_plugin_folder` | Open FORA-X user plugin folder. | - | plugin | builtin |
| `open_plugin_folder` | Open the user plugin folder. | - | plugin | builtin |
| `open_third_party_plugin_folder` | Open FORA-X third-party plugin folder. | - | plugin | builtin |
| `open_settings_folder` | Open settings folder. | - | settings | builtin |
| `export_profile` | Export workflow settings to `.fora-profile`. | - | profile | builtin |
| `import_profile` | Validate, back up, import, and reload a `.fora-profile`. | - | profile | builtin |
| `inspect_profile` | Inspect profile manifest/files without importing. | - | profile | builtin |
| `open_profile_export_folder` | Open `%APPDATA%/FORA-X/Profiles`. | - | profile | builtin |
| `open_profile_backup_folder` | Open `%APPDATA%/FORA-X/Backups`. | - | profile | builtin |
| `forge_theme_from_template` | Generate a deterministic `.fora-theme` draft. | - | forge | builtin |
| `forge_profile_from_current_settings` | Generate a `.fora-profile` draft from current settings. | - | forge | builtin |
| `forge_plugin_prefix_rename_template` | Generate a safe sample `.fora-plugin` draft. | - | forge | builtin |
| `forge_plan_from_text` | Generate a FORA Plan draft from intent text. | - | forge/plan | builtin |
| `create_file_plan_from_natural_language` / `fora_plan_from_natural_language` | Create a validated FileOperationPlan draft artifact from natural language and preview it without execution. | - | plan/forge | builtin |
| `generate_forge_artifact_with_ai` | Generate a Forge draft with an optional configured AI provider. | - | forge/ai | builtin |
| `generate_plugin_with_ai` | Generate a plugin draft through Forge AI provider flow. | - | forge/ai | builtin |
| `generate_theme_with_ai` | Generate a theme draft through Forge AI provider flow. | - | forge/ai | builtin |
| `generate_profile_with_ai` | Generate a profile draft through Forge AI provider flow. | - | forge/ai | builtin |
| `generate_plan_with_ai` | Generate a plan draft through Forge AI provider flow and preview it. | - | forge/ai/plan | builtin |
| `open_ai_provider_settings` | Open `ai_providers.json`. | - | forge/ai/settings | builtin |
| `reload_ai_provider_settings` | Reload AI provider settings and show policy/default provider status. | - | forge/ai/settings | builtin |
| `show_ai_policy_status` | Show offline/external policy state and provider list without secret values. | - | forge/ai/settings | builtin |
| `show_ai_provider_diagnostics` | Show provider configured/missing/blocked/selectable status without secret values. | - | forge/ai/settings | builtin |
| `show_ai_env_var_help` | Show Windows environment variable setup guidance for AI provider credentials. | - | forge/ai/settings | builtin |
| `copy_ai_env_var_setup_help` | Copy AI credential setup guidance without API key values. | - | forge/ai/settings | builtin |
| `enable_external_ai` | Confirm and enable external AI provider access without storing API keys. | - | forge/ai/settings | builtin |
| `disable_external_ai` | Disable external AI and switch to offline mode. | - | forge/ai/settings | builtin |
| `set_ai_mode_offline` | Set offline/company mode; no external API calls will be attempted. | - | forge/ai/settings | builtin |
| `set_ai_mode_external_optional` | Confirm and set external-optional mode. | - | forge/ai/settings | builtin |
| `test_ai_provider` | Check selected provider availability without exposing keys or sending file contents. | - | forge/ai/settings | builtin |
| `import_forge_draft_from_json` | Paste/import generated DraftArtifact JSON and validate it. | - | forge | builtin |
| `preview_forge_artifact` | Preview the current Forge draft. | - | forge | builtin |
| `export_forge_artifact` | Export the current Forge draft through Profile/Pack/Plan paths. | - | forge | builtin |
| `install_forge_artifact` | Install/import current Forge draft through safe existing importers. | - | forge | builtin |
| `copy_plugin_generation_prompt` | Copy external-AI plugin generation prompt. | - | forge | builtin |
| `copy_theme_generation_prompt` | Copy external-AI theme generation prompt. | - | forge | builtin |
| `copy_profile_generation_prompt` | Copy external-AI profile generation prompt. | - | forge | builtin |
| `copy_plan_generation_prompt` | Copy external-AI plan generation prompt. | - | forge | builtin |
| `open_forge_artifacts_folder` | Open `%APPDATA%/FORA-X/Forge`. | - | forge | builtin |
| `plan_natural_language_operation` | Prompt for deterministic file operation intent, artifact-preview it, preview, confirm, and execute. | - | plan | builtin |
| `preview_current_plan` | Show dry-run preview for the current plan. | - | plan | builtin |
| `execute_current_plan` | Execute the current validated plan after confirmation. | - | plan | builtin |
| `cancel_current_plan` | Cancel and clear the current plan. | - | plan | builtin |
| `undo_last_plan_transaction` | Preview and undo the latest safe executed FORA Plan transaction. | - | plan/undo | builtin |
| `show_last_plan_transaction` | Show the latest executed FORA Plan transaction log path. | - | plan | builtin |
| `show_plan_history` | Show recent transaction logs. | - | plan | builtin |
| `open_transaction_log_folder` | Open `%APPDATA%/FORA-X/Transactions`. | - | plan | builtin |
| `inspect_bundle` | Inspect a `.fora-plugin` or `.fora-theme` without installing or executing code. | - | pack | builtin |
| `install_bundle` | Validate and install a plugin or theme bundle after confirmation. | - | pack | builtin |
| `list_installed_bundles` | Show installed FORA plugin and theme bundles. | - | pack | builtin |
| `inspect_plugin_bundle` | Inspect a `.fora-plugin` without executing plugin code. | - | pack/plugin | builtin |
| `install_plugin_bundle` | Install a `.fora-plugin` under `%APPDATA%/FORA-X/Plugins/User`. | - | pack/plugin | builtin |
| `list_installed_plugins` | List installed plugin bundle folders. | - | pack/plugin | builtin |
| `remove_installed_plugin` | Remove an installed FORA-X plugin bundle folder. | - | pack/plugin | builtin |
| `inspect_theme_bundle` | Inspect a `.fora-theme` data bundle. | - | pack/theme | builtin |
| `install_theme_bundle` | Install a `.fora-theme` under `%APPDATA%/FORA-X/Themes`, then ask whether to apply it. | - | pack/theme | builtin |
| `list_installed_themes` | List installed theme bundle folders. | - | pack/theme | builtin |
| `remove_installed_theme` | Remove an installed FORA-X theme bundle folder. | - | pack/theme | builtin |
| `apply_theme` | Apply a built-in or installed theme with recovery choices always available. | - | theme | builtin |
| `reset_theme_to_default` | Restore and persist the Built-in Default theme. | - | theme | builtin |
| `apply_default_theme` | Apply and persist the Built-in Default theme. | - | theme | builtin |
| `apply_dark_theme` | Apply and persist the Built-in Dark theme. | - | theme | builtin |
| `open_theme_folder` | Open `%APPDATA%/FORA-X/Themes`. | - | theme | builtin |
| `reload_themes` | Show theme reload/apply guidance. | - | theme | builtin |
| `open_action_bar_settings` | Open `action_bar.json`. | - | settings | builtin |
| `open_terminal_profile_settings` | Open `terminal_profiles.json`. | - | settings | builtin |
| `open_drive_shortcut_settings` | Open `drive_shortcuts.json`. | - | settings | builtin |
| `open_tool_mode_settings` | Open `tool_modes.json`. | - | settings | builtin |
| `open_column_settings` | Open `columns.json`. | - | settings | builtin |
| `open_archive_provider_settings` | Open `archive_providers.json`. | - | settings | builtin |
| `reload_settings` | Reload action bar, columns, archive providers, and key bindings. | - | settings | builtin |
| `reload_action_bar` | Reload action bar settings. | - | settings | builtin |
| `reload_key_bindings` | Reload key bindings. | - | settings | builtin |
| `reload_column_settings` | Reload column settings. | - | settings | builtin |
| `reload_archive_provider_settings` | Reload archive provider settings. | - | settings | builtin |
| `reset_column_settings` | Backup and reset column settings only. | - | settings | builtin |

### Update commands

| Command | Description | Category |
| --- | --- | --- |
| `check_for_updates` | Check for a newer FORA-X release now (manual, ignores interval). | update |
| `install_latest_update` | Same as `check_for_updates` — fetches latest and shows update prompt. | update |
| `open_update_settings` | Open `update_settings.json` in the default editor. | update |
| `reload_update_settings` | Reload update settings from disk. | update |

Plugin and compatibility commands may appear dynamically with source
`plugin_v2` or `compatibility` depending on discovered plugin bundles.

## Audit Notes

- No default `Shift+Delete` permanent-delete binding is present.
- `reset_all_settings_to_defaults` is intentionally not implemented for v0.1.x because user settings must not be deleted silently.
- Some direct key handling is intentionally outside JSON settings to keep pane navigation responsive and compatible with filter editing.

## Font Settings

Open Command Palette with Ctrl+P, then choose Font Setting.

Available actions:
- Change Font: choose a font family with live preview.
- Change Font Size: choose a font size with live preview.
- Change Font Weight: choose Normal, Medium, or Bold if supported.
- Increase Font Size: increase by 1 pt and save immediately.
- Decrease Font Size: decrease by 1 pt and save immediately.
- Reset Font Setting: restore system/default font settings.

In picker views:
- ↑ / ↓: preview the selected value immediately.
- Enter: commit and persist.
- Esc: cancel and restore the previous setting.

Font preferences are stored in ppearance.json under the FORA-X settings directory.
