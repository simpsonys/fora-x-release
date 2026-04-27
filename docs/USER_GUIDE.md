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

### Fast Directory Hopping (F3)
Press **F3** to open the "Go To Directory" prompt. 
- Type a few letters of a folder name.
- Use **Up/Down** to select from the suggestions.
- Press **Tab** to complete a folder name.
- Press **Enter** to jump there.

### Drive Jumping
If you have multiple drives (C:, D:, etc.), hold **Shift** and press the drive letter to jump to its root instantly. For example, `Shift+D` takes you to `D:\`.

### Type-to-Filter
Just start typing while a file list is focused. FORA-X will instantly hide files that don't match your text. Press **Esc** to clear the filter.

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
- They appear in the **Go To Directory (F3)** suggestions.
- They are listed in the **Navigation Sidebar (Ctrl+B)**.

---

## Navigation Sidebar

Press **Ctrl+B** to toggle the sidebar.

- **Favorites**: Quick links to your saved folders.
- **Drives**: One-click access to all your connected drives.
- **Folder Tree**: Explore your directory structure without leaving the current view. Expanding a folder in the tree does not change your pane until you click on a folder.

---

## Tips for Efficiency

- **Ctrl+Shift+P** is your best friend. Use it to find any command you've forgotten the shortcut for.
- Use **Ctrl+Z** to undo your last move or rename operation if you made a mistake.
- Most dialogs in FORA-X are designed to be dismissed with **Esc** or confirmed with **Enter**.