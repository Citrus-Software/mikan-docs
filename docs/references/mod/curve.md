---
title: curve
description: Creates a curve by connecting nodes' transforms to curve CVs.
---

# curve

Creates a curve by connecting nodes' transforms to curve CVs.

This modifier generates a curve where each control vertex (CV) is dynamically driven by the local matrix of a specified node. By default, these curves are configured as **visual helpers**: they are visible in the viewport but remain unselectable, preventing accidental interactions by the animator. They are perfect for drawing schematic lines between
controllers or joints to clarify logical connections within the rig. However, since the output remains a standard NURBS curve, it can also be used for advanced rigging setups, such as driving a **Wire Deformer** or serving as a track for a **Motion Path** (often paired with the [`rig.path`](rig/path.md) modifier).

## Parameters

| Parameter | Type         | Default              | Description                                                                                           |
|-----------|--------------|----------------------|-------------------------------------------------------------------------------------------------------|
| `nodes`   | *list[node]* |                      | Nodes whose local matrices will drive the curve CVs. **Must contain at least `degree + 1` nodes**.    |
| `parent`  | *node*       | Parent of `nodes[0]` | Optional parent for the generated curve. Defaults to the hierarchy parent of the first targeted node. |
| `degree`  | *int*        | `1`                  | Degree of the resulting NURBS curve (`1` for linear, `2` for quadratic, `3` for cubic).               |
| `name`    | *str*        | optional             | Custom base name for the curve. Defaults to the formatted nice name of the node tag.                  |

:::caution Minimum Nodes Requirement
To successfully generate a NURBS curve, Maya requires a minimum number of control vertices based on the curve's degree. You must provide at least **`degree + 1`** nodes in your `nodes` list. For example:

- A linear curve (`degree: 1`) requires at least **2** nodes.
- A cubic curve (`degree: 3`) requires at least **4** nodes.
  :::

## Output

The generated curve is registered internally and can be accessed through the ID: `<id>::mod.curve.<name>`. In the scene, the physical curve node will be named following the pattern: `cv_<name>_<suffix>`.

:::tip Safe Reparenting
The generated curve supports seamless reparenting. Moving it to a different group or hierarchy in your rig will not break its deformations, as the CVs remain robustly driven by the source matrices.
:::

## Example

:::info Demo Scene
Explore how to build a procedural setup where a curve is generated from a joint rig, with a controller attached via a motion path. Download the [**`mod_curve.ma`**](https://drive.google.com/file/d/1vOWPyFqXK1RPV4M_QXdwsJqRD0I8beKE/view?usp=drive_link) demo scene from our [Google Drive folder](https://drive.google.com/drive/folders/1tDXJmNxd-3ev1BwvZMm4Gl7tbnJTWJcn?usp=drive_link).
:::

Creates a cubic helper curve driven by three spine controllers and parented under a specific hook.

```yml
curve:
  nodes:
    - spine::ctrls.0
    - spine::ctrls.1
    - spine::ctrls.2
  parent: spine::hooks.mid
  degree: 2
  name: spine_curve
```
