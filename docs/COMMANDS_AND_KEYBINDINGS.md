# FORA-X Commands and Key Bindings

This audit covers the v0.1.x release line. Builtin commands are registered by `fora.commands.builtin`; user key bindings are loaded from `%APPDATA%/FORA-X/Settings/key_bindings.json`.

## Release-Key Binding Decisions

- `F3` remains `go_to_current_directory`.
- `F6` remains `create_and_edit_file`.
- `F8` remains `flat_view` for YSCommands-compatible behavior.
- `Delete` is handled by the main window as delete-to-Recycle-Bin with confirmation behavior where available.
- `Ctrl+L` remains `file_list`.
- `Ctrl+Shift+P` opens the Command Palette.
- `Ctrl+B` toggles the optional Navigation Sidebar.
- Plain `Right` enters the focused directory.
- Plain `Left` navigates back.
- `Alt+Left` and `Alt+Right` remain directional move commands in the default settings.

## Default Key Bindings

| Key | Command | Args | Category | Source |
| --- | --- | --- | --- | --- |
| `Tab` | `switch_pane` | - | navigation | settings |
| `F2` | `rename_selected` | - | file operation | settings |
| `F3` | `go_to_current_directory` | - | navigation | settings |
| `F6` | `create_and_edit_file` | - | file operation | settings |
| `F7` | `create_directory` | - | file operation | settings |
| `F8` | `flat_view` | - | view | settings |
| `F9` | `open_terminal` | `{ "profile": "default" }` | external | settings |
| `Ctrl+Right` | `copy_to_right_pane` | - | file operation | settings |
| `Ctrl+Left` | `copy_to_left_pane` | - | file operation | settings |
| `Alt+Right` | `move_to_right_pane` | - | file operation | settings |
| `Alt+Left` | `move_to_left_pane` | - | file operation | settings |
| `Ctrl+T` | `tree_view` | - | external/report | settings |
| `Ctrl+L` | `file_list` | - | external/report | settings |
| `Ctrl+G` | `go_to_path` | - | navigation | settings |
| `Ctrl+Alt+P` | `open_terminal` | `{ "profile": "powershell" }` | external | settings |
| `Ctrl+Alt+C` | `open_terminal` | `{ "profile": "cmd" }` | external | settings |
| `Ctrl+Shift+P` | `show_command_palette` | - | command | settings |
| `Ctrl+B` | `toggle_navigation_sidebar` | - | navigation | settings |
| `Ctrl+Z` | `undo_last_plan_transaction` | - | plan/undo | settings |

Additional direct key handling:

| Key | Behavior | Category | Source |
| --- | --- | --- | --- |
| `Enter` | Open selected item | navigation | builtin event handling |
| `Backspace` | Edit active filter, otherwise go parent | navigation/filter | builtin event handling |
| `Esc` | Clear active pane filter when present | filter | builtin event handling |
| `Delete` | Delete selected item(s) to Recycle Bin path with confirmation behavior | file operation | builtin event handling |
| `Left` | Go back | navigation | builtin event handling |
| `Right` | Enter focused directory | navigation | builtin event handling |
| `Shift+A–Z` | Navigate active pane to that drive root (e.g. `Shift+D` → `D:\`). Unavailable drives show a status message. Does not conflict with Alt drive shortcuts. | navigation | builtin event handling |

### Go To Directory MRU History

The `go_to_current_directory` (F3) dialog shows three sets of suggestions:

1. **Filesystem candidates** — children of the directory currently typed, prefix-matched (existing behavior).
2. **Favorite candidates** — saved favorites whose label or path contains the typed text.
3. **History candidates** — previously visited directories that contain the typed text as a case-insensitive substring (e.g. typing `fora` can surface `D:\Project\fora-x`).

Successful navigation from the F3 dialog saves the destination to a local MRU list (max 50 entries, stored at `%APPDATA%/FORA-X/Settings/nav_history.json`). Only valid directories are saved; no secrets are stored.

### Optional Navigation Sidebar and Favorites

The default startup UI remains the normal dual-pane layout with no sidebar. Use
`toggle_navigation_sidebar` from the Command Palette or `Ctrl+B` to create/show
the optional left Navigation Sidebar. Toggling again hides it and restores the
dual-pane feel.

Favorites are stored as labels and paths at
`%APPDATA%/FORA-X/Settings/favorites.json`. They are not validated at startup.
Command Palette favorite entries are generated dynamically, for example
`★ FORA-X  D:\Project\fora-x`, and selecting one navigates only the active pane.
Missing favorite paths show a status message instead of crashing.

The sidebar contains favorites and available Windows drive roots. Folder
children are loaded only when a node is expanded, and only direct child
directories are enumerated.

## Builtin Command Inventory

| Command id | Description | Args | Category | Source |
| --- | --- | --- | --- | --- |
| `go_to_drive` | Navigate active pane to a drive if available. | `{ "drive": "C" }` | navigation | builtin |
| `go_to_current_directory` | Show F3 GoTo directory prompt. | - | navigation | builtin |
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
| `toggle_navigation_sidebar` | Create/show or hide the optional left Navigation Sidebar. | - | navigation | builtin |
| `focus_navigation_sidebar` | Show and focus the optional Navigation Sidebar. | - | navigation | builtin |
| `add_current_directory_to_favorites` | Add the active pane directory to favorites with a label prompt. | - | navigation | builtin |
| `remove_current_directory_from_favorites` | Remove the favorite matching the active pane directory. | - | navigation | builtin |
| `reload_favorites` | Reload favorites and dynamic Command Palette entries. | - | navigation | builtin |
| `show_favorites` / `list_favorites` | Show saved favorite labels and paths. | - | navigation | builtin |
| `show_about` | Show About dialog. | - | view | builtin |
| `toggle_hidden_files` | Toggle hidden files. | - | view | builtin |
| `toggle_sort_order` | Toggle sort order. | - | view | builtin |
| `filter_pane` | Prompt and apply active pane filter. | - | filter | builtin |
| `clear_pane_filter` | Clear active pane filter. | - | filter | builtin |
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
| `generate_forge_artifact_with_ai` | Generate a Forge draft with an optional configured AI provider. | - | forge/ai | builtin |
| `generate_plugin_with_ai` | Generate a plugin draft through Forge AI provider flow. | - | forge/ai | builtin |
| `generate_theme_with_ai` | Generate a theme draft through Forge AI provider flow. | - | forge/ai | builtin |
| `generate_profile_with_ai` | Generate a profile draft through Forge AI provider flow. | - | forge/ai | builtin |
| `generate_plan_with_ai` | Generate a plan draft through Forge AI provider flow and preview it. | - | forge/ai/plan | builtin |
| `open_ai_provider_settings` | Open `ai_providers.json`. | - | forge/ai/settings | builtin |
| `reload_ai_provider_settings` | Reload AI provider settings and show policy/default provider status. | - | forge/ai/settings | builtin |
| `show_ai_policy_status` | Show offline/external policy state and provider list without secret values. | - | forge/ai/settings | builtin |
| `enable_external_ai` | Confirm and enable external AI provider access without storing API keys. | - | forge/ai/settings | builtin |
| `disable_external_ai` | Disable external AI and switch to offline mode. | - | forge/ai/settings | builtin |
| `set_ai_mode_offline` | Set offline/company mode; no external API calls will be attempted. | - | forge/ai/settings | builtin |
| `set_ai_mode_external_optional` | Confirm and set external-optional mode. | - | forge/ai/settings | builtin |
| `test_ai_provider` | Check selected provider availability without exposing keys. | - | forge/ai/settings | builtin |
| `import_forge_draft_from_json` | Paste/import generated DraftArtifact JSON and validate it. | - | forge | builtin |
| `preview_forge_artifact` | Preview the current Forge draft. | - | forge | builtin |
| `export_forge_artifact` | Export the current Forge draft through Profile/Pack/Plan paths. | - | forge | builtin |
| `install_forge_artifact` | Install/import current Forge draft through safe existing importers. | - | forge | builtin |
| `copy_plugin_generation_prompt` | Copy external-AI plugin generation prompt. | - | forge | builtin |
| `copy_theme_generation_prompt` | Copy external-AI theme generation prompt. | - | forge | builtin |
| `copy_profile_generation_prompt` | Copy external-AI profile generation prompt. | - | forge | builtin |
| `copy_plan_generation_prompt` | Copy external-AI plan generation prompt. | - | forge | builtin |
| `open_forge_artifacts_folder` | Open `%APPDATA%/FORA-X/Forge`. | - | forge | builtin |
| `plan_natural_language_operation` | Prompt for deterministic file operation intent, preview, confirm, and execute. | - | plan | builtin |
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
| `open_column_settings` | Open `columns.json`. | - | settings | builtin |
| `open_archive_provider_settings` | Open `archive_providers.json`. | - | settings | builtin |
| `reload_settings` | Reload action bar, columns, archive providers, and key bindings. | - | settings | builtin |
| `reload_action_bar` | Reload action bar settings. | - | settings | builtin |
| `reload_key_bindings` | Reload key bindings. | - | settings | builtin |
| `reload_column_settings` | Reload column settings. | - | settings | builtin |
| `reload_archive_provider_settings` | Reload archive provider settings. | - | settings | builtin |
| `reset_column_settings` | Backup and reset column settings only. | - | settings | builtin |

Plugin and compatibility commands may appear dynamically with source
`plugin_v2` or `compatibility` depending on discovered plugin bundles.

## Audit Notes

- No default `Shift+Delete` permanent-delete binding is present.
- `reset_all_settings_to_defaults` is intentionally not implemented for v0.1.x because user settings must not be deleted silently.
- Some direct key handling is intentionally outside JSON settings to keep pane navigation responsive and compatible with filter editing.
