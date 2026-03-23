# Formatting Objects

Visual formatting in PBIR is controlled through `objects` properties at the visual level and `visualContainerObjects` at the container level.

---

## ThemeDataColor — Color Palette

```json
"dataColors": ["#3374BB", "#6898CD", "#95B6DB", "#D5E3F1", "#98C93C", "#BADB7D", "#2B2F38", "#F4A261"]
```

| ColorId | Hex | Uso |
|---|---|---|
| 0 | `#FFFFFF` | Default Power BI theme color
| 1 | `#000000` | Default Power BI theme color
| 2 | `#3374BB` | Main color 1
| 3 | `#6898CD` | Main color 2
| 4 | `#95B6DB` | Main color 3
| 5 | `#D5E3F1` | Main color 4
| 6 | `#98C93C` | Secondary color 1
| 7 | `#BADB7D` | Secondary color 2
| 8 | `#2B2F38` | Standout color 1
| 9 | `#F4A261` | Standout color 2

Bar charts:
- Use theme colors starting from ColorId 2 (main colors).
- Assign colors sequentially (2, 3, 4, 5...) based on category order.

Line charts:
- Use secondary colors (ColorId 6 and 7).
- Use ColorId 6 for the first line and 7 for the second.

### Fixed Interface Colors (always use literal hex, never ThemeDataColor)

| Element | Hex |
|---|---|
| Page background | `#F5F5F5` |
| Visual/card background | `#FFFFFF` |
| Titles and callouts | `#444444` |
| Subtitles and labels | `#666666` |

---

## Typography Scale

| Level | Size | Font | Usage |
|---|---|---|---|
| 1 | 32pt | Segoe UI Semibold | Page title (textbox) |
| 2 | 24pt | Default | Visual title |
| 3 | 16pt | Default | Slicer header |
| 4 | 16pt | Segoe UI | Table header, legend, KPI label |
| 5 | 14pt | Segoe UI | Data labels, table values, slicer items |
| 6 | 48pt | Segoe UI | Card Value |

---

## Page-Level Formatting (page.json)

### Page Preferences

```json
"displayOption": "FitToPage",
"height": 1088,
"width": 1920,
"objects": {
  "displayArea": [{
    "properties": {
      "verticalAlignment": { "expr": { "Literal": { "Value": "'Middle'" } } }
    }
  }],
  "background": [{
    "properties": {
      "color": { "solid": { "color": { "expr": { "Literal": { "Value": "'#F5F5F5'" } } } } },
      "transparency": { "expr": { "Literal": { "Value": "0D" } } }
    }
  }]
}
```

---

## Container-Level Formatting (visualContainerObjects)

These go inside the `visual` object, alongside `visualType`, `query` and `objects`.

### Background
```json
"background": [{
  "properties": {
    "show": { "expr": { "Literal": { "Value": "true" } } },
    "color": { "solid": { "color": { "expr": { "Literal": { "Value": "'#FFFFFF'" } } } } },
    "transparency": { "expr": { "Literal": { "Value": "0D" } } }
  }
}]
```

### Border
```json
"border": [{
  "properties": {
    "show": { "expr": { "Literal": { "Value": "true" } } },
    "color": { "solid": { "color": { "expr": { "Literal": { "Value": "'#FFFFFF'" } } } } },
    "radius": { "expr": { "Literal": { "Value": "20D" } } },
    "width": { "expr": { "Literal": { "Value": "1D" } } }
  }
}]
```

### Title
```json
"title": [{
  "properties": {
    "show": { "expr": { "Literal": { "Value": "true" } } },
    "text": { "expr": { "Literal": { "Value": "'Sales Overview'" } } },
    "fontColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#444444'" } } } } },
    "fontSize": { "expr": { "Literal": { "Value": "24D" } } },
    "alignment": { "expr": { "Literal": { "Value": "'left'" } } }
  }
}]
```

### Shadow
```json
"dropShadow": [{
  "properties": {
    "show": { "expr": { "Literal": { "Value": "true" } } },
    "preset": { "expr": { "Literal": { "Value": "'Custom'" } } },
    "position": { "expr": { "Literal": { "Value": "'Outer'" } } },
    "color": { "solid": { "color": { "expr": { "Literal": { "Value": "'#000000'" } } } } },
    "transparency": { "expr": { "Literal": { "Value": "90D" } } },
    "shadowSpread": { "expr": { "Literal": { "Value": "0D" } } },
    "shadowBlur": { "expr": { "Literal": { "Value": "15D" } } },
    "shadowDistance": { "expr": { "Literal": { "Value": "0D" } } }
  }
}]
```

### Padding
```json
"padding": [{
  "properties": {
    "top": { "expr": { "Literal": { "Value": "16D" } } },
    "bottom": { "expr": { "Literal": { "Value": "16D" } } },
    "left": { "expr": { "Literal": { "Value": "16D" } } },
    "right": { "expr": { "Literal": { "Value": "16D" } } }
  }
}]
```

**Exceptions**

`card`, `slicer` and `textbox` should never have background, border, shadow, or title in `visualContainerObjects`:
```json
"visualContainerObjects": {
  "background": [{ "properties": { "show": { "expr": { "Literal": { "Value": "false" } } } } }],
  "border":     [{ "properties": { "show": { "expr": { "Literal": { "Value": "false" } } } } }],
  "dropShadow": [{ "properties": { "show": { "expr": { "Literal": { "Value": "false" } } } } }],
  "title":      [{ "properties": { "show": { "expr": { "Literal": { "Value": "false" } } } } }]
}
```

---

## Visual-Level Formatting (objects inside visual)

These go inside the `visual` object, alongside `visualType`,  `query` and `visualContainerObjects`.

### Card Legacy (`card`)
```json
"objects": {
  "categoryLabels": [{
    "properties": {
      "show": { "expr": { "Literal": { "Value": "true" } } },
      "color": { "solid": { "color": { "expr": { "Literal": { "Value": "'#666666'" } } } } },
      "fontSize": { "expr": { "Literal": { "Value": "20D" } } }
    }
  }],
  "labels": [{
    "properties": {
      "color": { "solid": { "color": { "expr": { "Literal": { "Value": "'#444444'" } } } } },
      "fontSize": { "expr": { "Literal": { "Value": "48D" } } }
    }
  }]
}
```

### Slicer (`slicer`)
```json
"objects": {
  "data": [{
    "properties": {
      "mode": { "expr": { "Literal": { "Value": "'Dropdown'" } } }
    }
  }],
  "selection": [{
    "properties": {
      "singleSelect": { "expr": { "Literal": { "Value": "false" } } }
    }
  }],
  "header": [{
    "properties": {
      "show": { "expr": { "Literal": { "Value": "true" } } },
      "fontColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#444444'" } } } } },
      "fontSize": { "expr": { "Literal": { "Value": "16D" } } }
    }
  }],
  "items": [{
    "properties": {
      "fontColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#666666'" } } } } },
      "fontSize": { "expr": { "Literal": { "Value": "14D" } } },
      "padding": { "expr": { "Literal": { "Value": "4D" } } }
    }
  }]
}
```

### Table (`tableEx`)
```json
"objects": {
  "columnHeaders": [{
    "properties": {
      "fontSize": { "expr": { "Literal": { "Value": "16D" } } },
      "fontColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#444444'" } } } } },
      "backColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#FFFFFF'" } } } } },
      "outlineStyle": { "expr": { "Literal": { "Value": "4D" } } },
      "outlineColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#444444'" } } } } },
      "outlineWeight": { "expr": { "Literal": { "Value": "1D" } } } }
  }],
  "values": [{
    "properties": {
      "fontSize": { "expr": { "Literal": { "Value": "14D" } } },
      "fontColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#444444'" } } } } },
      "backColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#F5F5F5'" } } } } },
      "backColorAlternate": { "solid": { "color": { "expr": { "Literal": { "Value": "'#FDFDFD'" } } } } }
    }
  }],
  "grid": [{
    "properties": {
      "gridHorizontal": { "expr": { "Literal": { "Value": "true" } } },
      "gridHorizontalWeight": { "expr": { "Literal": { "Value": "1D" } } },
      "gridHorizontalColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#E6E6E6'" } } } } },
      "gridVertical": { "expr": { "Literal": { "Value": "false" } } },
      "rowPadding": { "expr": { "Literal": { "Value": "4D" } } }
    }
  }],
  "total": [{
    "properties": {
      "fontSize": { "expr": { "Literal": { "Value": "14D" } } },
      "fontColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#444444'" } } } } },
      "bold": { "expr": { "Literal": { "Value": "true" } } }
    }
  }]
}
```

### Donut (`donutChart`)
```json
"objects": {
  "legend": [{
    "properties": {
      "show": { "expr": { "Literal": { "Value": "true" } } },
      "showTitle": { "expr": { "Literal": { "Value": "false" } } },
      "fontSize": { "expr": { "Literal": { "Value": "16D" } } },
      "position": { "expr": { "Literal": { "Value": "'RightCenter'" } } },
      "labelColor": { "solid": { "color": { "expr": { "Literal": { "Value": "'#666666'" } } } } }
    }
  }],
  "labels": [{
    "properties": {
      "show": { "expr": { "Literal": { "Value": "true" } } },
      "fontSize": { "expr": { "Literal": { "Value": "14D" } } },
      "labelDisplayUnits": { "expr": { "Literal": { "Value": "1D" } } },
      "labelPrecision": { "expr": { "Literal": { "Value": "0L" } } },
      "labelStyle": { "expr": { "Literal": { "Value": "'Data value, percent of total'" } } }
    }
  }],
  "slices": [{
    "properties": {
      "innerRadiusRatio": { "expr": { "Literal": { "Value": "60L" } } }
    }
  }]
}
```

### Chart — Data Colors (single series)
```json
"dataPoint": [{
  "properties": {
    "fill": { "solid": { "color": { "expr": { "ThemeDataColor": { "ColorId": 3, "Percent": 0 } } } } }
  }
}]
```

### Chart — Data Colors (multiple series with selector) - Example
```json
"dataPoint": [
  {
    "properties": {
      "fill": { "solid": { "color": { "expr": { "ThemeDataColor": { "ColorId": 3, "Percent": 0 } } } } }
    },
    "selector": { "metadata": "_Medidas.Total" }
  },
  {
    "properties": {
      "fill": { "solid": { "color": { "expr": { "ThemeDataColor": { "ColorId": 2, "Percent": 0 } } } } }
    },
    "selector": { "metadata": "_Medidas.Total Ano Anterior" }
  }
]
```

### Chart — Category Axis
```json
"categoryAxis": [{
  "properties": {
    "fontSize": { "expr": { "Literal": { "Value": "14D" } } },
    "showAxisTitle": { "expr": { "Literal": { "Value": "false" } } }
  }
}]
```

### Chart — Value Axis (hidden)
```json
"valueAxis": [{
  "properties": {
    "show": { "expr": { "Literal": { "Value": "false" } } },
    "showAxisTitle": { "expr": { "Literal": { "Value": "false" } } }
  }
}]
```

### Chart — Legend
```json
"legend": [{
  "properties": {
    "showTitle": { "expr": { "Literal": { "Value": "false" } } },
    "fontSize": { "expr": { "Literal": { "Value": "16D" } } },
    "labelColor": {"solid": { "color": { "expr": { "Literal": { "Value": "'#666666'" } } } } }
  }
}]
```

### Chart — Data Labels
```json
"labels": [{
  "properties": {
    "show": { "expr": { "Literal": { "Value": "true" } } },
    "fontSize": { "expr": { "Literal": { "Value": "14D" } } },
    "labelDisplayUnits": { "expr": { "Literal": { "Value": "1D" } } },
    "labelPrecision": { "expr": { "Literal": { "Value": "0L" } } }
  }
}]
```

### Page Title (`textbox`)

```json
{
  "visualType": "textbox",
  "objects": {
    "general": [{
      "properties": {
        "paragraphs": [{
          "textRuns": [{
            "value": "Title",
            "textStyle": {
              "fontFamily": "Segoe UI Semibold",
              "fontSize": "32pt",
              "color": "#444444"
            }
          }]
        }]
      }
    }]
  }
}
```