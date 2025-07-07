# arm.legacy

> Full IK/FK arm rig template module

## Overview

`arm.legacy` provides a complete arm rig with IK/FK blending. It shares a common module base with `leg.legacy` and `quad.legacy`, meaning their construction and logic are nearly identical.


## Template Structure

- The root of the template marks the shoulder position.
- The rest of the chain defines the elbow, wrist, carpal, and fingertip.
- You **do not** need to orient joints manually; orientation is handled automatically during rig build.
- An optional clavicle joint can be placed *above* the shoulder joint. This acts as the pivot for the clavicle rig, which the arm will then be parented to.


## Options

- `clavicle` (*bool*, default: `on`): Enables the clavicle rig.
- `reverse_lock` (*bool*, default: `off`): Enables a reverse foot-style locking system.
- `effector_plane` (*enum*, default: `z`): Orientation vector used to auto-orient the wrist joint chain.
  - `x`, `y`, `z`: World-space up vectors.
  - `auto`: Based on the plane formed by the joint positions (useful for wings).
  - `ground`: Forces wrist orientation perpendicular to the ground plane (used for feet).
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

- `twist_joints_up` (*int*, min: `2`, default: `3`): Number of twist joints between shoulder and elbow.
- `twist_joints_dn` (*int*, min: `2`, default: `3`): Number of twist joints between elbow and wrist.
- `add_chains` (*list[str]*, optional): Adds multiple twist/shear chains for advanced deformation control.
- `advanced_twist` (*bool*, default: `off`): Adds a correction rig for twist artifacts using extra attributes.
- `space_switch` (*bool*, default: `on`): Enables default space switches on key controllers.

### Not Yet Implemented

- `aim_axis` (*enum*, default: `-y`): Not yet supported.
- `up_axis` (*enum*, default: `-x`): Not yet supported.
- `up_axis2` (*enum*, default: `z`): Not yet supported.

