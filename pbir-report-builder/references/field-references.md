# Field References in PBIR

Every visual that shows data needs field references that point to columns or measures in the semantic model. This document shows the exact JSON patterns.

## Column Reference

Use when binding a column from a table (for axes, categories, slicers):

```json
{
  "field": {
    "Column": {
      "Expression": {
        "SourceRef": { "Entity": "TableName" }
      },
      "Property": "ColumnName"
    }
  },
  "queryRef": "TableName.ColumnName",
  "nativeQueryRef": "ColumnName",
  "displayName": "ColumnDisplay"
}
```

**Example** — Product category on a bar chart axis:
```json
{
  "field": {
    "Column": {
      "Expression": {
        "SourceRef": { "Entity": "TABLE_NAME" }
      },
      "Property": "COLUMN_NAME"
    }
  },
  "queryRef": "TABLE_NAME.COLUMN_NAME",
  "nativeQueryRef": "COLUMN_NAME",
  "displayName": "COLUMN_DISPLAY"
}
```

## Measure Reference

Use when binding a DAX measure (for values, KPIs, totals):

```json
{
  "field": {
    "Measure": {
      "Expression": {
        "SourceRef": { "Entity": "TableName" }
      },
      "Property": "MeasureName"
    }
  },
  "queryRef": "TableName.MeasureName",
  "nativeQueryRef": "MeasureName",
  "displayName": "MeasureDisplay"
}
```

**Example** — a measure in a card:
```json
{
  "field": {
    "Measure": {
      "Expression": {
        "SourceRef": { "Entity": "MEASURES_TABLE" }
      },
      "Property": "MEASURE_VALUE"
    }
  },
  "queryRef": "MEASURES_TABLE.MEASURE_VALUE",
  "nativeQueryRef": "MEASURE_VALUE",
  "displayName": "MEASURE_DISPLAY"
}
```

## Aggregated Column Reference

Use when you want Power BI to aggregate a column (SUM, AVG, COUNT, etc.) instead of using a DAX measure:

```json
{
  "field": {
    "Aggregation": {
      "Expression": {
        "Column": {
          "Expression": {
            "SourceRef": { "Entity": "TableName" }
          },
          "Property": "ColumnName"
        }
      },
      "Function": 0
    }
  },
  "queryRef": "Sum(TableName.ColumnName)",
  "nativeQueryRef": "ColumnName",
  "displayName": "Sum of ColumnName"
}
```

### Aggregation Function Codes
| Code | Function |
|---|---|
| `0` | SUM |
| `1` | AVG |
| `2` | COUNT |
| `3` | MIN |
| `4` | MAX |
| `5` | COUNTNONBLANK (Count Distinct) |
| `6` | None (Don't Summarize) |

## Conditional Formatting with Measures

Use a measure to dynamically set a visual property (like color):

```json
"color": {
  "solid": {
    "color": {
      "expr": {
        "Measure": {
          "Expression": {
            "SourceRef": { "Entity": "MEASURES_TABLE" }
          },
          "Property": "KPI_COLOR_MEASURE"
        }
      }
    }
  }
}
```

This is how KPI cards get green/red coloring based on performance.

## Literal Values

For static values in formatting objects:

```json
// Boolean
{ "expr": { "Literal": { "Value": "true" } } }

// String
{ "expr": { "Literal": { "Value": "'FitToPage'" } } }

// Number (append D for decimal)
{ "expr": { "Literal": { "Value": "0D" } } }

// Integer (append L for long)
{ "expr": { "Literal": { "Value": "12L" } } }

// Color
{ "expr": { "Literal": { "Value": "'#FF0000'" } } }
```

## Complete Query Example

A clustered column chart with a category axis and two measures (CY vs PY):

```json
"query": {
  "queryState": {
    "Category": {
      "projections": [
        {
          "field": {
            "Column": {
              "Expression": { "SourceRef": { "Entity": "TABLE_NAME" } },
              "Property": "COLUMN_NAME"
            }
          },
          "queryRef": "TABLE_NAME.COLUMN_NAME",
          "nativeQueryRef": "COLUMN_NAME",
          "displayName": "COLUMN_DISPLAY"
        }
      ]
    },
    "Y": {
      "projections": [
        {
          "field": {
            "Measure": {
              "Expression": { "SourceRef": { "Entity": "MEASURES_TABLE" } },
              "Property": "MEASURE_VALUE"
            }
          },
          "queryRef": "MEASURES_TABLE.MEASURE_VALUE",
          "nativeQueryRef": "MEASURE_VALUE"
        },
        {
          "field": {
            "Measure": {
              "Expression": { "SourceRef": { "Entity": "MEASURES_TABLE" } },
              "Property": "MEASURE_COMPARISON"
            }
          },
          "queryRef": "MEASURES_TABLE.MEASURE_COMPARISON",
          "nativeQueryRef": "MEASURE_COMPARISON",
          "displayName": "MEASURE_DISPLAY"
        }
      ]
    }
  }
}
```

## Key Rules

1. **Entity** must match a table name in the semantic model exactly (case-sensitive)
2. **Property** must match a column or measure name exactly (case-sensitive)
3. **queryRef** format: `TableName.FieldName` (dot-separated)
4. **nativeQueryRef**: raw field name without table prefix — must match `Property` exactly
5. **displayName**: user-friendly label shown in titles, legends, and axis labels. Use to shorten or clean up long technical measure names.
6. Multiple fields in one role = multiple projections in the array
7. The `active: true` flag on the first projection indicates it's the primary field
