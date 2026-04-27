# FORA-X Architecture

FORA-X is a Windows-first, keyboard-first, dual-pane file explorer. The current
line has grown from the initial PySide6 shell into a v0.1.x baseline with
Profile, Pack, Plan, Forge, Plugin API v2, and optional Forge AI provider
foundations moving toward v0.2 stabilization.

FORA-X is not a fork of fman and is not affiliated with fman. Any local
`reference/fman` checkout is behavioral reference only. fman Core source and
plugin source must not be copied into FORA-X.

## Layers

- `fora.app`: Qt application, main window, command palette, key loading.
- `fora.panes`: pane model and pane widget.
- `fora.fs`: URL conversion, local filesystem helpers, operation entry points.
- `fora.commands`: command registry and built-in commands.
- `fora.plugins`: discovery, loader, and isolated fman compatibility bridge.
- `fora.plugins.api_v2`: native Plugin API v2 manifest validation, controlled
  registration, contribution metadata, and permission checks on official APIs.
- `fora.profile`: data-only workflow profile export/import.
- `fora.pack`: offline plugin/theme bundle inspection and installation.
- `fora.plan`: typed file operation planning, validation, preview, execution,
  and transaction logging.
- `fora.themes`: theme loading and Qt stylesheet compilation.
- `fora.forge`: deterministic and optional AI-assisted draft artifact
  generation routed through validation.
- `fora.guard`: dry-run and safety primitives.
- `fora.infra`: paths, logging, and simple configuration.

The long-term FORA native plugin API is the primary extension model. fman
compatibility remains best-effort and isolated.
