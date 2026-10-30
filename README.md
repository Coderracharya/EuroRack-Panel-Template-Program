# Parametric Eurorack Panel Template and Router Program (FreeCAD)

A FreeCAD sample project that turns a **placement list** (a spreadsheet of hole positions) into finished Eurorack front panels, then into a router program that cuts a full set from one aluminium blank.

## About Me

I'm **Arijit Acharjee**, a mechanical engineering graduate (B.Tech, Academy of Technology, MAKAUT). I'm starting my career in design and manufacturing, and I enjoy turning a drawing into something that can actually be machined.

## Why I'm Solving This

This task covers the whole path from CAD to CAM in one small project: parametric design, toolpaths, G-code and documentation. It is practical, repeatable work, and it is the kind of work I want to do well and grow in.

## Problem Statement (https://www.upwork.com/freelance-jobs/apply/FreeCAD-Expert-Needed-Parametric-Eurorack-Panel-Template-and-Router-Program_~022112830878510945078)

A small synthesizer maker wants to cut its own Eurorack front panels from aluminium sheet.

- The panels follow a fixed standard: **128.5 mm high**, widths in multiples of **5.08 mm (1 HP)** minus a small clearance, and standard mounting holes.
- Every panel has different holes for jacks, pots, LEDs and switches.
- Redrawing each panel by hand is slow and error-prone.
- The machine needs a safe, repeatable cutting program that cuts several panels from one blank.

## Proposed Solution

1. **One parametric template.** Global values (height, thickness, clearance, mounting hole rules, minimum gaps) live in a `Params` spreadsheet.
2. **A placement list per panel.** Each panel has its own sheet listing the type, position and size of every feature. Change a cell, recompute, and the panel rebuilds.
3. **Automatic checks.** The model warns about holes that are too close to each other or to the panel edge.
4. **Four panels from the same template.** Each is just a different placement list.
5. **One CAM job.** All four panels are nested on a single aluminium blank: engrave, drill, cut the holes, then cut the outlines last with tabs.
6. **Deliverables.** G-code, setup sheet, PDF drawings and DXF of each panel.

## Eurorack Basics Used

| Item | Value |
|------|-------|
| Panel height | 128.5 mm |
| Panel width | HP × 5.08 mm − 0.3 mm clearance |
| Mounting hole diameter | 3.2 mm |
| Mounting holes from top and bottom edge | 3.0 mm |
| First mounting hole from left edge | 7.5 mm |
| Second mounting hole (wide panels) | 7.5 mm + (HP − 3) × 5.08 mm |
| Panel thickness | 2 mm aluminium |

Default hole sizes (edit `LOOKUP` in the macro to match your parts):

| Type | Diameter |
|------|----------|
| jack (3.5 mm) | 6.0 mm |
| pot (9 mm) | 7.2 mm |
| LED (3 mm) | 3.1 mm |
| switch | 6.2 mm |

## Repository Contents

```
.
├── EurorackPanels.FCMacro     # builds the panels from spreadsheets
├── EurorackPanels.FCStd       # FreeCAD project (template, four panels, CAM job)
├── gcode/                     # posted G-code
├── docs/
│   ├── setup_sheet.pdf        # operator setup sheet
│   └── drawings/              # PDF drawing per panel
└── dxf/                       # DXF per panel
```

> Update this tree to match what is actually in the repository.

## Placement List Format

Each panel has a sheet named `Place_<NAME>`. The panel width in HP is in cell `J1` (alias `HP`). The list starts at row 2 and stops at the first empty `type`.

| Column | Meaning |
|--------|---------|
| A `ID` | Feature name, e.g. `J1` |
| B `type` | `jack`, `pot`, `led`, `switch`, `hole` or `slot` |
| C `x` | Centre, mm from the panel's bottom-left corner |
| D `y` | Centre, mm from the panel's bottom-left corner |
| E `dia` | Diameter; leave empty to use the lookup table |
| F `length` | Overall length, slots only |
| G `label` | Text to engrave |

## How to Use

1. Install FreeCAD (1.0 recommended).
2. Copy `EurorackPanels.FCMacro` into your FreeCAD macro folder.
3. Run it from **Macro → Macros…**. It creates the `Params` sheet, one `Place_` sheet per panel, and the four panel objects.
4. Edit any cell in a `Place_` sheet and press **Ctrl+R** to recompute.
5. To export DXF files, uncomment `export_all_dxf()` at the end of the macro and run it again. Files are written to `~/eurorack_output`.

## CAM Workflow

1. Switch to the **CAM** workbench and create a **Job** with all four panels as base models.
2. Set the stock size and nest the panels with gaps of at least 8-10 mm and margins for hold-down screws.
3. Set the work zero (G54): bottom-left corner of the stock, Z0 on the top surface.
4. Add tool controllers (V-bit, drill, end mill) with spindle speed and feeds suited to aluminium.
5. Add operations in this order: **Engrave → Drill → Helix / Slot → Profile (outside)**.
6. Add **Tag** dressups to each profile so the panels stay held until the end.
7. Run the **CAM Simulator** and fix any issues.
8. Post-process with the stock post-processor matching the router (e.g. `grbl` or `linuxcnc`) and check the units, absolute mode, spindle commands and retract.

## Hold-Down

Sacrificial MDF spoilboard, with screws in the waste margin or double-sided tape under the panels. Toolpaths are kept clear of screw positions.

## Status

- [x] Parametric template and placement-list macro
- [x] Four panel designs
- [ ] CAM job and simulation
- [ ] G-code posted and checked
- [ ] Setup sheet PDF
- [ ] Drawings and DXF for each panel

> Tick the boxes as you complete each item. Only mark a box when the work is really done.

## Notes and Limitations

- The panel layouts in this repository are **sample designs**, not a client specification.
- Cutting parameters are starting values and must be tuned for the actual machine and cutter.
- Run an air cut first, then cut one panel and measure it before running the full set.

## Author

**Arijit Acharjee**
B.Tech Mechanical Engineering, Academy of Technology (MAKAUT)
