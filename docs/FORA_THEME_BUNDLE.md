# FORA Theme Bundle v1

`.fora-theme` is the FORA-X theme sharing format. It is a ZIP file and must be data-only.

## Structure

```text
ExampleTheme.fora-theme
  manifest.json
  theme.json
  preview.png optional
  README.md optional
  hashes.json optional
```

`hashes.json` is optional. The minimum valid theme bundle contains
`manifest.json` and the configured theme file, usually `theme.json`.

## Manifest

```json
{
  "schema": "fora.theme.v1",
  "id": "com.example.theme",
  "name": "Example Theme",
  "version": "0.1.0",
  "description": "Example FORA-X theme",
  "theme_file": "theme.json",
  "created_by": "FORA-X",
  "created_at": "ISO-8601 timestamp"
}
```

Required fields:

- `schema`: must be `fora.theme.v1`.
- `id`: safe bundle id used for the install folder.
- `name`
- `version`

## Installation

Installed themes go under:

```text
%APPDATA%/FORA-X/Themes/<theme-id>/
```

FORA-X validates `manifest.json`, rejects unsafe paths, verifies that the configured `theme_file` exists and contains a JSON object, then copies theme files into the FORA-X theme folder.

`install_theme_bundle` installs only. After a successful install, FORA-X asks
whether to apply the theme now. Choosing No leaves the current UI unchanged and
the theme can be applied later with `apply_theme`.

## Data-Only Rule

Theme bundles must not contain executable code. The installer rejects executable file types including:

- `.py`
- `.exe`
- `.bat`
- `.cmd`
- `.ps1`
- `.dll`
- `.pyd`

## Applying Themes

Use `apply_theme` from the Command Palette to apply a theme when the current
runtime can load it. The selector always includes:

- Built-in Default
- Built-in Dark

These built-in choices do not depend on `%APPDATA%/FORA-X/Themes`, so they
remain available even when installed themes are missing or invalid.

Recovery commands:

- `reset_theme_to_default`
- `apply_default_theme`
- `apply_dark_theme`

If the saved active theme points to a missing or invalid installed theme,
FORA-X falls back to Built-in Default and keeps the app usable.

Test themes may validate Pack structure and install/apply flow but may not be
production-quality UX. Visual contrast and polish still require manual review.

## Automated Verification

Run `scripts/verify_fora_pack.ps1 -Theme <path>` to validate, install, remove,
and run executable-file rejection checks against a `.fora-theme` bundle in a
temporary app data root. Visual quality still requires manual GUI verification.
