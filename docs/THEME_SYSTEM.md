# Theme System

Themes are loaded from a theme directory containing `theme.json`. The current schema is deliberately small:

```json
{
  "name": "Graphite",
  "colors": {
    "background": "#181b20",
    "panel": "#22272e",
    "text": "#edf2f7",
    "muted": "#a7b0bc",
    "accent": "#e0b15c"
  }
}
```

The loader converts these colors into a Qt stylesheet at startup. Shareable theme bundles and richer validation are planned for later milestones.
