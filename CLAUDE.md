# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project: Photon Mission Creator

Tooling for generating Avilon Photon drone mission files from warehouse shelf layout data (WMS exports).

- **Customer context:** Yusen Logistics — BMW warehouse, Aisle N06
- **Related repos:** Cart-kart/Yusen-Drone-Dashboard (viewing dashboard)

## File Types

| Extension / Name | Role |
|---|---|
| `*.mission` | Drone mission file — comma-separated waypoints fed to Avilon autopilot |
| `TAG_*.json` | ArUco/AprilTag navigation marker config for the drone |
| `Layout_Locate_*_WMS_L.csv` | WMS shelf layout export — Left side location codes |
| `Layout_Locate_*_WMS_R.csv` | WMS shelf layout export — Right side location codes |

## Mission File Format

```
x, y, z, yaw, type, param, LeftLocation|RightLocation
```

| Field | Values |
|---|---|
| type | 0 = home, 2 = scan point, 3 = waypoint |
| param | 2 = scan, 6 = turn/navigate, 7 = start, 8 = end |
| names | `LeftLocation\|RightLocation` — only on scan lines (type 2) |

## Shelf Location Code Convention

`G [aisle] [bay_code] [level] [slot]`

- **Aisle:** 2-digit (e.g. `06` for N06)
- **Bay codes (Left):** 22, 20, 18 … 02 (even, bay1→bay11)
- **Bay codes (Right):** 21, 19, 17 … 01 (odd, bay1→bay11)
- **Level:** 1–7 (single digit)
- **Slot:** 10 = front (drone-facing), 20 = back

Example: `G0622210` = Aisle 06, Left bay1, Level 2, front

## Current Output (Aisle N06, Yusen BMW)

- `YBMWG06_60LO_MAIN.mission` — Full 7-level serpentine scan, all bays
- `TAG_BMW_Yusen_Main_E2.json` — ArUco tags E2 configuration
- `Layout_Locate_G06_L7_WMS_L.csv` / `_R.csv` — WMS layout input for both sides

## Naming Convention

`Y[customer][aisle][scan_spec]_[variant].mission`
- `Y` = Yusen, `BMW` = BMW site, `G06` = Aisle N06, `60LO` = 60-location scan
