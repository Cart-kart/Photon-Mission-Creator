# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project: Photon Mission Creator

Tooling for generating Avilon Photon drone mission files from warehouse shelf layout data (WMS exports).

- **Related repos:** Cart-kart/Yusen-Drone-Dashboard (viewing dashboard)
- **Note:** X/Y/Z coordinates, transit heights, and bay positions are warehouse-specific — each site has its own spec. Do not assume values from one site apply to another.

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

## Scan Pattern

**Serpentine** — drone alternates direction each level, so no wasted traversal at level transitions:
- Level N ends at one end of the aisle → Level N+1 starts from the same end, just rises in Z
- Turn waypoints (`type 3, param 6`) are placed only when the drone needs to cross to the other end

## Shelf Location Code Convention

`G [aisle] [bay_code] [level] [slot]`

- **Aisle:** 2-digit (e.g. `06` for N06)
- **Bay codes (Left):** even numbers descending (22 → 02, step −2, bay1 → bay11)
- **Bay codes (Right):** odd numbers descending (21 → 01, step −2, bay1 → bay11)
- **Level:** single digit (1–7)
- **Slot:** 10 = front (drone-facing), 20 = back

Example: `G0622210` = Aisle 06, Left bay1, Level 2, front

## WMS CSV Structure

```
Direction, Scroll, Rack rows → then one Rectangle row per storage location
```
- `RelativeX` = bay position (increments per slot, site-defined step)
- `RelativeY` = level (increments per level, site-defined step)
- Left CSV and Right CSV are separate files, mirrored layout

## Naming Convention

`[CustomerPrefix][aisle][scan_spec]_[variant].mission`

Example: `YBMWG06_60LO_MAIN.mission`
- `Y` = Yusen, `BMW` = BMW site, `G06` = Aisle N06, `60LO` = 60-location scan, `MAIN` = primary variant
