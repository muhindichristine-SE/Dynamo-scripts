# Dynamo-scripts

A suite of Autodesk Dynamo graphs for automating repetitive structural BIM tasks in Revit — creating views, cropping them to grid extents, generating sheets, placing views, and batch renaming.

All graphs are built with **visual nodes only**. There are no Python Script nodes in this repository (with the exception noted below), so the graphs can be opened, read, and edited by anyone without scripting knowledge.

---

## Requirements

| Item | Notes |
|---|---|
| Autodesk Revit | Structural discipline model |
| Autodesk Dynamo | Bundled with Revit (Dynamo for Revit) |
| Clockwork | Install via Dynamo → Packages → Search for a Package |
| archilab | Required for the view template graphs |
| spring nodes | |
| BIM4Struc | |

Some graphs that place existing views onto existing sheets at specific coordinates additionally require **Rhythm** or **Genius Loci** — stock Dynamo nodes cannot do this on their own.

---

## Scripts

### `Create_Structural_Plan_Views_From_Levels.dyn`
Creates a structural plan view for every level in the model (or a filtered subset). Use this first on a new project when the views don't exist yet.

> On projects that already have views, this graph will create duplicates. Use the layout-based graph below instead.

### `CREATE_SHEETS_WITH_VIEWS_FROM_LAYOUTS.dyn`
The production graph. Collects **existing** structural plan views, filters them by name, generates sequential sheet numbers, and creates sheets with the views already placed via `Sheet.ByNameNumberTitleBlockAndViews`. No Excel input required — everything is pulled from the model.

### `Creating_Sheets_with_views.dyn`
Earlier working version of the above, kept for reference. Being superseded by the layouts graph.

### `Place_Sections_On_Sheets_Grid.dyn`
Arranges section views onto sheets in a grid pattern. Positioning existing viewports at specific XY coordinates needs a third-party package node — currently this graph previews the intended layout, with final placement done manually.

### `Rename_Levels_UpperLower_v02..dyn`
Batch renames levels, handling upper/lower case conversion across the level list.

### `Rename_Selection_PrefixSuffixReplace.dyn`
Batch renames any selected elements — add a prefix, add a suffix, or find-and-replace part of the existing name.

---

## Recommended workflow

Run the graphs in this order on a new project:

1. **Create views** — `Create_Structural_Plan_Views_From_Levels.dyn`
2. **Crop to grid extents** — crops each plan view to the structural grid
3. **Create sheets with views** — `CREATE_SHEETS_WITH_VIEWS_FROM_LAYOUTS.dyn`

On an existing project, skip step 1 and let the layouts graph filter the views that are already there.

---

## Conventions

These are baked into the graphs. Change the Code Block values at the top of each relevant group if your project differs.

**Sheet numbers** — prefix `6000084-HH-01-00-DR-SE-` with only the trailing four digits incrementing.

**Sheet names** — level name plus the suffix `" STRUCTURAL PLAN"`.

**Titleblock** — `HH_A1_V3 1:AUXILIARY BUILDINGS`.

**View names** — structural layout views end with a trailing dot, e.g. `"Layout."`. This lets `String.EndsWith` filter them precisely and keeps them separate from coordination, Steel, and Rebar views.

**Graph names** — `ALL_CAPS_UNDERSCORE`.

**Groups** — titled `Step 1:`, `Step 2:`, and so on, with a plain-English Note explaining what each step does.

---

## Before you run

- Set the graph to **Manual** run mode. Automatic mode re-runs on every change and will create duplicate sheets or hit sheet number collisions.
- Check that the titleblock family name matches exactly what's loaded in your project.
- Run once, check the result in Revit, then **save the graph immediately** while it's in a working state.

---

## Troubleshooting

**Lists are multiplying (way more sheets than expected)**
Lacing is set to Cross Product. Change it to Longest on the sheet/view pairing node.

**Lacing changes have no effect**
Check for `@L2` on a node input — that syntax overrides node-level lacing.

**Sheet numbers already in use**
The graph ran more than once. Switch to Manual mode and bump the starting number.

**Filter returns nothing**
Confirm the `String.EndsWith` bool output is actually wired into the `mask` input of `List.FilterByBoolMask`. This connection breaks easily when nodes get moved around the canvas.

**Category node returns null**
`"Structural Plans"` is not a valid `Category.ByName` string. Use the Categories dropdown node instead.

**Downstream lists don't line up**
`List.Clean` with `preserveIndices = false` will break alignment. Set it to `true` or remove the node.

---

## Resources

- [Dynamo Primer](https://primer.dynamobim.org/)
- [Dynamo Forum](https://forum.dynamobim.com/)
- [Dynamo Package Manager](https://dynamopackages.com/)

---

## Licence

Copyright (C) 2026 Christine Muhindi

This program is free software: you can redistribute it and/or modify it
under the terms of the GNU Affero General Public License as published by
the Free Software Foundation, either version 3 of the License, or (at your
option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT
ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or
FITNESS FOR A PARTICULAR PURPOSE. See the GNU Affero General Public License
for more details.

You should have received a copy of the GNU Affero General Public License
along with this program. If not, see <https://www.gnu.org/licenses/>.

---

## Author

Christine Muhindi — Structural Engineering
