# FORA Plugin Bundle

`.fora-plugin` is the FORA-X plugin sharing format. It is a ZIP file and may contain executable Python plugin code, so inspection and installation are deliberately non-executing.

FORA Pack supports legacy `fora.plugin.v1` bundles and native
`fora.plugin.v2` bundles. New native plugins should use Plugin API v2.

## Structure

```text
ExamplePlugin.fora-plugin
  manifest.json
  plugin/
    example_plugin/
      __init__.py
  Key Bindings.json optional
  README.md optional
  LICENSE optional
  hashes.json optional
```

## Manifest

```json
{
  "schema": "fora.plugin.v1",
  "id": "com.example.plugin",
  "name": "Example Plugin",
  "version": "0.1.0",
  "description": "Example FORA-X plugin bundle",
  "author": "unknown",
  "entry": "example_plugin",
  "api": {
    "compatible_with": ["fman.compat.v1"],
    "requires": []
  },
  "permissions": [
    "fs.read.current_folder"
  ],
  "commands": [
    {
      "id": "example_command",
      "title": "Example Command"
    }
  ],
  "created_by": "FORA-X",
  "created_at": "ISO-8601 timestamp"
}
```

Required fields:

- `schema`: must be `fora.plugin.v1`.
- `id`: safe bundle id used for the install folder.
- `name`
- `version`
- `entry`: Python package name under `plugin/`.

## Installation

Installed plugins go under:

```text
%APPDATA%/FORA-X/Plugins/User/<plugin-id>/
```

FORA-X validates the manifest and bundle paths, extracts into a temporary staging directory, then copies the plugin package and optional files into the user plugin folder.

Plugin code is not imported during inspection or installation. To execute an installed plugin, run `reload_plugins` or restart FORA-X.

## Automated Verification

Run `scripts/verify_fora_pack.ps1 -Plugin <path>` to validate, install, remove,
and check non-execution behavior for a `.fora-plugin` bundle in a temporary app
data root. The verifier also creates a controlled marker plugin to prove that
inspection and installation do not import plugin code; explicit Plugin API v2
load is tested separately without GUI when possible.

## Permission Metadata

Permissions are advisory in v1. FORA-X displays missing and high-risk permission warnings during inspection/install, but full enforcement is planned for the FORA native plugin API.

In v2, unknown permissions are rejected and official Plugin API surfaces enforce
declared permissions during command execution and FORA Plan requests. This is
not complete OS-level sandboxing; untrusted Python code still needs review.

See [FORA_PLUGIN_API_V2.md](FORA_PLUGIN_API_V2.md).

## Rejection Rules

FORA-X rejects:

- missing `manifest.json`
- wrong schema
- wrong extension
- unsafe ids
- absolute paths
- `..` traversal paths
- unsupported top-level bundle paths

Plugin executable files are allowed inside the plugin bundle because plugins are code, but they are highlighted as inspection warnings.
