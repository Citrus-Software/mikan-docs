# core.bones

> Generic FK controller chain module

## Overview

The `core.bones` module generates a controller rig for each joint in a template chain. Each controller is built from a hierarchy of nodes that support both deformation and animation layers. By default, the structure includes:

- A `root` node
- A controller (`ctrl`)
- A `skin` node (deformation output)

## Options

- `do_pose` (*bool*, default: `off`): Adds a `pose` node between the root and controller (for driven key control).
- `add_nodes` (*str* | *list[str]*, default: `null`): Adds custom nodes to the hierarchy.
  - Example: `inf` inserts a node `inf_{name}` above the controller.
  - Example: `[inf, pose]` inserts both nodes `inf_{name}` and `pose_{name}` in order.
  - Example: `[c, dyn]` adds `dyn_{name}` between the controller and the skin joint.

### Transform Behavior

- `parent_scale` (*bool*, default: `off`): Enables scale propagation between controllers (affects segment scale compensation).
- `rotate_order` (*enum*, default: `xyz`): Sets the rotation order on the controller hierarchy.

### Orientation

- `flip_orient` (*bool*, default: `off`): Flips root orientation to produce symmetrical translation behavior.
- `orient` (*enum*, default: `copy`): Sets the orientation strategy for the controller rig.
  - `copy`: Copies orientation from the template joint.
  - `auto`: Automatically orients joints using the options below.
  - `world`: Aligns orientation to the world axes.
  - `parent`: Aligns to the parent rig joint.

#### Auto Orientation Controls

- `aim_axis` (*str*, default: `y`): Main axis pointing toward the next joint in the chain.
- `up_axis` (*str*, default: `z`): Secondary axis used for local up vector.
- `up_dir` (*enum*, default: `auto`): Mode for computing the up vector.
  - `auto`: Based on geometry of the joint chain.
  - `+x`, `-x`, `+y`, `-y`, `+z`, `-z`: Fixed world-space directions.
- `up_auto` (*enum*, default: `average`): Strategy for `auto` up vector calculation.
  - `average`: Averages all segment triangles.
  - `each`: Computes individual up vectors per joint.
  - `first`: Uses first segment’s up vector for all.
  - `last`: Uses last segment’s up vector for all.

