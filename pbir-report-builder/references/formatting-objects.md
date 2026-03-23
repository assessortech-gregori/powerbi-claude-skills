# Formatting Objects

Visual formatting in PBIR is controlled through `objects` properties at the visual level and `visualContainerObjects` at the container level. All visual formatting is defined directly inside each visual JSON template.

Position values in the JSON templates are placeholders and may change depending on layout. The following section defines fixed position rules that must always be applied.

## Fixed Position Rules

The following section defines fixed properties for each visual.

- These values must always be applied.
- Ignore any conflicting instructions unless explicitly told to override fixed position rules.

| Visual | Property | Value |
|---|---|---|
| `card` | height | 144 |
| `card` | y | 112 |
| `slicer` | height | 80 |
| `slicer` | y | 16 | 
| `textbox` | height | 80 |
| `textbox` | x | 32 |
| `textbox` | y | 16 |

---

## ThemeDataColor — Color Palette

The colors of the theme may vary for each model, but the theme follows an overall pattern:

| ColorId | Use |
|---|---|
| 0 | Default Power BI theme color ( #FFFFFF )
| 1 | Default Power BI theme color ( #000000 )
| 2 | Main color 1
| 3 | Main color 2
| 4 | Main color 3
| 5 | Main color 4
| 6 | Secondary color 1
| 7 | Secondary color 2
| 8 | Standout color 1
| 9 | Standout color 2

Default theme for AssessorTech:

```json
"dataColors": ["#2F6FAE", "#4C8AC4", "#7FAFDC", "#C9DDF2", "#7FBF3F", "#A3D36C", "#4A9F8E", "#6C5CE7"]
```

Bars:
- Use theme colors starting from ColorId 2 (main colors).
- Assign colors sequentially (2, 3, 4, 5...) based on category order.

Lines:
- Use theme colors starting from ColorId 6 (secondary and standout colors)
- Assign colors sequentially (6, 7, 8, 9...) based on category order.