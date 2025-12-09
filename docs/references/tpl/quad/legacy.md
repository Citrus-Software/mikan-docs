# quad.legacy

> Full IK/FK quadruped leg rig template module

## Overview

`quad.legacy` provides a complete IK/FK rig for a quadruped leg.
It shares a common module base with arm.legacy and leg.legacy, meaning their construction and logic are largely identical.

The module combines:

- A 2-joint IK chain plus an **aim constraint** on the 3rd joint for quadruped-style legs
- Optional clavicle support for front legs
- Reverse foot setup for stable contact and foot rolls
- Stretch, soft IK, and squash-volume compensation
- Twist and roll joints for smooth deformation

## Template Structure

- The **root of the template** marks the hip position.
- The rest of the chain defines, in order:
  - knee
  - ankle
  - tarsus (heel/mid-foot)
  - toe tip

- You do **not need to orient** the joints manually. Orientation is handled automatically during rig build.
- The **heel joint** is placed separately and defines the pivot of the reverse foot system.

For forelegs, an **optional clavicle joint** can be added and parented backwards to the hip. This joint defines the pivot for the clavicle rig, to which the leg will be parented.

The template hierarchy is **strict and must not be modified**, otherwise the build may fail or produce unexpected results.

## Options

**Quadruped Type**

- `quad_type` (**enum**, default: `down`)
  Defines how the quadruped leg IK is oriented. The quad leg uses a 2-joint IK chain with the third joint driven by an aim constraint pointing to the opposite end of the chain.
  - `down` : The 3rd joint (ankle_quad) aims toward the hip.
  - `up` : The 1st joint (hip) aims toward the foot.

**Clavicle and Reverse Foot**

- `clavicle` (**bool**, default: `on`)
  Enables the clavicle rig, using the optional clavicle template joint as the pivot for the front leg.
- `reverse_lock` (**bool**, default: `off`)
  Enables the reverse foot locking system (heel / ball / toe pivots).
- `bank` (**bool**, default: `off`)
  Enables lateral foot banking controls within the reverse foot system, allowing the animator to tilt the foot inward or outward.

**Effector Plane & Pole Vector**

- `effector_plane` (**enum**, default: `ground`)
  Up-vector mode for auto-orienting the 3 main “foot” joints.
  - `x, y, z` : World up vectors
  - `auto` : Auto up vector based on joint geometry (good for wings)
  - `ground` : Forces perpendicular-to-ground orientation (useful for feet)
- `pv_space` (**id**, default: `space.cog`)
  Global parent space for the pole vector.
- `pv_space_default` (**float**, min: `0`, max: `1`, default: `1`)
  Default weight for pole-vector space switching.

**IK Stretching**

- `default_stretch` (**bool**, default: `on`)
  Enables IK stretching at build time.
- `soft_distance` (**float**, min: `0`, max: `1`, default: `0.05`)
  Threshold (percentage of stretch distance) where soft IK starts to blend in.
- `stomp_power` (**float**, min: `-1`, max: `0`, default: `-0.5`)
  Power falloff used for squash/volume compensation.
  -0.5 corresponds to inverse square-root falloff.

**Interpolation & Roll Joints**

- `blend_joints` (**bool**, default: `on`)
Enables roll joints at key articulations for deformation smoothing.

**Twist System**

- `splits_up` (**int**, min: `2`, default: `3`)
- `splits_mid` (**int**, min: `2`, default: `3`)
- `splits_dn` (**int**, min: `2`, default: `3`)
  Number of twist joints for upper, mid, and rear sections of the leg.

**Space Switching**

- `space_switch` (**bool**, default: `on`)
  Adds default space-switch modes to important controllers.

### Not Yet Implemented

- `aim_axis` (*enum*, default: `-y`): Not yet supported.
- `up_axis` (*enum*, default: `-x`): Not yet supported.
- `up_axis2` (*enum*, default: `z`): Not yet supported.