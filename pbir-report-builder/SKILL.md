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
- `references/layout-rules.md` — fixed position rules per visual type and color palette reference
- `references/folder-structure.md` — annotated PBIP/PBIR/TMDL folder tree
- `references/page-naming.md` — readable naming convention (pg##/v## rules)

**JSON templates (ready-to-use):**
- `references/json-templates/card.json` — Legacy KPI Card
- `references/json-templates/clusteredBarChart.json` — Horizontal bar chart
- `references/json-templates/clusteredColumnChart.json` — Vertical bar chart
- `references/json-templates/comboChart.json` — Column + line combo chart
- `references/json-templates/slicer.json` — Slicer dropdown
- `references/json-templates/donutChart.json` — Donut/pie chart
- `references/json-templates/lineChart.json` — Line chart
- `references/json-templates/matrix.json` — Pivot table with rows/columns/values
- `references/json-templates/page.json` — Standard page definition
- `references/json-templates/table.json` — Table with multiple columns/measures
- `references/json-templates/title.json` — Textbox with the page title

**JSON schemas (Microsoft originals):**
- `references/json-schemas/` — local copies of all PBIR schemas for offline validation

Always read the relevant template file before building any visual. Templates contain the complete and correct formatting for that visual type.

## How It Works

### Step 1: Gather Requirements

Ask the user:
1. **Target PBIP project path** — where to write the files (must already exist as a saved PBIP)
2. **Page name and purpose** — e.g., "Sales Overview", "Product Drillthrough"
3. **Visuals needed** — what charts/cards/tables, and what data they show
4. **Measures and columns** — table.field references for each visual (case-sensitive, must match model exactly)

### Step 2: Generate the PBIR Files

Write files directly into the project's `.Report/definition/` folder.

#### File Generation Order:
1. Create page folder: `definition/pages/pg##Name/`
2. Create `page.json` in that folder
3. Create `visuals/` subfolder
4. For each visual, create `visuals/v##Name/visual.json`
5. Update `definition/pages/pages.json` — add the new page to `pageOrder`

### Step 3: Naming Convention

Apply naming convention from `references/page-naming.md`. The `name` field inside JSON MUST match the folder name exactly.

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

#### Visual Interactions (DataFilter)

By default, clicking a data point in one visual **highlights** bar/column and pie/donut charts instead of filtering them. To change this behavior to **filter**, declare `DataFilter` interactions in the page.

**Valid sources (support click selection):** bar/column chart, line chart, pie/donut, table/matrix.

**Valid targets:** bar/column chart, pie/donut

For every source→target pair where target is a bar/column or pie/donut, add an entry:
```json
"visualInteractions": [
  { "source": "v01BarByRegion", "target": "v02ColByMonth", "type": "DataFilter" },
  { "source": "v02ColByMonth", "target": "v01BarByRegion", "type": "DataFilter" }
]
```

### visual.json — Visual Container Structure

Each visual is a folder `v##Name/` containing a single `visual.json`.  
**Always start from the relevant template in `references/json-templates/`** — it contains the complete structure including `query`, `objects`, and `visualContainerObjects`.

Key properties to substitute in the template:
- `name` — must match the folder name exactly
- `position` — x, y, z, width, height, tabOrder (apply Layout Rules from `layout-rules.md`)
- `query.queryState` — replace all placeholder table/field names with actual model values (case-sensitive)
- `displayName` — set a short, user-friendly label for each field projection (measures especially).

---

## Visual Type Patterns

See `references/visual-types.md` for all 35+ visual types, query roles, and container structure.

---

## Field Reference Patterns

See `references/field-references.md` for Column, Measure, Aggregation patterns and literal value types.

---

## Layout Grid Rules

**Important** — All visuals must follow the layout grid. These rules are mandatory and must always be respected.

- Maintain 32px spacing from page borders and between visuals.
- X and Y positions must be multiples of 16.
- Width and height must be multiples of 16.

---

## Implementation Method

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