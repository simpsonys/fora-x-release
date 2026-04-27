# FORA Forge AI Provider Adapters v1

FORA Forge AI providers are optional adapters that ask an external or local
model to generate Forge draft artifacts. They do not replace Forge validation.
AI output is untrusted and must pass the same Forge, Pack, Profile, and Plan
paths before export, install, import, or execution.

## Settings

Provider settings live at:

```text
%APPDATA%/FORA-X/Settings/ai_providers.json
```

The default file contains no secrets. It enables only the deterministic `mock`
provider and includes disabled entries for `openai`, `anthropic`, `gemini`, and
`local_http`.

Raw API keys should not be written to settings. v1 prefers environment variable
names such as `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, and `GEMINI_API_KEY`.

The same settings file also contains a global policy block:

```json
{
  "policy": {
    "mode": "offline",
    "external_ai_enabled": false,
    "allow_network_providers": false,
    "allow_local_http_provider": true,
    "send_file_contents_by_default": false,
    "require_confirmation_before_network": true
  }
}
```

`offline` is the default company-safe mode. In this mode, OpenAI, Anthropic,
Gemini, and other external network providers are blocked before prompt,
request, provider, or HTTP client construction. FORA-X does not silently fall
back to `mock` when policy blocks a selected provider; it reports a provider
policy error so the user can choose an offline workflow explicitly.

`external_optional` mode is reserved for explicit user configuration. External
providers still require `external_ai_enabled: true`,
`allow_network_providers: true`, an enabled provider entry, and an API key from
an environment variable.

## Providers

- `mock`: deterministic, offline, enabled by default, safe for tests and demos.
- `local_http`: local-only, disabled by default, sends a generic JSON POST only
  when the provider is enabled and `allow_local_http_provider` is true.
- `openai`: disabled by default, uses the OpenAI Responses API when
  `OPENAI_API_KEY` is available.
- `anthropic`: disabled by default, uses the Anthropic Messages API when
  `ANTHROPIC_API_KEY` is available.
- `gemini`: disabled by default, uses the Gemini `generateContent` REST API when
  `GEMINI_API_KEY` is available.

FORA-X works without network access and without any provider API key.

External provider adapters use only Python standard library HTTP calls and are
optional. If policy blocks a provider, a provider is disabled, its API key
environment variable is missing, an endpoint is unavailable, a request times
out, or a response is malformed, FORA-X reports a clear provider error without
exposing raw key values.

Default endpoints:

- OpenAI: `https://api.openai.com/v1/responses`
- Anthropic: `https://api.anthropic.com/v1/messages`
- Gemini: `https://generativelanguage.googleapis.com/v1beta/models/<model>:generateContent`

Each provider may use an explicit `endpoint` override from
`ai_providers.json`, but this should be treated as trusted local configuration.

## Commands

- `generate_forge_artifact_with_ai`
- `generate_plugin_with_ai`
- `generate_theme_with_ai`
- `generate_profile_with_ai`
- `generate_plan_with_ai`
- `open_ai_provider_settings`
- `reload_ai_provider_settings`
- `show_ai_policy_status`
- `enable_external_ai`
- `disable_external_ai`
- `set_ai_mode_offline`
- `set_ai_mode_external_optional`
- `test_ai_provider`

Generation commands create a current Forge draft and show a preview. They do not
export, install, import, reload plugins, or execute plans automatically.
Provider selection is grouped as Offline / deterministic, Local-only, or
External. External providers are omitted from the active selection path while
policy blocks them. Enabling external AI requires an explicit confirmation and
does not store API key values.

## Privacy Boundary

Initial AI requests include only the user's natural-language request and schema
constraints. FORA-X does not send local file contents, selected file contents,
full directory trees, `.env` files, credentials, tokens, or SSH keys by default.
Future file-context sharing must require explicit confirmation.

## Validation Pipeline

```text
AIProviderRequest
  -> provider policy check
  -> provider.generate()
  -> raw text response
  -> provider-specific response normalization
  -> JSON extraction
  -> DraftArtifact conversion
  -> Forge validator
  -> Pack/Profile/Plan validation on export or import path
  -> user review
```

Rules:

- Theme/profile artifacts are data-only and reject executable files.
- Plugins target FORA Plugin API v2 by default and must declare permissions.
- Generated plugin code is never executed during generation, validation,
  packaging, inspection, or installation.
- Plan drafts go through FORA Plan validation and preview; they do not execute
  automatically.
- Delete and recursive plan intents remain unsupported unless a future Plan
  version explicitly adds them.

## Manual Fallback

If providers are disabled, users can still use the existing prompt workflow:

1. Run a `copy_*_generation_prompt` command.
2. Paste the prompt into an external AI tool.
3. Copy the DraftArtifact JSON.
4. Run `import_forge_draft_from_json`.
5. Preview and export/install only through FORA's validated paths.

This supports restricted environments: generate at home, export a bundle, then
inspect/install offline at work without runtime AI access.

## Known Limitations

- Provider behavior depends on external API availability and may require future
  updates if provider request/response formats change.
- There is no generated plugin sandbox in this task.
- AI output remains untrusted even when provider config is valid.
- Local file context is intentionally not sent by default.
- The local HTTP adapter is generic and may need endpoint-specific response
  parsing for non-Ollama-compatible servers.
