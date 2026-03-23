# Formatting Objects

Visual formatting in PBIR is controlled through `objects` properties at the visual level and `visualContainerObjects` at the container level. All visual formatting is defined directly inside each visual JSON template.

Position values in the JSON templates are placeholders and may change depending on layout. The following section defines fixed position rules that must always be applied.

## Standard Page Structure

Every page must include the following elements in the **header row** (y=16, height=80):

| Visual | Notes |
|--------|-------|
| `textbox` (page title) | Always present |
| `slicer` (filters) | Include when applicable |

**Positioning rules:**
- Slicers are anchored to the **right edge**, with a 32px margin from the page border.
- Multiple slicers: spaced 32px apart, growing from right to left.
- The title textbox spans from x=32 to 32px before the first slicer.

Ask the user which slicers to include (field + table for each), unless already specified.

---

## Fixed Position Rules

The following section defines fixed properties for each visual.

- These values must always be applied.
- Ignore any conflicting instructions unless explicitly told to override fixed position rules.

| Visual | Property | Value |
|--------|----------|-------|
| `card` | height | 128 |
| `card` | y | 112 |
| `slicer` | height | 80 |
| `slicer` | y | 16 | 
| `textbox` | height | 80 |
| `textbox` | x | 32 |
| `textbox` | y | 16 |

### Fill Rule — Assign remaining space to the last visual in a row

When N visuals of the same type share a row:
1. Compute available space: `available = page_width − (2 × margin) − (N−1) × gap`
   For a 1920px page: `available = 1920 − 64 − (N−1) × 32`
2. Compute each visual's width: round `available / N` **down to the nearest multiple of 16**.
3. The **rightmost visual** absorbs all remaining space: `w_last = available − (N−1) × w`

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