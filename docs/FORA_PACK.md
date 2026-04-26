# FORA Pack v1

FORA Pack is the shareable bundle layer for FORA-X. It complements `.fora-profile`:

- `.fora-profile`: data-only workflow settings.
- `.fora-plugin`: installable plugin bundle that may contain Python code.
- `.fora-theme`: data-only theme bundle.

FORA-X is not a fork of fman. fman compatibility remains best-effort and isolated; plugin bundles must not copy fman Core source.

## Bundle Principles

- Bundles are ZIP files with FORA-specific extensions.
- Every bundle must contain `manifest.json`.
- Inspection never executes Python, shell, or plugin code.
- Installation never executes Python, shell, or plugin code.
- Plugin code may run only later through explicit `reload_plugins` or app restart.
- Installation writes only under `%APPDATA%/FORA-X`.
- Zip-slip, absolute paths, and `..` traversal paths are rejected.
- Theme bundles are data-only and reject executable file types.

## Commands

- `inspect_bundle`: inspect `.fora-plugin` or `.fora-theme`.
- `install_bundle`: install either bundle type after confirmation.
- `list_installed_bundles`: show installed plugin and theme bundle folders.
- `inspect_plugin_bundle`, `install_plugin_bundle`, `list_installed_plugins`, `remove_installed_plugin`.
- `inspect_theme_bundle`, `install_theme_bundle`, `list_installed_themes`, `remove_installed_theme`.
- `apply_theme`: apply an installed theme when the current theme runtime can load it.
- `open_user_plugin_folder`, `open_theme_folder`, `reload_plugins`, `reload_themes`.

## Automated Verification

Use `scripts/verify_fora_pack.ps1` to automate Pack bundle checks without
writing to the real `%APPDATA%\FORA-X` install roots. The verifier inspects
provided or discovered `.fora-theme` and `.fora-plugin` bundles, installs and
removes them under a temporary FORA-X app data root, generates malicious fixture
bundles, and verifies plugin code is not imported during inspect/install.

See [FORA_PACK_VERIFICATION.md](FORA_PACK_VERIFICATION.md).

## Install Flow

```text
select bundle
  -> read manifest.json
  -> detect schema and extension
  -> validate manifest
  -> validate zip paths
  -> show metadata, permissions, warnings
  -> user confirms
  -> extract to temporary staging directory
  -> copy into FORA-X app data
  -> show reload/restart guidance
```

## Security Model

`.fora-plugin` bundles can contain executable Python files, so FORA-X treats them as code packages. The installer only copies files after validation; it does not import modules.

FORA Pack understands both legacy `fora.plugin.v1` manifests and native
`fora.plugin.v2` manifests. v2 manifests reject unknown permissions during
inspection/install and show high-risk permission warnings. Runtime enforcement
is applied later when the installed plugin is loaded through Plugin API v2.

Generated plugin sandbox tests exercise this boundary with temporary
`.fora-plugin` bundles: inspect/install must not import plugin modules, and
controlled test plugins are imported only through explicit Plugin API v2 load
tests. This confirms FORA Pack's non-execution contract, but it does not provide
complete OS-level sandboxing for plugin code after load.

Permissions in `.fora-plugin` v1 are declared metadata. High-risk permissions
are highlighted, but v1 remains compatibility-oriented.

High-risk permission examples:

- `fs.write.anywhere`
- `process.spawn`
- `network.http`
- `clipboard.read`

`.fora-theme` bundles are stricter. They are data-only and reject executable file types such as `.py`, `.exe`, `.bat`, `.cmd`, `.ps1`, `.dll`, and `.pyd`.

## Future

- `.fora-plugin` export tools.
- `.fora-theme` export tools.
- Unified `.fora-pack` collections.
- FORA Forge templates and pasted JSON can generate `.fora-plugin`, `.fora-theme`, and `.fora-profile` drafts that install through FORA Pack/Profile validation.
- Stronger sandboxing or subprocess isolation beyond Plugin API v2 API-surface enforcement.
