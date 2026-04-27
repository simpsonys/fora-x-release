# FORA-X Advanced Usage

This document covers optional advanced features of FORA-X intended for developers and power users.

## Customization

### Profiles
Profiles allow you to export and import your entire FORA-X setup (shortcuts, favorites, action bar) as a single `.fora-profile` file. This is great for backing up your settings or sharing them between computers.
- [Learn about FORA Profiles](./FORA_PROFILE.md)

### Themes
FORA-X supports a flexible theme system. You can install `.fora-theme` bundles or create your own to change every aspect of the UI's appearance.
- [Theme System Overview](./THEME_SYSTEM.md)
- [How to create Theme Bundles](./FORA_THEME_BUNDLE.md)

---

## Extensibility

### Plugins (v2)
The FORA Plugin API v2 allows developers to add new commands and capabilities to FORA-X. Plugins are packaged as `.fora-plugin` files and can be safely inspected before installation.
- [Plugin API v2 Documentation](./FORA_PLUGIN_API_V2.md)
- [Plugin Bundle Format](./FORA_PLUGIN_BUNDLE.md)

### FORA Pack
FORA Pack is the utility that handles the safe inspection and installation of plugin and theme bundles.
- [Learn about FORA Pack](./FORA_PACK.md)

---

## AI and Automation

### FORA Forge
Forge is a powerful tool for generating plugins, themes, and complex file operation plans. It can work offline using deterministic templates or online via optional AI providers.
- [FORA Forge Overview](./FORA_FORGE.md)
- [Configuring AI Providers](./FORA_FORGE_AI_PROVIDERS.md)

### FORA Plan
FORA Plan allows you to describe complex file operations in natural language. FORA-X then generates a safe, multi-step plan that you can preview and confirm before it executes.
- [How FORA Plan Works](./FORA_PLAN.md)

---

## Administration

### Release Guide
If you are contributing to FORA-X or managing your own fork, refer to the release guide.
- [Release Guide](./RELEASE_GUIDE.md)