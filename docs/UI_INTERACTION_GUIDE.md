# FORA-X UI Interaction Guide

FORA-X is keyboard-first and compact by default. Future UI work should follow these rules unless there is a clear product reason to make a view larger.

## Compact Layout Rule

- Controls should take only the height they need.
- File panes consume available space.
- Toolbars and action bars use fixed/content-sized vertical policies.
- Avoid expanding spacers in toolbars.
- Use small margins and spacing.

## Keyboard-First Rule

- Every dialog should support `Enter` and `Escape`.
- Lists should support `Up` and `Down`.
- Command Palette must be navigable without a mouse.
- Text input should keep focus while list selection changes.

## Modifier State Rule

- Ctrl/Alt UI hints must reset on key release.
- Ctrl/Alt UI hints must also reset on focus loss, dialog open, command palette open, or application deactivation.
- If Ctrl and Shift are both held, prefer `ctrl+shift`, then `shift`, then `ctrl`, then `default`.
- If Ctrl and Alt are both held, prefer `ctrl_alt`, then `ctrl`, then `alt`, then `default`.
- `Shift` alone shows dynamic drive-jump hints; `Ctrl+Shift` shows the static `ctrl+shift` Action Bar config (Filter / Git / Palette / Tree).
- Missing `ctrl+shift` defaults are injected at load time so existing user settings receive the hints without reinstalling.
- Modifier state must not leak into the pane filter.

## Action Bar Layout Rule

- Keep Action Bar left-aligned. Do not center-align hint buttons.
- Action Bar must not force the main window to a minimum width equal to all hints combined.
- Each button sets `minimumWidth(0)` so the parent window can resize freely.
- Items carry an optional `priority` field (1=high, 2=medium, 3=low, default=2).
- When horizontal space is insufficient, lower-priority buttons are hidden first.
  High-priority items (Rename, GoTo, New File/Folder, active Tool Mode actions) remain visible.
  Low-priority items (VSCode, Terminal, Explorer, Flat) may be hidden.
- Do not hide buttons below one visible button.
- Action Bar is primarily a hint strip; clickable buttons are a fallback discovery surface.

## Tool Mode Rule

- Tool Modes are JSON-configurable command groups, not hardcoded feature menus.
- A Tool Mode trigger enters a temporary keyboard mode before pane filter routing.
- While a Tool Mode is active, the Action Bar shows only that mode's actions plus `Esc` cancel.
- Action keys execute mapped registered commands and then exit the mode.
- `Esc`, focus loss, modal dialogs, or window deactivation exit the mode.
- Unknown keys are consumed by the active Tool Mode and must not append to the pane filter.
- Default modes include Export (`Ctrl+E`), Filter (`Ctrl+Shift+F`), and Git (`Ctrl+Shift+G`).
- Filter mode provides keyboard access to column filters.
- Git mode uses curated TortoiseGit commands and must not import the full Explorer shell context menu.
- The Command Palette remains the complete command discovery surface.

## Semantic Emoji/Icon Rule

- Use meaningful emoji in titles, actions, and status where it improves scanning.
- Avoid decorative noise.
- Do not sacrifice alignment or readability.
- Prefer a dedicated icon column or stable prefix for file list icons.

## fman-Like GoTo Rule

- `F3` and `Alt+D` should both open Go To Directory.
- Path prompts should show candidates.
- `Up`/`Down` navigates candidates.
- `Tab` completes the selected candidate or common prefix.
- `Enter` navigates to the selected or typed valid directory.
- Candidate ordering should keep direct filesystem/current-directory matches before favorites, then MRU history.

## Active Pane Rule

- Commands must target the active pane captured at invocation time.
- Double-click and `Enter` must not affect the wrong pane.
- Dialogs launched from a pane should continue targeting that pane after focus changes.
- Optional navigation surfaces such as favorites and folder tree navigation must navigate only the active pane.
- The active pane must be obvious at a glance in the dark theme.
- Use an accent border/header for the active pane and a muted border/header for inactive panes.
- Inactive panes should stay readable and interactive, never visually disabled.
- Active selected rows should use the FORA amber/gold accent; inactive selected rows should be dimmer.

## Optional Navigation Sidebar Rule

- The default UI is the existing dual-pane layout; no navigation tree pane should be shown on first launch.
- Create/show the Navigation Tree Pane only after an explicit command such as `toggle_navigation_tree_pane` or compatible `toggle_navigation_sidebar`.
- `Ctrl+B` is a visibility toggle: when showing the tree, reveal/select the current active file pane path without moving focus.
- `focus_navigation_tree` / `Ctrl+Shift+B` shows the tree, reveals the last active file pane path, and moves focus to the tree.
- Tree pane visibility may be persisted after the user toggles it.
- The Navigation Tree Pane must be keyboard-focusable.
- When visible, `Tab` / `Shift+Tab` cycles Tree, left file pane, and right file pane.
- The folder tree must not recursively scan folders, drives, or network paths.
- Folder tree roots should be cheap: favorites and available Windows drive roots.
- Expanding a node may enumerate only direct child directories and must handle access errors quietly.
- Drive root enumeration must not raise on `PermissionError`, `OSError`, or `FileNotFoundError`; inaccessible drives are silently skipped from the root list.
- When expanding a drive that becomes inaccessible after enumeration, show a single child item with the message "Drive X:\ is unavailable or access is denied." and emit a status message; do not crash.
- Current-path reveal should expand only the ancestors needed for the visible route and must not recursively scan.
- First-pass tree actions are Go, Copy Path, Add Favorite, and Reveal.
- Tree Delete, Move, Copy-to-pane, Rename, and New Folder are deferred until their semantics are explicitly designed.

## Favorites Rule

- Favorites are sorted alphabetically by label, then normalized path.
- Favorites and History remain separate data sources.
- History remains most-recent-first.
- Missing favorite paths show a status message and must not crash.
- Manual favorite reorder is intentionally not part of the first pass.

## Theme Recovery Rule

- `apply_theme` must always offer Built-in Default and Built-in Dark, even when
  no installed themes exist.
- Installed themes should be shown with human-readable labels and stable ids,
  for example `FORA Light Test (com.example.theme.light-test)`.
- `install_theme_bundle` installs first and asks whether to apply afterward; it
  must not imply the UI changed unless apply actually succeeded.
- `reset_theme_to_default`, `apply_default_theme`, and `apply_dark_theme` must
  recover the UI without deleting installed themes.
- If a saved installed theme is missing or invalid, startup should fall back to a
  built-in theme and keep the app readable.

## File List Column Rule

- File list columns are configured at `%APPDATA%/FORA-X/Settings/columns.json`.
- Visible columns and order must follow the JSON settings.
- Supported column ids are `icon`, `name`, `size`, `type`, `modified`, `created`, `accessed`, `extension`, `full_name`, and `full_path`.
- Unknown column ids should be ignored safely.
- `full_name` and `full_path` are hidden by default to avoid duplicate Explorer-like views.
- Reload column changes with the `reload_column_settings` command.
- Header context-menu filters must have keyboard command equivalents such as `filter_by_name`, `filter_by_type`, `filter_by_size`, `filter_by_modified`, and `clear_column_filters`.

## Arrow Navigation Rule

- `Right Arrow` (plain, no modifier) enters the currently focused directory in the active pane. If the focused item is a file, shows a brief status and does nothing.
- `Left Arrow` (plain, no modifier) navigates back in the active pane's history. If no history exists, shows a status and does nothing.
- `Alt+Left` and `Alt+Right` move focus between panes (unchanged).
- `Ctrl+Left` and `Ctrl+Right` copy to panes (unchanged).
- Arrow navigation does not trigger while any text input widget, dialog, or Command Palette has focus.

## Delete Key Rule

- `Delete` (plain, no modifier) triggers `delete_selected_to_recycle_bin` when focus is in a file pane.
- If no items are selected, shows a status message and does nothing.
- A confirmation dialog must appear before any deletion.
- Items are moved to the Windows Recycle Bin via `send2trash`, not permanently deleted.
- `Shift+Delete` is intentionally unbound to avoid accidental permanent deletion.

## Mouse Selection Rule

- Full-row selection is expected file-pane behavior.
- File panes use `ExtendedSelection` mode.
- **Plain click**: selects only the clicked item, resets anchor.
- **Ctrl + click**: toggles the clicked item, preserves other selections.
- **Shift + click**: selects a contiguous range from the anchor to the clicked item using visible/sorted row order.
- Selection state must be preserved across Ctrl/Shift combinations.
- File operations (copy, move, delete) must target all selected items, not just the current/focused row.
- Clicking an inactive pane activates it before applying selection.
- Range selection respects type-to-filter: only visible/filtered rows are included.

## Update Flow Rule

- Automatic update checks run after startup (3 s delay) only if `check_for_updates_on_startup` is `true` in `update_settings.json`.
- Dev/pre-release versions skip automatic checks. Manual checks always run.
- Startup check respects `check_interval_hours`; it does not check again if the interval has not elapsed since `last_checked_at`.
- Network failures during startup checks show a non-fatal status bar message only. They never block the UI or show dialogs.
- When a newer release is found, the update prompt shows current and latest version, a release notes preview, and a release page link.
- **Update**: downloads the MSI to `%APPDATA%/FORA-X/Updates/` and launches `msiexec /i` interactively. Requires final confirmation before download.
- **Later**: dismisses the prompt for this session. The same version is not prompted again until the next startup check or manual `check_for_updates`.
- **Skip this version**: records `skipped_version` in settings. Automatic prompts for that version are suppressed. Manual `check_for_updates` still shows it.
- Portable ZIP users see a guidance message and a link to the release page; no MSI install is attempted.
- Updates are never installed silently. The user must confirm every step.
- The MSI installer runs as a separate process; FORA-X does not close automatically.
- Settings file: `%APPDATA%/FORA-X/Settings/update_settings.json`.

## Type-To-Filter Rule

- Normal typing in a focused file pane filters only that pane.
- Filtering must not trigger from `Ctrl`, `Alt`, `Meta`, function keys, arrows, or text input widgets.
- `Esc` clears the pane filter.
- `Backspace` edits the filter while filter text exists; otherwise it can perform normal parent navigation.
- Filtering should keep the full directory listing in the model and derive visible entries from it.
- Range selection (Shift+click) after filtering uses the filtered visible order.
