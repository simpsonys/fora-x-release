# FORA-X Plugin Creation Guide

FORA-X is highly extensible, allowing users to create plugins that add custom commands to the Command Palette, context menu, action bar, and key bindings.

This guide explains how to create a native **FORA Plugin API v2** plugin.

---

## 1. Plugin Folder Structure

A plugin is packaged as a `.fora-plugin` bundle (which is a standard ZIP archive during distribution). During development, it is a folder located in your User Plugins directory:

- Windows: `%APPDATA%/FORA-X/Plugins/User/<plugin-id>/`

A minimal plugin folder structure looks like this:

```text
my-plugin/
├── manifest.json
└── plugin/
    └── my_plugin/
        ├── __init__.py
        └── plugin.py
```

---

## 2. The manifest.json File

Every plugin must include a `manifest.json` file in its root directory. This file declares the plugin metadata, requested permissions, and contributed commands.

Example `manifest.json`:

```json
{
  "schema": "fora.plugin.v2",
  "id": "com.user.hello-world",
  "name": "Hello World Plugin",
  "version": "1.0.0",
  "description": "Adds a simple hello world command to FORA-X",
  "author": "Your Name",
  "entry": "my_plugin.plugin:create_plugin",
  "api": {
    "requires": ["fora.plugin.v2"],
    "min_fora_x": "0.1.11"
  },
  "permissions": [
    "ui.message"
  ],
  "contributes": {
    "commands": [
      {
        "id": "com.user.hello-world.say_hello",
        "title": "Hello World: Say Hello",
        "category": "plugins",
        "permission_required": ["ui.message"]
      }
    ]
  }
}
```

- **id**: A unique dot-separated identifier.
- **entry**: The entry point in format `<package>.<module>:<function>`.
- **permissions**: Declares the APIs the plugin is allowed to call.
- **contributes.commands**: Lists the commands that will appear in FORA-X's Command Palette.

---

## 3. Implementing the Plugin Code

In your `plugin/my_plugin/plugin.py` file, implement the entry point function and command handlers.

```python
def create_plugin(context):
    """
    Called by FORA-X when loading the plugin.
    Use the context to register commands.
    """
    context.register_command(
        id="com.user.hello-world.say_hello",
        title="Hello World: Say Hello",
        handler=say_hello,
        permissions=["ui.message"],
    )

def say_hello(ctx):
    """
    Command handler.
    Receives a CommandContext object.
    """
    ctx.show_message("Hello from your custom FORA-X Plugin!")
```

### Command Context API

Command handlers receive a `CommandContext` object `ctx` that exposes safe APIs:
- `ctx.show_message(message)`: Shows an informational popup.
- `ctx.show_error(message)`: Shows an error popup.
- `ctx.active_pane`: Path of the currently focused pane.
- `ctx.selected_paths`: List of selected file/folder paths in the active pane.
- `ctx.base_path`: Directory path of the active pane.
- `ctx.other_pane_path`: Directory path of the inactive pane.

---

## 4. Registering and Loading the Plugin

1. Put your plugin folder under `%APPDATA%/FORA-X/Plugins/User/`.
2. Open FORA-X.
3. Open the Command Palette (`F1` or `Ctrl+P` / `Ctrl+Shift+P`).
4. Run the command `Command Palette: Reload Commands` or restart the app.
5. Search for `Hello World: Say Hello` in the Command Palette and press **Enter** to run it.

---

## 5. Filesystem Safety Guidelines

When writing plugins that interact with the filesystem:
- **Declare Permissions**: Only request the permissions you actually need (e.g. `fs.read.current_folder`).
- **Do Not Overwrite Unintentionally**: Implement checks before performing write, copy, move, or rename operations.
- **Prefer Dry-Run/Previews**: Use FORA Plan integration if you want the user to preview changes before execution.
- **Handle Edge Cases**: Account for Unicode paths, access denied errors, long paths, and empty selection states.
