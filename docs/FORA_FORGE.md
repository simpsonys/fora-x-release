# FORA Forge v1

FORA Forge is the generation layer for FORA-X artifacts. It creates drafts, validates them, previews them, and exports them through existing safe FORA paths.

```text
User request
  -> ForgeRequest
  -> DraftArtifact
  -> validation
  -> preview
  -> export/install path
  -> FORA Profile / FORA Pack / FORA Plan
```

FORA Forge v1 does **not** require external AI APIs. It provides deterministic templates and a pasted/generated JSON workflow so users can use any external AI tool manually while FORA-X remains AI-optional at runtime.

FORA Forge AI Provider Adapters v1 add an optional provider path. The default
provider is a deterministic offline `mock` provider. The global provider policy
defaults to `offline`, so external network providers are disabled before any
prompt, request, provider, or HTTP client is constructed. Provider output still
becomes only a DraftArtifact and must pass the same validation pipeline.

## Core Principles

- Forge generates artifacts, not unchecked runtime actions.
- AI is optional and not required at runtime.
- External AI provider access is optional, policy-gated, and disabled by
  default.
- Generated profiles are data-only.
- Generated themes are data-only.
- Generated plugins may contain Python code but are never executed during generation, validation, packaging, inspection, or installation.
- Generated file operation intents become FORA Plan drafts and must pass dry-run/confirmation.
- No generated artifact bypasses validation.

## Artifact Types

### Profile

`forge_profile_from_current_settings` creates a `.fora-profile` draft from current settings. Export uses the same profile bundle validation path.

### Theme

`forge_theme_from_template` creates a data-only `.fora-theme` draft with `manifest.json`, `theme.json`, and `README.md`.

### Plugin

`forge_plugin_prefix_rename_template` creates a sample `.fora-plugin` draft
targeting FORA Plugin API v2. It uses `PluginContext`, declares permissions, and
hands natural-language intent to `CommandContext.request_file_operation_plan_from_text`
so FORA Plan can parse, validate, permission-check, and preview the requested
file operation. It does not rename files directly. Generated plugin code is
packaged only after validation and is not executed by Forge.

### Plan

`forge_plan_from_text` turns supported intent text into a FORA Plan draft. It
does not execute automatically. The deterministic parser supports conservative
daily-driver intents such as selected-file prefix/suffix rename, selected-file
copy/move to the opposite pane when pane context is available, and simple
create-folder/create-file plans.

## Pasted JSON Workflow

Use `import_forge_draft_from_json` to paste or select a generated DraftArtifact JSON file. FORA-X validates:

- artifact type
- manifest schema
- safe relative paths
- no traversal paths
- no executable files in profile/theme artifacts
- plugin permission declarations and high-risk warnings
- Plugin API v2 permission names when generating plugins
- FORA Plan rejection rules for generated plan intents

## Artifact Preview

Forge preview uses a shared review summary before export, install, import, or
plan handoff. The preview shows artifact type, source, validation status,
safe summary, warnings/errors, and available actions.

Sources are reported as deterministic template, pasted JSON, mock/offline AI,
local-only AI, or external AI. The preview does not show API key values or raw
secret-like manifest values.

Action guidance remains conservative:

- Invalid artifacts can be previewed, rejected, or copied for diagnosis, but
  install/apply/execute paths stay blocked.
- Plan artifacts show operation count, affected paths, and denied/restricted
  operations, then hand off to FORA Plan preview without execution.
- Theme/profile/plugin artifacts show their manifest summary and only proceed
  through existing validated Pack/Profile paths.
- Plugin drafts are disabled by default; install does not load or execute plugin
  code.

## External AI Prompt Workflow

Prompt commands copy strict prompts for external AI tools:

- `copy_plugin_generation_prompt`
- `copy_theme_generation_prompt`
- `copy_profile_generation_prompt`
- `copy_plan_generation_prompt`

The prompts require JSON-only output, no secrets, no execution directives, and the relevant FORA schema.

## Optional AI Provider Workflow

AI provider settings live at `%APPDATA%/FORA-X/Settings/ai_providers.json`.
The settings file stores provider ids, labels, model names, endpoint URLs, and
environment variable names for API keys. It should not store raw API keys.

The top-level policy defaults to:

- `mode: offline`
- `external_ai_enabled: false`
- `allow_network_providers: false`
- `allow_local_http_provider: true`
- `send_file_contents_by_default: false`

With this policy, OpenAI, Anthropic, Gemini, and other external providers are
blocked before request construction. FORA-X does not fall back to `mock`
silently when a selected external provider is blocked. The offline mock,
deterministic templates, copyable prompts, pasted JSON, Forge Plan, Pack, and
Profile flows remain first-class.

Commands:

- `generate_forge_artifact_with_ai`
- `generate_plugin_with_ai`
- `generate_theme_with_ai`
- `generate_profile_with_ai`
- `generate_plan_with_ai`
- `open_ai_provider_settings`
- `reload_ai_provider_settings`
- `show_ai_policy_status`
- `show_ai_provider_diagnostics`
- `show_ai_env_var_help`
- `copy_ai_env_var_setup_help`
- `enable_external_ai`
- `disable_external_ai`
- `set_ai_mode_offline`
- `set_ai_mode_external_optional`
- `test_ai_provider`

Generation sends only the user's request and schema constraints by default. It
does not send selected file contents, full file trees, secrets, credentials, or
local private paths. Generated output is previewed only; export/install/execute
remain separate validated steps.

Provider selection separates Offline / deterministic, Local-only, and External
providers. External providers are not offered as a normal active generation path
while offline policy blocks them. `enable_external_ai` and
`set_ai_mode_external_optional` require explicit user confirmation.

Provider diagnostics and credential help show configured/missing status and
required environment variable names without showing raw API key values.
Availability checks are explicit user actions and stop at policy or missing
credential failures before constructing network providers.

See [FORA_FORGE_AI_PROVIDERS.md](FORA_FORGE_AI_PROVIDERS.md).

## End-to-End Artifact Flows

Forge keeps each generated artifact as a draft until the user chooses the next
validated handoff:

- Theme: generate draft -> preview -> export `.fora-theme` -> inspect with FORA
  Pack -> install through the theme bundle path.
- Profile: generate draft from settings -> preview -> export `.fora-profile` ->
  inspect with FORA Profile -> import with settings backup.
- Plugin: generate Plugin API v2 draft -> preview files and permissions ->
  export `.fora-plugin` -> inspect/install through FORA Pack -> load only on an
  explicit plugin reload. Generation, export, inspection, and installation do
  not import plugin code.
- Plan: generate FORA Plan draft -> preview -> export `.fora-plan.json` or hand
  off to FORA Plan preview. Plans are never executed automatically.

These flows are covered by temp-directory end-to-end tests so Forge can verify
the same safe handoffs without touching the user's real AppData settings.

## Commands

- `forge_theme_from_template`
- `forge_profile_from_current_settings`
- `forge_plugin_prefix_rename_template`
- `forge_plan_from_text`
- `import_forge_draft_from_json`
- `preview_forge_artifact`
- `export_forge_artifact`
- `install_forge_artifact`
- `open_forge_artifacts_folder`
- `copy_plugin_generation_prompt`
- `copy_theme_generation_prompt`
- `copy_profile_generation_prompt`
- `copy_plan_generation_prompt`

## Security Boundary

Forge output is reviewed before use:

- Profiles install through FORA Profile import.
- Themes/plugins install through FORA Pack.
- Plans use FORA Plan validation and preview.

AI providers must produce the same DraftArtifact or FileOperationPlan data,
respect provider policy, and cannot bypass these paths.

Generated plugin tests use temporary `.fora-plugin` bundles to verify that
Forge/Pack inspection and installation do not import plugin code. Controlled
test plugins are imported only during explicit Plugin API v2 load/reload tests,
where permissions and contribution registration are checked.

## Future Work

- Further provider-specific hardening for OpenAI/Claude/Gemini/local LLM
  adapters as upstream APIs evolve.
- Stronger generated plugin isolation beyond the current controlled test
  harness.
- Stronger Plugin API v2 sandboxing beyond official API-surface enforcement.
- Richer templates and marketplace/sharing flows.
