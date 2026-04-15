---
title: rig.path
description: Attaches one or more nodes to a NURBS curve using advanced path or projection constraints.
---

# rig.path

Attaches one or more nodes to a curve, acting as a highly customizable motion path.

This modifier is the "Swiss Army Knife" of curve attachment. It can be used for simple motion paths, dynamic closest-point projections (like a zipper or sliding skin), or complex interactive systems like belts and tank tracks. It generates a dedicated control group that mathematically follows the curve, onto which your target nodes are attached.

## Parameters

Because this modifier handles many different use cases, its parameters are divided into four logical categories: Hierarchy, Positioning, Orientation, and Advanced Interactive behaviors.

### Core & Hierarchy

| Parameter        | Type         | Default         | Description                                                                                                                |
|:-----------------|:-------------|:----------------|:---------------------------------------------------------------------------------------------------------------------------|
| `geo`            | *node*       |                 | The curve (geometry) the path will follow. Expected format: `cv_name->shape`.                                              |
| `nodes` / `node` | *list[node]* | `self.node`     | The node(s) to attach to the curve. By default, their initial position is used to calculate where they spawn on the curve. |
| `parent`         | *node*       | `node.parent()` | The parent group for the generated technical path setup.                                                                   |
| `hook`           | *bool*       | `on`            | If `on`, the targeted `nodes` are automatically re-parented under the generated path transform.                            |
| `snap`           | *bool*       | `off`           | If `on`, zeroes out the transforms of the `nodes` so they perfectly snap to generated path transform.                      |

### Positioning & Parameterization

These settings dictate how the position along the curve is calculated.

| Option    | Type    | Default  | Description                                                                                                                                                                      |
|:----------|:--------|:---------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `u`       | *float* | *(Auto)* | Hardcodes the initial curve parameter. If omitted, Mikan automatically projects the node's starting position onto the curve to find the closest `u` value.                       |
| `percent` | *bool*  | `off`    | If `on`, normalizes the curve length to `0.0 - 1.0` instead of using the raw parametric `u` values of the NURBS curve. Highly recommended for animation controllers.             |
| `length`  | *bool*  | `off`    | Forces distance-based parameterization (MotionPath) instead of parameter-based (PointOnCurve). This ensures constant speed sliding, but disables standard Up-Object orientation. |
| `closest` | *node*  |          | **Dynamic Projection:** Instead of a static `u` value, the position on the curve is continuously driven by the closest point to this external node.                              |
| `attach`  | *node*  | `node`   | The reference node used to calculate the static initial `u` projection (defaults to the driven node itself).                                                                     |

### Orientation & Vectors

| Option             | Type         | Default     | Description                                                                                                                       |
|:-------------------|:-------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------|
| `orient`           | *bool*       | `on`        | Whether the path should drive the rotation of the attached nodes.                                                                 |
| `forward_vector`   | *str / list* | `[1, 0, 0]` | The local axis pointing down the curve (e.g., `x`, `-z`, or `[1, 0, 0]`).                                                         |
| `up_vector`        | *str / list* | `[0, 1, 0]` | The local up axis.                                                                                                                |
| `up_object`        | *node*       |             | An external transform node used to aim the `up_vector` towards. Essential for preventing flipping on 3D curves.                   |
| `up_object_vector` | *str / list* | `[0, 0, 0]` | An absolute world-space up vector (used if `up_object` is not provided).                                                          |
| `geo_up`           | *node*       |             | A secondary NURBS curve. If provided, Mikan will generate a secondary path on this curve to act as the `up_object` automatically. |
| `flip`             | *bool*       | `off`       | Inverts both the forward and up vectors if the mod is executed in a mirrored branch.                                              |

### Interactive Mode (`io`)

| Option | Type   | Default | Description                                                                                                                                                                                |
|:-------|:-------|:--------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `io`   | *bool* | `off`   | **Advanced Mode:** Unlocks interactive features on the generated path node, perfect for belts, treads, or sliding systems. *(See the Outputs section below for the generated attributes).* |

## Outputs

Because `rig.path` is often an intermediate step, it generates specific nodes, IDs, and attributes that other modifiers (like `plug` or `drive`) can connect to.

### Generated Nodes & IDs

For every node attached, Mikan generates a parent transform named **`path_<geo_name>`**.

- **ID Registration:** This generated transform is registered in Mikan with the ID **`<id>::mod.path.<index|name>`**.
- **Secondary IDs:** If `geo_up` was used, the secondary up-path is registered as **`<id>::mod.path.up.<index|name>`**.

### Animatable Attributes

The generated `path` transform always exposes the following attributes:

- **`@u`**: The parameter driving the position. (Ranging from `0` to `max_u`, or `0` to `1` if `percent: on`). **This is the plug you want to connect to slide the object.**
- **`@length`**: The live, dynamically calculated length of the curve.
- **`@length0`**: The original resting length of the curve.

### Interactive Attributes (if `io: on`)

If the `io` (Interactive) flag is enabled, standard parameterization is bypassed, and the `path` transform exposes an advanced sliding dashboard:

- **`@sliding_behavior`** *(Enum)*: Defines how the object reacts if the curve stretches.
    - `parametric`: Sticks to the curve's UV space.
    - `slide_stretch`: Stretches proportionally with the curve.
    - `slide_keep_distance_start`: Maintains absolute distance from the start of the curve.
    - `slide_keep_distance_end`: Maintains absolute distance from the end of the curve.
- **`@offset`**: Animatable float to continuously slide the object along the path.
- **`@loop`**: *(Bool)* If `true`, the object wraps around when reaching the end of the curve (ideal for tank tracks or conveyor belts).

## Examples

### Standard Normalized Slider

Attaches a controller to a curve. `percent: on` ensures the `@u` attribute ranges cleanly from 0 to 1, making it easy to drive via a `plug` modifier.

:::warning[Build Order (Priority)]
When using `snap: on`, you must ensure that your target node is fully built and bound *before* the modifier moves it to the start of the curve. To enforce this, assign a negative priority (e.g., `#!-10`) to the mod block. This guarantees the path snapping executes after the initial cart setup.
:::

```yml
rig.path:
  geo: cv_path->shape
  node: cart::roots.0

  orient: on
  u: 0
  percent: on
  length: on
  snap: on

  # axes
  forward_vector: z
  up_vector: x
  geo_up: cv_path_up->shape

plug:
  node: cart::ctrls.0
  path: { k: on, min: 0, max: 100 }

expression:
  op: u = path * 0.01
  u: world::mod.path.0@u
  path: cart::ctrls.0@path
```

:::info[Demo Scene]
To see this setup in action, download the [**mod_path_cart.ma**](https://drive.google.com/file/d/1BhCEbFqqXz_rApBXBlp86DfsUPNTFRd8/view?usp=drive_link) demo scene from our [Google Drive folder](https://drive.google.com/drive/folders/1tDXJmNxd-3ev1BwvZMm4Gl7tbnJTWJcn?usp=drive_link).
:::

### Closest Point Projection (Dynamic)

The generated path will constantly slide along `cv_necklace` to stay as close as possible to the `chest::skin.0` node.

```yml
rig.path:
  geo: cv_necklace->shape
  node: necklace::roots.0
  closest: chest::skin.0
```
