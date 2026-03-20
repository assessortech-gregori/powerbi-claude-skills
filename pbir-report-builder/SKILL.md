---
name: pbir-report-builder
description: "Power BI PBIR Report Builder. Generates Power BI report pages and visuals by writing PBIR JSON files directly into PBIP project folders. Use this skill EVERY TIME the user asks to: create a Power BI report page, add visuals to a report, generate KPI cards, create charts or tables in Power BI, build a dashboard layout with visuals, create actual vs plan visuals, or programmatically create Power BI visuals. Also trigger when the user mentions 'PBIR', 'variance chart', 'variance table', 'actual vs plan', 'actual vs comparison', 'create visuals', 'add a page', 'build a report', 'KPI cards', 'place visuals', or wants to generate Power BI report content through code. If the user mentions any combination of Power BI + visuals/page/report/KPI/chart/table/IBCS/variance + create/build/generate/add, use this skill."
---

# PBIR Report Builder

Add pages and visuals to existing Power BI PBIP projects by writing PBIR (Enhanced Report Format) JSON files directly into the project folder structure.

## Critical: Hybrid Approach (Do NOT Create PBIP From Scratch)

**NEVER create an entire PBIP project from scratch.** Power BI Desktop generates boilerplate files (`report.json`, `.platform`, `version.json`, theme packages, settings) with version-specific schema references, real GUIDs, and internal settings that are impractical to replicate manually.

**The correct workflow:**
1. **User creates a blank PBIP** — open Power BI Desktop, connect to the data source, save as PBIP format. This generates all boilerplate correctly.
2. **User creates measures and columns** — build your semantic model in Power BI Desktop as usual.
3. **This skill adds pages and visuals** — write PBIR JSON files into the existing `.Report/definition/` folder to create report pages with visuals.

**Why not from scratch?** The `report.json` file contains Desktop-version-specific theme references (e.g., `CY26SU02` with nested version objects), resource packages, 6+ report settings, and the `.platform` files need real UUIDs. These change between Desktop versions and cannot be reliably generated.

### PBIP File Structure (for reference — user creates this by saving)
```
ProjectName.pbip                           ← entry point (Desktop creates)
ProjectName.Report/
  .platform                                ← Desktop creates (Report type, UUID)
  definition.pbir                          ← Desktop creates (links to semantic model)
  definition/
    report.json                            ← Desktop creates (theme, settings)
    version.json                           ← Desktop creates
    pages/
      pages.json                           ← WE MODIFY (add page to pageOrder)
      pg01Overview/                        ← WE CREATE
        page.json                          ← WE CREATE
        visuals/
          v01KpiSales/visual.json          ← WE CREATE
ProjectName.SemanticModel/
  .platform                                ← Desktop creates (SemanticModel type, UUID)
  definition.pbism                         ← Desktop creates (NOT definition.tmdl!)
  definition/
    database.tmdl                          ← Desktop creates (compatibilityLevel: 1600)
    model.tmdl                             ← Desktop creates
    tables/*.tmdl                          ← Desktop creates
```

**Key learning:** The semantic model entry point is `definition.pbism` (a JSON file), NOT `definition.tmdl`. TMDL files go inside the `definition/` subfolder.

## References (Self-Contained)

All reference material is bundled inside this skill at `references/`:

**Reference docs:**
- `references/visual-types.md` — all 35+ visual type identifiers and query roles
- `references/field-references.md` — column/measure binding patterns, aggregation codes, conditional formatting
- `references/formatting-objects.md` — container/visual/page formatting JSON patterns
- `references/folder-structure.md` — annotated PBIP/PBIR/TMDL folder tree
- `references/page-naming.md` — readable naming convention (pg##/v## rules)

**JSON templates (ready-to-use):**
- `references/json-templates/card-kpi.json` — KPI card with CY, PY, YoY%, conditional color
- `references/json-templates/clustered-column.json` — vertical bar chart
- `references/json-templates/clustered-bar.json` — horizontal bar chart
- `references/json-templates/line-chart.json` — line trend over time
- `references/json-templates/combo-chart.json` — column + line combo
- `references/json-templates/table.json` — table with multiple columns/measures
- `references/json-templates/matrix.json` — pivot table with rows/columns/values
- `references/json-templates/slicer.json` — slicer dropdown
- `references/json-templates/donut.json` — donut/pie chart
- `references/json-templates/page-standard.json` — standard page definition
- `references/json-templates/report-settings.json` — report-level settings

**JSON schemas (Microsoft originals):**
- `references/json-schemas/` — local copies of all PBIR schemas for offline validation

Read the relevant template file when building a visual type you haven't used recently.

## How It Works

### Step 0: Prerequisites

Before using this skill, verify:
1. **A PBIP project already exists** — user must have saved a `.pbip` from Power BI Desktop
2. **Measures exist** — created in Power BI Desktop
3. **Power BI Desktop is CLOSED** — files cannot be written while Desktop has the project open

If the user doesn't have a PBIP yet, instruct them to:
1. Open Power BI Desktop
2. Connect to their data source
3. File → Save As → select "Power BI Project (.pbip)" format
4. This creates the full boilerplate structure that this skill needs

### Step 1: Gather Requirements

Ask the user:
1. **Target PBIP project path** — where to write the files (must already exist as a saved PBIP)
2. **Page name and purpose** — e.g., "Sales Overview", "Product Drillthrough"
3. **Canvas size** — 1920x1088
4. **Visuals needed** — what charts/cards/tables, and what data they show
5. **Measures and columns** — table.field references for each visual (case-sensitive, must match model exactly)

### Step 2: Generate the PBIR Files

Write files directly into the project's `.Report/definition/` folder.

#### File Generation Order:
1. Create page folder: `definition/pages/pg##Name/`
2. Create `page.json` in that folder
3. Create `visuals/` subfolder
4. For each visual, create `visuals/v##Name/visual.json`
5. Update `definition/pages/pages.json` — add the new page to `pageOrder`

### Step 3: Naming Convention

**ALWAYS use readable names:**
- Pages: `pg01Overview`, `pg02SalesDetail`, `pg03Drillthrough`
- Visuals: `v01KpiTotalSales`, `v02BarByRegion`, `v03LineTrend`
- The `name` field inside the JSON MUST match the folder name exactly

See `page-naming.md` for full convention.

### Step 4: Deliver

Tell the user:
1. Files have been written to `[path]`
2. Close Power BI Desktop if open, then reopen the `.pbip` file
3. The new page should appear with all visuals

---

## JSON Structure Reference

### pages.json — Adding a New Page
Read existing `pages.json`, add the new page name to `pageOrder`:
```json
{
  "$schema": "https://developer.microsoft.com/json-schemas/fabric/item/report/definition/pagesMetadata/1.0.0/schema.json",
  "pageOrder": ["pg01Overview", "pg02NewPage"],
  "activePageName": "pg01Overview"
}
```

### visual.json — Visual Container Structure
```json
{
  "$schema": "https://developer.microsoft.com/json-schemas/fabric/item/report/definition/visualContainer/2.0.0/schema.json",
  "name": "v01Kpi",
  "position": {
    "x": 160,
    "y": 80,
    "z": 1000,
    "width": 320,
    "height": 160,
    "tabOrder": 0
  },
  "visual": {
    "visualType": "cardVisual",
    "query": {
      "queryState": {
        "ROLE_NAME": {
          "projections": [
            {
              "field": { "..." : "..." },
              "queryRef": "Table.Field",
              "nativeQueryRef": "Field"
            }
          ]
        }
      }
    },
    "objects": {},
    "drillFilterOtherVisuals": true
  }
}
```

---

## Visual Type Patterns

### KPI Card

**Query roles:** `Values`

Visual type: `card`

### Bar/Column Chart

**Query roles:** `Category` (axis), `Y` (values), `Series` (legend, optional), `Tooltips` (optional)

```json
"visual": {
  "visualType": "clusteredColumnChart",
  "query": {
    "queryState": {
      "Category": { "projections": [{
        "field": { "Column": { "Expression": { "SourceRef": { "Entity": "TABLE" } }, "Property": "COLUMN" } },
        "queryRef": "TABLE.COLUMN", "nativeQueryRef": "COLUMN"
      }]},
      "Y": { "projections": [{
        "field": { "Measure": { "Expression": { "SourceRef": { "Entity": "MEASURES_TABLE" } }, "Property": "MEASURE" } },
        "queryRef": "MEASURES_TABLE.MEASURE", "nativeQueryRef": "MEASURE"
      }]}
    }
  },
  "drillFilterOtherVisuals": true
}
```

Visual types: `clusteredColumnChart`, `clusteredBarChart`, `columnChart` (stacked), `barChart` (stacked)

### Line Chart

**Query roles:** `Category` (axis), `Y` (values), `Y2` (secondary axis, optional), `Series` (legend, optional)

```json
"visual": {
  "visualType": "lineChart",
  "query": {
    "queryState": {
      "Category": { "projections": [{
        "field": { "Column": { "Expression": { "SourceRef": { "Entity": "TABLE_NAME" } }, "Property": "COLUMN_NAME" } },
        "queryRef": "TABLE_NAME.COLUMN_NAME", "nativeQueryRef": "COLUMN_NAME"
      }]},
      "Y": { "projections": [{
        "field": { "Measure": { "Expression": { "SourceRef": { "Entity": "MEASURES_TABLE" } }, "Property": "MEASURE" } },
        "queryRef": "MEASURES_TABLE.MEASURE", "nativeQueryRef": "MEASURE"
      }]}
    }
  }
}
```

### Combo Chart

**Query roles:** `Category`, `ColumnY`, `LineY`, `Series`

Use `ColumnY` for bar values and `LineY` for line values (NOT `Y`).
Visual type: `lineClusteredColumnComboChart` or `lineStackedColumnComboChart`

### Table

**Query roles:** `Values` — each column/measure is a separate projection in the same array

```json
"visual": {
  "visualType": "tableEx",
  "query": {
    "queryState": {
      "Values": { "projections": [
        { "field": { "Column": { "Expression": { "SourceRef": { "Entity": "TABLE" } }, "Property": "COL1" } }, "queryRef": "TABLE.COL1", "nativeQueryRef": "COL1" },
        { "field": { "Measure": { "Expression": { "SourceRef": { "Entity": "MTABLE" } }, "Property": "M1" } }, "queryRef": "MTABLE.M1", "nativeQueryRef": "M1" },
        { "field": { "Measure": { "Expression": { "SourceRef": { "Entity": "MTABLE" } }, "Property": "M2" } }, "queryRef": "MTABLE.M2", "nativeQueryRef": "M2" }
      ]}
    }
  }
}
```

### Matrix

**Query roles:** `Rows`, `Columns`, `Values`

Visual type: `pivotTable`

### Slicer

**Query roles:** `Values` — the field to slice by

Visual type: `slicer`

### Donut / Pie

**Query roles:** `Category` (slices), `Y` (value)

Visual types: `donutChart`, `pieChart`

---

## Field Reference Patterns

### Column (for axes, categories, slicers):
```json
{ "Column": { "Expression": { "SourceRef": { "Entity": "TABLE_NAME" } }, "Property": "COLUMN_NAME" } }
```

### Measure (for values, KPIs):
```json
{ "Measure": { "Expression": { "SourceRef": { "Entity": "TABLE_NAME" } }, "Property": "MEASURE_NAME" } }
```

### Rules:
- `Entity` = exact table name from semantic model (case-sensitive)
- `Property` = exact column or measure name (case-sensitive)
- `queryRef` = `"Table.Field"` (dot-separated)
- `nativeQueryRef` = just the field name

---

## Layout Grid Positions

- All visuals must maintain **32px spacing** from the page borders and from other visuals.
- `X` and `Y` positions must be **multiples of 16**.
- Visual **width and height** must also be **multiples of 16**.

**Exception — Page title (textbox):**
- Must be positioned at `X = 24` and `Y = 24`.

**Slicers:**
- Must be placed at the **top-right area of the page**.

## Implementation Method

### Writing Files

Use Node.js (.mjs) scripts to write files. This avoids the EEXIST error that the Write tool encounters on paths with spaces.

```javascript
import { writeFileSync, mkdirSync, readFileSync } from 'fs';
import { join } from 'path';

const projectBase = 'PATH_TO_PBIP_PROJECT';
const reportDef = join(projectBase, 'ProjectName.Report', 'definition');

// 1. Create page folder
const pageDir = join(reportDef, 'pages', 'pg01Overview');
const visualsDir = join(pageDir, 'visuals');
mkdirSync(visualsDir, { recursive: true });

// 2. Write page.json
writeFileSync(join(pageDir, 'page.json'), JSON.stringify(pageJson, null, 2), 'utf8');

// 3. Create visual folders and write visual.json files
for (const visual of visuals) {
  const vDir = join(visualsDir, visual.name);
  mkdirSync(vDir, { recursive: true });
  writeFileSync(join(vDir, 'visual.json'), JSON.stringify(visual, null, 2), 'utf8');
}

// 4. Update pages.json
const pagesJsonPath = join(reportDef, 'pages', 'pages.json');
const pagesJson = JSON.parse(readFileSync(pagesJsonPath, 'utf8'));
if (!pagesJson.pageOrder.includes('pg01Overview')) {
  pagesJson.pageOrder.push('pg01Overview');
}
writeFileSync(pagesJsonPath, JSON.stringify(pagesJson, null, 2), 'utf8');
```

Save the script as `.mjs` (not `.js`) in the `07-PowerBI/` folder and run with `node script.mjs`.

### Important Caveats

1. **Power BI Desktop must be closed** before writing files, then reopen the `.pbip` file
2. **Invalid JSON causes blocking errors** — always validate structure before writing
3. **Entity and Property names are case-sensitive** — must match semantic model exactly
4. **The name field must match the folder name** — for both pages and visuals
5. **Max 50 characters** for page/visual names
6. **Always include $schema** — Power BI Desktop validates against these schemas
7. **NEVER create a PBIP from scratch** — always work with an existing Desktop-saved project
8. **Semantic model entry point is `definition.pbism`** (JSON), NOT `definition.tmdl` — TMDL files go in `definition/` subfolder
9. **`.platform` files need real UUIDs** — only Desktop generates these correctly
10. **Schema versions matter** — use v2.0.0 for visualContainer/page, v1.3.0+ for report (check existing `report.json` for the version Desktop used)
11. **Read existing `report.json` first** — match its schema version and theme name when adding pages

---

## Workflow

This skill handles the **report and visual layer** of a Power BI project. The semantic model (tables, columns, measures, relationships) is managed in Power BI Desktop as usual.

Typical workflow:
1. User opens PBIP in Desktop, connects data, creates measures
2. User closes Desktop
3. This skill writes page/visual JSON files into the project folder
4. User reopens Desktop to see new pages