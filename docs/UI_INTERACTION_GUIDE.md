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
- If Ctrl and Alt are both held, prefer `ctrl_alt`, then `ctrl`, then `alt`, then `default`.

## Semantic Emoji/Icon Rule

- Use meaningful emoji in titles, actions, and status where it improves scanning.
- Avoid decorative noise.
- Do not sacrifice alignment or readability.
- Prefer a dedicated icon column or stable prefix for file list icons.

## fman-Like GoTo Rule

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

- The default UI is the existing dual-pane layout; no navigation sidebar should be shown on first launch.
- Create/show the Navigation Sidebar only after an explicit command such as `toggle_navigation_sidebar`.
- Sidebar visibility may be persisted after the user toggles it.
- The folder tree must not recursively scan folders, drives, or network paths.
- Folder tree roots should be cheap: favorites and available Windows drive roots.
- Expanding a node may enumerate only direct child directories and must handle access errors quietly.
- Current-path tree sync/highlighting is optional and should remain deferred unless it is cheap.

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

- File panes use `ExtendedSelection` mode.
- **Plain click**: selects only the clicked item, resets anchor.
- **Ctrl + click**: toggles the clicked item, preserves other selections.
- **Shift + click**: selects a contiguous range from the anchor to the clicked item using visible/sorted row order.
- Selection state must be preserved across Ctrl/Shift combinations.
- File operations (copy, move, delete) must target all selected items, not just the current/focused row.
- Clicking an inactive pane activates it before applying selection.
- Range selection respects type-to-filter: only visible/filtered rows are included.

## Type-To-Filter Rule

- Normal typing in a focused file pane filters only that pane.
- Filtering must not trigger from `Ctrl`, `Alt`, `Meta`, function keys, arrows, or text input widgets.
- `Esc` clears the pane filter.
- `Backspace` edits the filter while filter text exists; otherwise it can perform normal parent navigation.
- Filtering should keep the full directory listing in the model and derive visible entries from it.
- Range selection (Shift+click) after filtering uses the filtered visible order.
