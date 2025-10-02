# Subdiv

Polygon subdivision (surface smoothing).


## Features

- Applies polygonal subdivision to a mesh using Maya’s `polySmooth` node.
- Reads/writes the subdivision **level** (`divisions`).
- Built with `keepBorders = false` (borders are smoothed) and `smoothUVs = true` (UVs are smoothed).

:::warning
    - Operates on polygon meshes.
    - Subdivision changes topology (adds vertices/edges/faces). Plan your downstream rig/deformer stack accordingly.
:::


## Options

- `level (int)` : number of subdivision levels (maps to `divisions` on `polySmooth`)
- `keep_borders (bool, default: off)` : keep border edges unsmoothed  
- `smooth_uvs (bool, default: on)` : smooth UVs along with vertices  


## Hooks

- `enable`, `envelope (float, min: 0, max: 1)` : global application weight