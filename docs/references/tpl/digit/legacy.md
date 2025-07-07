# digit.legacy

> Finger rig module based on the `core.bones` structure

## Overview

`digit.legacy` creates a controller rig for each joint in a digit (finger) chain, following the standard rig structure used in `core.bones`. Each controller setup includes a hierarchy of nodes: a root, a posing node (for driven keys), a controller, and a skinning node.

## Options

- `meta` (*bool*, default: `on`): Renames the first controller in the chain with the `meta` suffix and excludes it from aim behavior.
- `shear` (*bool*, default: `off`): Enables a shearing rig. Adds two extra joints per phalanx (base and tip).
- `shear_values` (*list[float]*): Default shearing values (editable after rig creation).
- `target_parent` (*node*, default: `*::hooks.digits`): Parent ID used for the aim rig. Prioritizes parent template.
- `target_weights` (*list[float]*): Default aim constraint weights per phalanx.

### Node Hierarchy Options (same as `core.bones`)

- `do_pose` (*bool*, default: `off`): Adds a posing node above the controller.
- `add_nodes` (*str*, default: `null`): Adds additional nodes to the controller hierarchy.
  - Example: `inf` creates an `inf_{name}` node above the controller.
  - `[inf, pose]` adds `inf_{name}` and `pose_{name}` in order.
  - `[c, dyn]` adds a `dyn_{name}` node between controller and skin joint.
- `parent_scale` (*bool*, default: `off`): Enables scale transmission between controllers (segment scale compensation).

### Orientation and Rotation

- `rotate_order` (*enum*, default: `xyz`): Rotation order for all rig elements.
- `orient` (*enum*, default: `copy`): Orientation mode for the joint chain.
  - `copy`: Matches template orientation.
  - `auto`: Automatic orientation (see axis options below).

#### Orientation Axes

- `aim_axis` (*str*, default: `y`): Main aim axis for controller orientation.
- `up_axis` (*str*, default: `z`): Up axis for controller orientation.
- `up_dir` (*enum*, default: `auto`): Determines up vector calculation mode.
  - `auto`: Calculated from the geometry of the chain.
  - `+x`, `-x`, `+y`, `-y`, `+z`, `-z`: Scene-based up vectors.
- `up_auto` (*enum*, default: `average`): Auto up calculation method.
  - `average`: Averages triangle normals.
  - `each`: Calculates unique up for each joint.
  - `first`: Uses first triangle's up vector for the entire chain.
  - `last`: Uses last triangle's up vector for the entire chain.
