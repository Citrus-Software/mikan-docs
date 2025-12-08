# spine.legacy

> cartoon-friendly spline IK template module

## Overview

`spine.legacy` builds a flexible, cartoon-oriented spine rig based on a spline IK.
It is designed to sit between the pelvis and upper body (shoulders) and to work with the other template modules (arms, legs, etc.) in a consistent way.

The module provides:

- A spline-IK based deformation chain for smooth bending and stretching
- FK/IK-style controllers along the spine curve
- Optional automatic orientation helpers (pelvis, mid-spine, shoulders)
- Several modes to control how deformation bones are distributed and how stretch is computed

## Template Structure

- The root of the template defines the pelvis position.
- The rest of the chain follows the spine up to the shoulders and neck.
- The spline IK curve is created from these template joints and drives the deformation chain used for skinning.
- You do not need to manually orient the template joints:
    - Base orientation is handled during rig build.
    - Additional automatic re-orientation can be enabled via the `orient` options.

The spine module is typically parented under a World template module and acts as the bridge between lower body (legs) and upper body (arms / neck / head).

## Options

**Skin Bones & Distribution**

- `bones` (*int*, default: `6`): Number of deformation bones generated along the spline IK for skinning.
- `bones_length` (*enum*, default: `equal`): Controls how the skin joints are distributed along the spline:
  - `parametric` :  Bones are distributed according to the curve parameter (even spacing in parameter space).
  - `cvs` : Bones are distributed relative to the positions of the FK controllers along the curve.
  - `equal` : Each bone has the same length along the deformation, giving evenly sized segments.

**Orientation Helpers**

Automatic orientation of key controllers and IK handles. When enabled, the module adjusts local axes based on their parent and the underlying spline.

- `orient_spine` (*bool*, default: `off`)
  Automatically orients c_spine1 and c_spine2 along their primary axis.
- `orient_pelvis` (*bool*, default: `off`)
  Automatically orients c_pelvis and pelvis_IK along their axis, relative to their parent.
- `orient_shoulders` (*bool*, default: `off`)
  Automatically orients c_shoulders_IK along its axis, relative to its parent.

**Pelvis Pivot**

Defines where the pivot of c_pelvis_IK is placed. This affects how the hips rotate and how motion propagates into the spine.

- `pivots` (*enum*, default: `legacy`)
  - `legacy` : Pivot is at the root (historical / backward-compatible behavior).
  - `spine1` : Pivot is placed at the first FK spine controller.
  - `centered` : Pivot is placed between the two intermediate FK spine controllers.

**Stretching**

Controls stretch behavior for the spline IK spine.

- `default_stretch` (**bool**, default: `on`)
  Enables spine stretching by default on rig build.
- `stretch_mode` (**enum**, default: `translate`)
  Defines how stretch is computed and applied along the spline:
  - `scale` – Joints are scaled along the curve to achieve stretch.
  - `translate` – Joints are translated along the curve to follow the stretched length (preserves joint scales, useful in some pipelines).
