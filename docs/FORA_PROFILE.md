# FORA Profile v1

`.fora-profile` is the first FORA-specific shareable artifact format. It packages personal FORA-X workflow settings so the same keyboard and pane setup can move across machines, including restricted environments where AI access is unavailable.

## Format

Bundle type: `.fora-profile`

- ZIP file.
- Data-only.
- No executable code.
- Must contain `manifest.json`.
- Must not install plugins.
- Must not run Python, shell, or external commands during inspection or import.

Recommended structure:

```text
MyWorkflow.fora-profile
  manifest.json
  settings/
    action_bar.json
    key_bindings.json
    terminal_profiles.json
    drive_shortcuts.json
    columns.json
    archive_providers.json
  themes/
    theme.json optional
  README.md optional
  hashes.json optional
```

## Manifest

```json
{
  "schema": "fora.profile.v1",
  "id": "com.example.my-workflow",
  "name": "My Workflow",
  "version": "0.1.0",
  "description": "Personal FORA-X workflow profile",
  "created_by": "FORA-X",
  "created_at": "2026-04-26T00:00:00+00:00",
  "requires": {
    "fora_x": ">=0.1.21"
  },
  "includes": {
    "settings": [
      "action_bar",
      "key_bindings",
      "terminal_profiles",
      "drive_shortcuts",
      "columns",
      "archive_providers"
    ],
    "theme": false
  }
}
```

`requires.fora_x` is generated from the current FORA-X package version when possible. If version resolution is unavailable, export falls back to a conservative v0.1.x baseline.

## Supported Settings

- `action_bar`
- `key_bindings`
- `terminal_profiles`
- `drive_shortcuts`
- `columns`
- `archive_providers`

Unknown setting ids are ignored with warnings. Unknown unsafe files are rejected.

## Export Flow

Command Palette command: `export_profile`

1. User enters a profile name.
2. User chooses a destination `.fora-profile` path.
3. FORA-X reads current settings from `%APPDATA%/FORA-X/Settings/`.
4. Missing settings are exported from safe built-in defaults.
5. FORA-X writes `manifest.json`, selected `settings/*.json`, optional `README.md`, and `hashes.json`.

Export never includes:

- user plugins
- fman plugins
- `reference/`
- logs
- cache
- backups
- AIHistory
- local `%APPDATA%` data outside supported settings

## Import Flow

Command Palette command: `import_profile`

1. User selects a `.fora-profile` file.
2. FORA-X validates zip paths and `manifest.json`.
3. FORA-X shows a confirmation dialog with name, version, description, and included settings.
4. Current settings are backed up before import.
5. Supported settings are written to `%APPDATA%/FORA-X/Settings/`.
6. FORA-X reloads settings where live reload exists.

Backup location:

```text
%APPDATA%/FORA-X/Backups/YYYYMMDD_HHMM_profile-import/
```

## Inspection

Command Palette command: `inspect_profile`

Inspection reads the manifest and file list without installing settings. It does not execute files.

## Security Model

- `.fora-profile` is data-only.
- No Python code execution.
- No shell command execution.
- No plugin installation.
- Zip-slip protection rejects absolute paths and `..` traversal.
- Manifest validation is required before import.
- Current settings backup is required before import.
- Import writes only under `%APPDATA%/FORA-X/Settings/`.
- Backups write only under `%APPDATA%/FORA-X/Backups/`.
- Unknown unsafe paths are rejected.

## Unsupported in v1

- Plugins are not included.
- Full `.fora-theme` bundles are not included in profiles.
- Theme bundles are handled separately by FORA Pack.
- Per-machine secrets or credentials are not included.

## Future

- unified `.fora-pack`
- optional profile references to installed FORA Pack bundles
- richer FORA Forge profile templates, still offline and data-only after generation
