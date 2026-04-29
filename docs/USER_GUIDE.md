# FORA-X User Guide

Welcome to FORA-X! This guide will help you master the efficient, dual-pane workflow of FORA-X.

## UI Tour

### The Dual-Pane Concept
FORA-X uses a classic dual-pane layout. This means you always have two directories open at once. This makes moving and copying files much faster than opening multiple windows and dragging between them.

### Active vs. Inactive Pane
- **Active Pane**: The pane that has focus. Commands like "New Folder" or "Go To" always apply to the active pane. It is highlighted with a gold border.
- **Inactive Pane**: The "target" for operations like Copy and Move.

### Bottom Action Bar
The buttons at the bottom show you what the Function keys (F2, F3, etc.) do. These labels change when you hold down `Ctrl`, `Alt`, or `Shift`, showing you even more shortcuts.

---

## Navigation Workflows

### Fast Directory Hopping (F3 or Alt+D)
Press **F3** or **Alt+D** to open the "Go To Directory" prompt.
- Type a few letters of a folder name.
- Use **Up/Down** to select from the suggestions.
- Press **Tab** to complete a folder name.
- Press **Enter** to jump there.

### Drive Jumping
If you have multiple drives (C:, D:, etc.), hold **Shift** and press the drive letter to jump to its root instantly. For example, `Shift+D` takes you to `D:\`.

Alt drive shortcuts are not used; `Alt+D` is reserved for Go To Directory.

### Type-to-Filter
Just start typing while a file list is focused. FORA-X will instantly hide files that don't match your text. Press **Esc** to clear the filter.

---

## Tool Modes

Tool Modes are keyboard-first command groups. Press **Ctrl+E** to enter the
default **Export** mode. The Action Bar changes to show:

- **L**: File List report
- **T**: Tree report
- **P**: Copy selected paths
- **N**: Copy selected names
- **Esc**: Cancel

The `tree_view` command creates a text tree report. It is separate from the
optional Navigation Sidebar folder tree.

Press **Ctrl+Shift+F** for **Filter** mode:

- **N**: Filter by Name
- **T**: Filter by Type
- **S**: Filter by Size
- **M**: Filter by Modified
- **C**: Clear column filters
- **Esc**: Cancel

Press **Ctrl+Shift+G** for **Git** mode. Git mode launches curated
TortoiseGit commands such as Commit, Log, Diff, Status, Add, Revert, Pull, and
Push. It does not use the full Windows Explorer context menu.

Power users can edit Tool Modes in
`%APPDATA%\FORA-X\Settings\tool_modes.json`. Use the Command Palette to run
`show_command_catalog` or `export_command_catalog` when you need valid command
ids for custom modes.

---

## Dual-Pane Operations

### Copying Files
1. In the **Active Pane**, select the files you want to copy.
2. Ensure the **Inactive Pane** is at your desired destination.
3. Press **Ctrl + (Arrow towards the other pane)**.
   - If the inactive pane is on the right, press `Ctrl+Right`.
   - If it's on the left, press `Ctrl+Left`.

### Moving Files
Same as copying, but use the **Alt** key instead:
- Press **Alt + (Arrow towards the other pane)**.

---

## Favorites

Favorites are your most-used folders.

### Adding a Favorite
1. Navigate to the folder you want to save.
2. Open the Command Palette (`Ctrl+Shift+P`).
3. Type "Add current directory to favorites" and press Enter.
4. Give it a friendly label.

### Using Favorites
- They appear in the **Go To Directory (F3 / Alt+D)** suggestions.
- They are listed in the optional **Navigation Tree Pane (Ctrl+B)**.
- Favorites are sorted alphabetically by label. Navigation history is separate and sorted most-recent-first.

---

## Navigation Tree Pane

Press **Ctrl+B** to toggle the optional Navigation Tree Pane. It is hidden by default to keep the dual-pane UI uncluttered.

When the tree is shown, it reveals/selects the active file pane path and keeps
focus in the file pane. Use **Ctrl+Shift+B** or the `focus_navigation_tree`
command to move focus into the tree.

- **Favorites**: Quick links to your saved folders.
- **Drives**: One-click access to all your connected drives.
- **Folder Tree**: Explore direct child folders without scanning the full filesystem at startup.

Keyboard actions:

- **Tab / Shift+Tab**: Cycle Tree, left pane, and right pane when the tree is visible.
- **Enter**: Navigate the last active file pane to the selected tree folder.
- **Space**: Expand or collapse the selected tree item.
- **C**: Copy the selected tree folder path.
- **F**: Add the selected tree folder to Favorites.
- **R**: Reveal the selected tree folder in Explorer.
- **Esc**: Return focus to the file panes.

Tree Delete, Move, Copy-to-pane, Rename, and New Folder are intentionally not available in this first pass.

---

## Tips for Efficiency

- **Ctrl+Shift+P** is your best friend. Use it to find any command you've forgotten the shortcut for.
- Use **Ctrl+Z** to undo your last move or rename operation if you made a mistake.
- Most dialogs in FORA-X are designed to be dismissed with **Esc** or confirmed with **Enter**.

## Icon

The FORA-X icon uses an FX/dual-pane mark. This is branding only and does not change functionality.
