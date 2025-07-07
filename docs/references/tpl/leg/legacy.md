# leg.legacy

> Full IK/FK leg rig template module

## Overview

`leg.legacy` provides a complete leg rig with IK/FK blending. It uses the same module base as `arm.legacy` and `quad.legacy`, making their structure and configuration nearly identical.

## Template Structure

- The root of the template marks the hip position.
- The rest of the chain defines the knee, ankle, tarsus, and toe tip.
- Joints do **not** need manual orientation; orientation is applied automatically at rig build.
- A heel joint must be placed separately and defines the pivot for the reverse foot rig.
- An optional clavicle joint can be placed above the hip. It defines the clavicle pivot and acts as the parent for the corresponding arm rig.

## Options

- `clavicle` (*bool*, default: `on`): Enables the clavicle rig.
- `reverse_lock` (*bool*, default: `off`): Enables a reverse foot-style locking system.
- `effector_plane` (*enum*, default: `z`): Orientation vector used to auto-orient the foot joint chain.
  - `x`, `y`, `z`: World-space up vectors.
  - `auto`: Based on the plane formed by joint positions.
  - `ground`: Forces foot orientation perpendicular to ground (ideal for feet).
- `pv_space` (*node*, default: `*::space.cog`): Global parent space for the pole vector.
- `pv_space_default` (*float*, `0-1`, default: `0`): Default weight for the pole vector space switch.

### IK Stretching

- `default_stretch` (*bool*, default: `on`): Enables IK stretching by default.
- `soft_distance` (*float*, `0-1`, default: `0.05`): Soft IK activation threshold relative to stretch distance.
- `stomp_power` (*float*, `-1 to 0`, default: `-0.5`): Volume compensation power factor. `-0.5` corresponds to inverse square root falloff.

### Interpolation

- `smooth_type` (*enum*, default: `length`): Mode for distributing intermediate joints along the limb.
  - `length`: Interpolated along the deformation.
  - `parametric`: Interpolated at fixed parameter points.
- `blend_joints` (*bool*, default: `on`): Enables skinning roll joints at key articulations.

### Twist System

- `twist_joints_up` (*int*, min: `2`, default: `3`): Number of twist joints between hip and knee.
- `twist_joints_dn` (*int*, min: `2`, default: `3`): Number of twist joints between knee and ankle.
- `add_chains` (*list[str]*, optional): Adds multiple twist/shear chains for advanced deformation control.
- `advanced_twist` (*bool*, default: `off`): Adds a correction rig for twist artifacts using extra attributes.
- `space_switch` (*bool*, default: `on`): Enables default space switches on key controllers.

### Not Yet Implemented

- `aim_axis` (*enum*, default: `-y`): Not yet supported.
- `up_axis` (*enum*, default: `-x`): Not yet supported.
- `up_axis2` (*enum*, default: `z`): Not yet supported.
