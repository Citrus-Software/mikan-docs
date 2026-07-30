---
title: core.xform
description: Generates a single transform, joint, or control locator node.
---

# core.xform

Generates a single transform, joint, or control locator node from a single template guide.

This lightweight module is ideal for simple positional markers, space switches, dynamic targets, zero-out groups, or isolated single-controller elements. It offers full flexibility over node typing (Transform vs Joint), rotation order, orientation matching, and custom shape display.

Depending on your parameters, the default structure generates:

- An optional `root` buffer node
- A main transform, joint, or controller node (`node` / `c_` / `sk_`)
- Optional locator or curve shapes

## Structure

The module processes a single point/transform guide in the template space.

Unlike complex chain modules, `core.xform` generates only a single node (or a root/child pair) and does not parse children hierarchies. If flagged as a controller or skin joint, it automatically adopts standard production prefixes (`c_` or `sk_`).

## Parameters

### Common Options

| Parameter | Type   | Default | Description                                                                          |
|:----------|:-------|:--------|:-------------------------------------------------------------------------------------|
| `do_ctrl` | *bool* | `off`   | Tags the node as an animation controller and automatically prepends the `c_` prefix. |
| `do_skin` | *bool* | `off`   | Tags the node as a deformation joint and automatically prepends the `sk_` prefix.    |

### Generation & Node Typing

| Parameter | Type   | Default | Description                                                                                                                                    |
|:----------|:-------|:--------|:-----------------------------------------------------------------------------------------------------------------------------------------------|
| `prefix`  | *str*  | `""`    | Adds a custom prefix to the generated node name. Automatically prepended before explicit system prefixes if `do_ctrl` or `do_skin` are active. |
| `root`    | *bool* | `off`   | When enabled, creates a parent `root` buffer group above the main locator node to isolate incoming transformations.                            |
| `joint`   | *bool* | `off`   | Creates a `joint` nodes instead of standard `transform` nodes for both the root and the locator.                                               |                                                   |

### Transform & Behavior

| Parameter      | Type   | Default | Description                                                                                                    |
|:---------------|:-------|:--------|:---------------------------------------------------------------------------------------------------------------|
| `rotate_order` | *enum* | `xyz`   | Sets the rotation order on the locator and optional root hierarchy (`xyz`, `yzx`, `zxy`, `xzy`, `yxz`, `zyx`). |

### Orientation

| Parameter      | Type   | Default | Description                                                                                                                                   |
|:---------------|:-------|:--------|:----------------------------------------------------------------------------------------------------------------------------------------------|
| `copy_orient`  | *bool* | `on`    | Matches the orientation directly from the template guide transform. If `off`, rotations are zeroed out in world space.                        |
| `flip_orient`  | *bool* | `off`   | Flips the root orientation (180° on local X) to produce symmetrical translation behavior on mirrored modules.                                 |
| `local_orient` | *bool* | `off`   | When `root` is enabled, transfers the rotation values into the child joint's `jointOrient` channel (`jo`) and zeroes out the root's rotation. |

### Display & Shapes

| Parameter     | Type   | Default | Description                                                                                                                             |
|:--------------|:-------|:--------|:----------------------------------------------------------------------------------------------------------------------------------------|
| `locator`     | *bool* | `off`   | Appends a locator shape to the node for viewport visualization.                                                                         |
| `copy_shapes` | *bool* | `off`   | Copies custom NURBS curve shapes directly from the template node onto the generated locator (handles mirrored inversion automatically). |

## Outputs

Once built, the module exposes a minimal DAG node structure and exposes hooks for downstream attachments.

### DAG Node Tree

Below is the node hierarchy generated based on parameter configurations:

```text
[hook]
 └── [root]          <-- Optional: created when 'root' is enabled
      └── [node]     <-- Transform, Joint, Controller (c_), or Skin (sk_)
           └── [shp] <-- Optional: locator or NURBS shape
```

### Node IDs

- `<id>::node`: The primary generated locator node (transform or joint).
- `<id>::roots.node`: The parent buffer node (only exposed if `root` is on).
- `<id>::ctrls.node`: Exposed if `do_ctrl` is enabled.
- `<id>::skin.node`: Exposed if `do_skin` is enabled.

### Hooks

- `<id>::node`: The primary attachment hook, mapped directly to the generated node.

## Usage & Rigging Notes

### Simple Hierarchies, Sets & Props

For lightweight rigs (such as sets, props, or simple assets) requiring straightforward parent-child chains with control layers, `core.locator` serves as the fundamental building block.

Enabling the `root` parameter automatically builds a zero-out buffer hierarchy, providing the standard structural node layout needed for clean transform management without overhead.

### Decoupling Translation and Rotation Spaces

A powerful yet advanced capability of this module is the ability to decouple translation space from local rotation space using joint orientation.

When `joint: on`, `root: on`, and `local_orient: on` are enabled, the orientation of the template is transferred directly to the child joint's `jointOrient` attribute. Consequently:

- The **parent `root`** stays aligned to the incoming hierarchy (or world axis), driving the primary translation axes.
- The **child `ctrl`** inherits local rotation axes tailored specifically to the targeted geometry.

#### Practical Examples:

- **Prop Layout (e.g., Row of Chairs):** In a scene with angled chairs, animators usually expect translation axes aligned with the room (world/parent space), but rotation axes local to each individual chair. Decoupling ensures all translation curves remain uniform across the set while keeping local rotation pivots aligned to the mesh.
- **IK Control Alignment:** This mimics standard Hand and Foot IK control setups, where translation operates in world space while rotation operates in local component space.

### Embedding Utility Curves in Template Space

For setups requiring NURBS curves directly within the rig hierarchy (such as custom facial deformation drivers or ribbon pathways) `core.xform` allows you to embed shapes without relying on external geometry helper files.

By pairing `copy_shapes: on` with custom shapes parented to the template node's transform root, the built module cleanly instantiates and transfers the curve shape directly into the final rig DAG tree. This keeps all specialized helper curves centralized within the template definition, streamlining asset updates and scene dependency management.
