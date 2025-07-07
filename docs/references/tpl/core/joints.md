# core.joints

> Chain-based joint rig template module

## Overview

`core.joints` creates a rig controller for each joint in the input template chain. Each controller consists of a hierarchy of nodes used for deformation and animation. By default, the structure includes:

- A `root` node
- A controller (`ctrl`)
- A `skin` node for deformation

## Options

- `type` (*enum*, default: `joint`): Rig hierarchy type.
  - `joint`: All rig joints are parented under their respective roots, allowing proper scale transmission.
  - `transform`: Controllers form a continuous hierarchy, resulting in cumulative scaling.
- `unchain` (*bool*, default: `off`): Prevents hierarchical parenting between generated controllers.
<!-- -->
- `do_pose` (*bool*, default: `on`): Adds a `pose` node above the controller (for driven key control).
- `add_nodes` (*yaml*, default: `null`): Injects additional nodes into the controller hierarchy.
  - `inf`: Adds an `inf_{name}` node above the controller.
  - `[inf, pose]`: Adds `inf_{name}` and `pose_{name}` in that order.
  - `[c, dyn]`: Adds a `dyn_{name}` node between the controller and the skin joint.

### Transform Behavior

- `parent_scale` (*bool*, default: `off`): Enables scale propagation between controllers (segment scale compensate between `skin` and `root`).
- `rotate_order` (*enum*, default: `xyz`): Rotate order applied to all rig elements.

### Orientation

- `flip_orient` (*bool*, default: `off`): Flips root orientation for symmetrical translation behavior.


