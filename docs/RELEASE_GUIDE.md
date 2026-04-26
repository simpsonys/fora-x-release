# FORA-X Release Guide

Current v0.1.x repository version evidence: `0.1.21` in `pyproject.toml`.

The repository already has tags through `v0.1.21`. New release-readiness
changes should be published as a later explicit tag instead of reusing or
downgrading any existing tag.

## Why tag-based releases?

Releases are **not** created on every push to `main`.  
Only a version tag push (`v*`) triggers the full release workflow.

This prevents accidental releases from WIP commits, keeps CI fast, and ensures
every published artifact is intentionally versioned.

| Trigger | Workflow | Result |
|---------|----------|--------|
| Push to `main` | `ci.yml` | Tests + ruff only |
| Pull request to `main` | `ci.yml` | Tests + ruff only |
| Push tag `v0.1.21` or later | `release.yml` | Build -> GitHub Release (in private repo) |
| `workflow_dispatch` | `release.yml` | Build → workflow artifacts only (in private repo) |
| Push tag `v*` | `public-release.yml` | Build -> Stage -> Publish to public release repo |

See `docs/PUBLIC_RELEASE_STRATEGY.md` for details on the public release workflow.

---

## How to create a release

### Private Release (for internal testing)

This creates a release inside the private `simpsonys/fora-x` repository.

```powershell
# 1. Make sure main is clean and tested
git checkout main
git pull origin main

# 2. Create the release tag for the approved target version
git tag v0.1.23

# 3. Push main and the tag separately
git push origin main
git push origin v0.1.23
```

The `release.yml` workflow will then build and attach artifacts to a new GitHub Release in this repository.

### Public Release (for distribution)

This publishes sanitized artifacts and documentation to the public `simpsonys/fora-x-release` repository.

**Prerequisites:**
- The public repository `simpsonys/fora-x-release` must exist.
- The `PUBLIC_RELEASE_REPO_TOKEN` secret must be configured in this repository.

See `docs/PUBLIC_RELEASE_STRATEGY.md` for one-time setup instructions.

To publish, push a version tag:
```powershell
# (after ensuring main is ready)
git tag v0.2.1
git push origin v0.2.1
```

The `public-release.yml` workflow will then:
1. Run tests and build artifacts.
2. Stage only allowlisted files using `scripts/stage_public_release.ps1`.
3. Push the staged files to the public repository.
4. Create a new GitHub Release in the public repository with the artifacts.

---

## Artifact names

| Artifact | Pattern |
|----------|---------|
| Portable ZIP | `FORA-X-<version>-portable-win64.zip` |
| MSI installer | `FORA-X-<version>-win64.msi` |

Example for `v0.1.21`:
- `FORA-X-0.1.21-portable-win64.zip`
- `FORA-X-0.1.21-win64.msi`

---

## Local build

### Prerequisites

```powershell
# Install uv (if not already installed)
winget install --id astral-sh.uv -e

# Sync dependencies (includes pyinstaller)
uv sync
```

### Portable build

```powershell
# Full build: tests + ruff + portable ZIP
.\scripts\build_windows.ps1

# Skip tests and lint
.\scripts\build_windows.ps1 -SkipTests -SkipRuff

# Override version
.\scripts\build_windows.ps1 -Version 0.2.1-dev
```

Output: `dist\FORA-X-<version>-portable-win64.zip`

Unzip to any folder, run `FORA-X.exe` directly — no installation required.

### MSI build

Requires [WiX Toolset v3](https://wixtoolset.org/releases/) or:

```powershell
choco install wixtoolset -y
```

Then build:

```powershell
# Full build including MSI
.\scripts\build_windows.ps1

# MSI only (skip tests and ruff, use existing portable build)
.\scripts\build_windows.ps1 -SkipTests -SkipRuff
```

If WiX is not installed, the script prints a warning and skips the MSI step
without failing.  Use `-SkipMsi` to explicitly suppress the warning.

Output: `dist\FORA-X-<version>-win64.msi`

---

## How to test the portable build

1. Unzip `FORA-X-<version>-portable-win64.zip` to any folder.
2. Run `FORA-X.exe`.
3. Verify the dual-pane window opens.
4. Navigate a folder, press `Ctrl+Shift+P` to open the command palette.
5. Check that no console window appears (windowed build).

---

## How to test the MSI installer

1. Double-click `FORA-X-<version>-win64.msi`.
2. Follow the installer wizard.
3. Launch FORA-X from the Start Menu shortcut.
4. Verify the app works.
5. Test uninstall via Settings → Apps.

---

## DevToolFora.ps1 shortcuts

The developer tool provides a `[ RELEASE ]` menu section (options 23–26):

| Option | Action |
|--------|--------|
| 23 | Build portable ZIP locally |
| 24 | Build MSI locally |
| 25 | Build all release artifacts |
| 26 | Show release command help |

---

## Troubleshooting

### uv not found

```
[ERROR] uv not found. Install: winget install --id astral-sh.uv -e
```

Install uv and reopen PowerShell.

### PyInstaller failure

Common causes:
- `uv sync` was not run first → run `uv sync`
- Missing PySide6 Qt plugins → check hidden imports in `packaging/FORA-X.spec`
- Path encoding issue → check the PowerShell UTF-8 settings at the top of the script

Run with verbose output:
```powershell
uv run pyinstaller packaging/FORA-X.spec --clean --noconfirm --log-level DEBUG
```

### WiX not found

```
[WARN] WiX Toolset not found — MSI build skipped.
```

Install WiX:
```powershell
choco install wixtoolset -y
```
Or download from https://wixtoolset.org/releases/ and ensure the `bin/` folder
is in `PATH`.

### PySide6 packaging issue

If the portable app crashes on launch:
1. Check `dist\FORA-X\` for Qt plugin folders (`platforms\`, `styles\`).
2. If missing, add `collect_all("PySide6")` to `packaging/FORA-X.spec`.
3. Re-run PyInstaller.

### GitHub Actions artifact missing

- Check the Actions run log for the failed step.
- If MSI failed: the ZIP is still uploaded; the MSI step has `continue-on-error: true`.
- Download the workflow artifact from the Actions run summary page.

### Release created without MSI

The release workflow marks the MSI step as `continue-on-error: true`.
If WiX fails in CI, the GitHub Release is still created with the portable ZIP.
The MSI can be built locally and attached manually via the GitHub Release edit UI.

---

## Version resolution order

1. `-Version` parameter passed to `build_windows.ps1`
2. Tag name stripped of `v` prefix (in GitHub Actions release workflow)
3. `version` field from `pyproject.toml`
4. Fallback: `0.0.0`

