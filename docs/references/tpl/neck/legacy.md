# neck.legacy

> cartoon-friendly variable-length neck module

## Overview

`neck.legacy` builds a flexible cartoon-style neck rig using a spline IK.
It provides smooth bending and optional rigidity controls, and integrates cleanly with the spine.legacy module.

The module provides:

- A spline IK deformation chain suitable for expressive animation
- FK controllers along the neck for posing
- Optional automatic orientation of FK controls
- Stretching and rigidity shaping along the spline
- Default space-switching capabilities for the main controllers

## Template Structure

- The root of the template defines the base of the neck (usually attached to the upper spine).
- The last joint defines the head base position.
- The spline IK curve is built between these joints and drives the deformation bones used for skinning.
- You do not need to orient template joints manually; base orientation is handled automatically during rig build, with additional options for controller auto-orientation if needed.

## Options

**Skin Bones & Distribution**

- `bones` (**int**, default: `2`)
  Number of deformation bones generated along the neck spline.
- `bones_length` (**enum**, default: `equal`)
  Controls how skin joints are distributed along the spline:
  - `parametric` : Bones are spaced according to the curve parameter.
  - `equal` : Matches uniform length distribution, creating evenly sized deformation segments.

**Orientation Helpers**

- `orient_neck` (**bool**, default: `on`)
  Automatically orients the FK controllers along their primary axis.
- `Axis definitions`:
  - `aim_axis` (**str**, default: `y`)
    Axis used to aim the FK controls along the neck direction.
  - `up_axis` (**str**, default: `x`)
    Up vector used to define the secondary orientation of the FK controls.

**Stretching**

- `default_stretch` (**bool**, default: `on`)
  Enables spline IK stretching by default, allowing the neck to elongate for cartoon-style motion.
- `stretch_mode` (**enum**, default: `scale`)
  Defines how stretch is computed and applied along the neck:
  - `scale` – Joints are scaled along the curve to achieve stretch.
  - `translate` – Joints are translated along the curve to follow the stretched length 

**Rigidity** (Tangents Rig)

Adds tangent-based rigidity to the spline IK to control how much the neck behaves like a bendy tube vs. a more solid structure.

- `rigidity` (**bool**, default: `on`)
  Enables tangential rigidity controls on the spline IK.
- `rigidity_dn` (**float**, default: `1`, min: `0`, max: `1`)
  Default rigidity at the base of the neck (1 = very rigid, 0 = very flexible).
- `rigidity_up` (**float**, default: `0`, min: `0`, max: `1`)
  Default rigidity near the head base (0 = flexible, 1 = rigid).

This allows progressive stiffness along the neck .(e.g., stable at the bottom, looser toward the head).

**Space Switching**

`space_switch` (**bool**, default: `on`)
 Enables default space-switch modifiers on the main controllers, allowing animators to re-parent neck controls to various spaces (head, world,etc.) depending on the production setup.

- `offset ctrl` : Adds an additive controller on top of the neck module.