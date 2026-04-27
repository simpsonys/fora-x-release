# FORA-X

FORA-X is a fast, Windows-first, keyboard-friendly dual-pane file explorer for users who want quicker file navigation and file operations than the default File Explorer.

## Download

You can find the latest release assets on the [Releases page](https://github.com/simpsonys/fora-x-release/releases).

- **Portable ZIP**: No installation required. Just extract and run `FORA-X.exe`.
- **MSI Installer**: Standard Windows installer that adds FORA-X to your applications.

FORA-X is built specifically for Windows to ensure the best performance and integration with your system.

## First Launch

When you first open FORA-X, you'll see a clean, efficient dual-pane interface:

- **Left and Right Panes**: Two independent file lists side-by-side.
- **Active Pane**: The pane you are currently working in. It is highlighted with a gold/amber border.
- **Path Bar**: Shows the current directory of each pane.
- **Bottom Action Bar**: Quick hints for common commands (F2-F10).
- **Status Bar**: Shows messages about your last action or current state.

**Note**: Most commands and keyboard shortcuts act on the **Active Pane**. Use `Tab` to switch between panes.

## Basic Navigation

FORA-X is designed to keep your hands on the keyboard:

- **Enter** or **Double-Click**: Open the selected file or enter the folder.
- **Right Arrow**: Enter the selected folder.
- **Left Arrow**: Go back in your navigation history.
- **Backspace**: Go to the parent folder (if no search filter is active).
- **Tab**: Switch focus between the left and right panes.
- **F3**: Open the **Go To Directory** prompt. Start typing a path or favorite name and press Enter.
- **Shift + Drive Letter**: Jump directly to a drive root (e.g., `Shift+C` for C:\).
- **Ctrl+B**: Toggle the optional Navigation Sidebar (Folder Tree and Favorites).

## Common File Operations

- **F2**: Rename the selected item.
- **F6**: Create a new empty file.
- **F7**: Create a new folder.
- **Delete**: Move selected items to the Recycle Bin (requires confirmation).
- **Ctrl + Right/Left Arrow**: Copy selected items to the other pane.
- **Alt + Right/Left Arrow**: Move selected items to the other pane.
- **Ctrl+C / Ctrl+V**: Standard clipboard copy and paste.

## Favorites and Fast Access

- **Add Favorite**: Use the Command Palette (`Ctrl+Shift+P`) and type "Add current directory to favorites".
- **Access Favorites**:
  - Open the Navigation Sidebar (`Ctrl+B`) to see your favorites list.
  - Press `F3` and type the name of your favorite.
  - Search for favorites directly in the Command Palette.
- **Manage Favorites**: Favorites are stored locally in your settings folder. You can remove them via the Command Palette or by editing `favorites.json`.

## Command Palette

Press **Ctrl+Shift+P** to open the Command Palette. 

This is the central hub for discovering features. Just start typing what you want to do (e.g., "theme", "plugin", "settings"), and FORA-X will show you matching commands. This is often the fastest way to run advanced tools.

## Bottom Action Bar

The labels at the bottom correspond to your function keys:

- **F2 Rename**: Rename selected.
- **F3 GoTo**: Open navigation prompt.
- **F5 VSCode**: Open current folder in VS Code (if installed).
- **F6 New File**: Create a new file.
- **F7 New Folder**: Create a new folder.
- **F8 Flat**: Toggle Flat View (displays files from subfolders in one list).
- **F9 Terminal**: Open your default terminal in the current folder.
- **F10 Explorer**: Open the current location in Windows File Explorer.

## Built-in Tools

- **Terminal**: Instantly launch PowerShell or CMD in your current path.
- **VSCode**: If you have VS Code installed, jump straight into coding in the current directory.
- **Flat View**: See all files within a folder structure as a single list.
- **Archive/Extract**: Right-click or use the Command Palette to ZIP files or extract archives.
- **Reports**: Generate text-based "Tree" or "List" reports of your directories.

## Keyboard Shortcuts

| Key | Action |
| --- | --- |
| `Tab` | Switch active pane |
| `Enter` | Open / Enter folder |
| `Right` | Enter folder |
| `Left` | Go back |
| `Backspace` | Go up to parent |
| `F2` | Rename |
| `F3` | Go To Directory |
| `F6` | New File |
| `F7` | New Folder |
| `Delete` | Delete to Recycle Bin |
| `Ctrl+Right/Left` | Copy to other pane |
| `Alt+Right/Left` | Move to other pane |
| `Ctrl+B` | Toggle Navigation Sidebar |
| `Ctrl+Shift+P` | Open Command Palette |
| `Ctrl+Z` | Undo last operation |

## Settings and Data Location

FORA-X stores your personal configuration in:
`%APPDATA%\FORA-X\Settings`

- `key_bindings.json`: Customize your shortcuts.
- `favorites.json`: Your saved locations.
- `action_bar.json`: Customize the bottom buttons.

You can quickly open this folder by typing "Open settings folder" in the Command Palette.

## Advanced Usage

For developers and power users, FORA-X supports advanced extensibility:

- [User Guide](./docs/USER_GUIDE.md)
- [Advanced Features & Customization](./docs/ADVANCED_USAGE.md)
- [Command & Keybinding Reference](./docs/COMMANDS_AND_KEYBINDINGS.md)
- [Plugin API Documentation](./docs/FORA_PLUGIN_API_V2.md)
- [Theme Creation](./docs/THEME_SYSTEM.md)

## Troubleshooting

- **Windows SmartScreen**: As FORA-X is a new application, Windows might show a warning. Click "More info" and "Run anyway".
- **VS Code button**: Ensure `code` is in your system PATH for this button to work.
- **Settings Issues**: If your settings become corrupted, you can delete the `Settings` folder in AppData to reset FORA-X to defaults.
- **Public Repository**: This repository is for releases and documentation. Source code is maintained privately.

---
[FORA-X Documentation Index](./docs/ARCHITECTURE.md)