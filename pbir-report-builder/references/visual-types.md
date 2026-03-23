# Visual Types Reference

Every visual in PBIR is defined by a `visual.json` file. The `visualType` string determines what kind of chart renders and what query roles are available.

## Visual Type Identifiers

### Cards & KPIs
| visualType | Display Name | Query Roles |
|---|---|---|
| `card` | Card (legacy) | `Values` |
| `cardVisual` | Card (new) | `Data`, `ReferenceLabels`, `AdditionalMeasure` |
| `multiRowCard` | Multi-Row Card | `Values` |
| `kpi` | KPI | `Indicator`, `TrendLine`, `Goal` |

### Bar & Column Charts
| visualType | Display Name | Query Roles |
|---|---|---|
| `clusteredBarChart` | Clustered Bar | `Category`, `Y`, `Series`, `Tooltips` |
| `clusteredColumnChart` | Clustered Column | `Category`, `Y`, `Series`, `Tooltips` |
| `barChart` | Stacked Bar | `Category`, `Y`, `Series`, `Tooltips` |
| `columnChart` | Stacked Column | `Category`, `Y`, `Series`, `Tooltips` |
| `hundredPercentStackedBarChart` | 100% Stacked Bar | `Category`, `Y`, `Series`, `Tooltips` |
| `hundredPercentStackedColumnChart` | 100% Stacked Column | `Category`, `Y`, `Series`, `Tooltips` |
| `waterfallChart` | Waterfall | `Category`, `Y`, `Breakdown`, `Tooltips` |

### Line & Area Charts
| visualType | Display Name | Query Roles |
|---|---|---|
| `lineChart` | Line Chart | `Category`, `Y`, `Y2`, `Series`, `Tooltips` |
| `areaChart` | Area Chart | `Category`, `Y`, `Series`, `Tooltips` |
| `stackedAreaChart` | Stacked Area | `Category`, `Y`, `Series`, `Tooltips` |
| `ribbonChart` | Ribbon Chart | `Category`, `Y`, `Series`, `Tooltips` |

### Combo Charts
| visualType | Display Name | Query Roles |
|---|---|---|
| `lineStackedColumnComboChart` | Line + Stacked Column | `Category`, `Y`, `Y2`, `Series`, `Tooltips` |
| `lineClusteredColumnComboChart` | Line + Clustered Column | `Category`, `Y`, `Y2`, `Series`, `Tooltips` |

### Pie & Donut
| visualType | Display Name | Query Roles |
|---|---|---|
| `pieChart` | Pie Chart | `Category`, `Y`, `Tooltips` |
| `donutChart` | Donut Chart | `Category`, `Y`, `Tooltips` |
| `funnel` | Funnel | `Category`, `Y`, `Tooltips` |
| `treemap` | Treemap | `Group`, `Details`, `Values`, `Tooltips` |

### Tables & Matrices
| visualType | Display Name | Query Roles |
|---|---|---|
| `tableEx` | Table | `Values` (multiple projections) |
| `pivotTable` | Matrix | `Rows`, `Columns`, `Values` |

### Maps
| visualType | Display Name | Query Roles |
|---|---|---|
| `map` | Map (Bing) | `Category`, `Size`, `Series`, `Tooltips` |
| `filledMap` | Filled Map | `Location`, `Values`, `Tooltips` |
| `azureMap` | Azure Map | varies |
| `shapeMap` | Shape Map | `Location`, `ColorSaturation`, `Tooltips` |

### Scatter & Other
| visualType | Display Name | Query Roles |
|---|---|---|
| `scatterChart` | Scatter | `Category`, `X`, `Y`, `Size`, `Series`, `Tooltips` |
| `gauge` | Gauge | `Y`, `MinValue`, `MaxValue`, `TargetValue` |
| `decompositionTreeVisual` | Decomposition Tree | `Analyze`, `Explain By` |

### Slicers & Filters
| visualType | Display Name | Query Roles |
|---|---|---|
| `slicer` | Slicer | `Values` |

### Decorative / Layout
| visualType | Display Name | Query Roles |
|---|---|---|
| `actionButton` | Button | none (uses `action` property) |
| `basicShape` | Shape | none |
| `image` | Image | none (uses `objects.general.imageUrl`) |
| `textbox` | Text Box | none (uses `objects.general.paragraphs`) |

---

## Query Role Details

### How Query Roles Work

Each visual type has named "wells" (data roles) that accept field projections. The `queryState` object maps role names to arrays of field projections:

```json
"query": {
  "queryState": {
    "RoleName": {
      "projections": [
        {
          "field": { ... },           // Column or Measure reference
          "queryRef": "Table.Field",  // Dot-separated table.field
          "nativeQueryRef": "Field",  // Just the field name
          "displayName": "Label"      // Clean label
        }
      ]
    }
  }
}
```

### Role Name Quick Reference

**Bar/Column/Line charts**: `Category` (axis), `Y` (values), `Series` (legend), `Tooltips`
**Combo charts**: `Category`, `ColumnY`, `LineY`, `Series`, `Tooltips`
**Legacy Card (card)**: `Values`
**New Card (cardVisual)**: `Data` (main value), `ReferenceLabels` (comparison), `AdditionalMeasure` (extra)
**Table**: `Values` (all columns as separate projections)
**Matrix**: `Rows`, `Columns`, `Values`
**Slicer**: `Values`
**Gauge**: `Y` (value), `MinValue`, `MaxValue`, `TargetValue`
**KPI**: `Indicator`, `TrendLine`, `Goal`

---

### Position Properties
| Property | Type | Description |
|---|---|---|
| `x` | number | Pixels from left edge of canvas |
| `y` | number | Pixels from top edge of canvas |
| `z` | number | Z-order (higher = on top). Start at 1000, increment by 1 |
| `width` | number | Visual width in pixels |
| `height` | number | Visual height in pixels |
| `tabOrder` | number | Tab navigation order (0-based) |
| `angle` | number | Rotation angle in degrees (optional) |

### Optional Container Properties
| Property | Description |
|---|---|
| `parentGroupName` | Name of parent visual group |
| `filterConfig` | Visual-level filters |
| `isHidden` | Boolean — hide the visual |
| `annotations` | Name-value metadata pairs |
| `howCreated` | Origin tag (Copilot, DraggedToCanvas, etc.) |
