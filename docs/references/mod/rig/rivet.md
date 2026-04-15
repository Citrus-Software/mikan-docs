---
title: rig.rivet
description: Constrains a node to a point on the surface of a deforming mesh using UV pinning or closest-point projection.
---

# rig.rivet

Constrains a node to a specific point on the surface of a mesh.

This modifier is essential for attaching rigid or secondary elements to a deforming surface. It can be used to pin buttons to a jacket, stick horns to a deforming creature's head, or create sliding collision locators that glide across a skin mesh.

:::warning Freeze Scale Required
The target mesh (`geo`) must have its scale frozen at `(1, 1, 1)`. Unfrozen scaling will cause matrix projection errors and offset the rivet incorrectly.
:::

## Usage

Depending on your parameters, the modifier automatically chooses between three completely different mathematical approaches:

1. **Surface Pinning (Default):** The modifier finds the closest polygon to the node's starting position and permanently pins the rivet to that exact face using local UV/Edge space. The rivet will perfectly follow the mesh deformation.
2. **Closest Point Projection:** The rivet is not pinned to a specific face. Instead, it dynamically slides across the surface to always remain at the closest physical point to a moving reference node.
3. **Keepout (Collision):** Similar to Closest Point, but only activates if the reference node penetrates *inside* the mesh volume. It pushes the rivet back to the surface, acting as a fake soft-collision system.

:::tip Hierarchy & Scaling
The generated rivet transform is extremely stable and can be re-parented anywhere in your rig. While its position and orientation are mathematically locked to the mesh surface, **its scale is inherited from its parent**.

To ensure your rig scales correctly, choose your `parent` parameter wisely:

- For a soft accessory (like a button on a sleeve), parent the rivet under the arm hierarchy so it inherits the arm's scale.
- For a rigid object (like a heavy armor plate) that shouldn't squash or stretch with the limb, parent it under a global scaling space (e.g., `world::hooks.root`).
  :::

:::caution Build Order & Deformers
Mikan's standard build pipeline executes in this order: **Template Modules > Modifiers > Deformers**.
Because `rig.rivet` is a Modifier, targeting a geometry driven by a Deformer (like a SkinCluster) means the deformer might not exist yet when the rivet tries to build.

Mikan's smart scheduler usually detects this missing dependency and automatically pushes the rivet's execution to the end of the stack. However, for complex loop dependencies (e.g., a rivet driving a controller that deforms the *same* mesh), you may need to explicitly force the execution order using a [**priority tag**](/usage/modifiers.md#priorities) (e.g.,
`#!-50:`).
:::

## Parameters

### Core Setup

| Parameter           | Type   | Default         | Description                                                                                                                  |
|:--------------------|:-------|:----------------|:-----------------------------------------------------------------------------------------------------------------------------|
| `geo`               | *node* |                 | The target mesh to rivet onto. Expected format: `mesh_name->shape`.                                                          |
| `node`              | *node* | `self.node`     | The node to attach. Used by default to calculate the initial pin position.                                                   |
| `parent`            | *node* | `node.parent()` | The node under which the generated rivet transform will be parented.                                                         |
| `hook`              | *bool* | `on`            | If `on`, the `node` is re-parented inside the generated rivet. *(Automatically disabled if `closest` or `keepout` is used).* |
| `orient` / `rotate` | *bool* | `on`            | Aligns the rivet's orientation with the mesh's surface normal. *(Defaults to `off` if `closest` or `keepout` is used).*      |

### Advanced Projection Rules

| Option    | Type          | Default | Description                                                                                                                                                                                       |
|:----------|:--------------|:--------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `closest` | *bool / node* | `off`   | Enables dynamic closest-point sliding. If you pass a specific node (e.g., `closest: collision::ctrls.0`), the rivet will slide to follow that node. If set to `on`, it uses `node` as the source. |
| `keepout` | *bool / node* | `off`   | Enables collision mode. The rivet stays locked to the source node, unless the source node goes *inside* the mesh, at which point the rivet stops at the surface.                                  |
| `raycast` | *list[float]* |         | Overrides the closest-point projection origin with a hardcoded `[x, y, z]` vector instead of using the node's position.                                                                           |
| `subdiv`  | *int*         | `0`     | Internally adds a `subdivision` node to the mesh before projection. Increases projection precision on very low-poly meshes.                                                                       |

:::warning
Direct `uv` coordinate override is currently not supported in Tangerine and defaults to closest-point initial position).
:::

## Outputs

### Generated Nodes & IDs

When the modifier runs, it generates a parent transform group named **`rvt_<geo_name>`**.

- **ID Registration:** This generated transform is automatically registered in Mikan with the ID **`<id>::mod.rivet.<name>`**.

## Examples

### Basic Surface Pin (Button on a Shirt)

Pins `button::roots.0` to the shirt geometry. It will stick to that exact polygon forever and orient itself to the surface normal.

```yml
rig.rivet:
  geo: msh_shirt->shape
  node: button::roots.0
  orient: on
```

### Sliding Collision (Tear Drop on Body)

Creates a locator that slides along the body mesh, always staying directly underneath the main controller.

```yml
rig.rivet:
  geo: msh_body->shape
  node: tear_drop::roots.0
  closest: tear_drop_world::j.0
  orient: on
```

### Keepout Collision (Bone under Skin)

Prevents a corrective joint from poking through a rigid armor piece. The joint follows its normal animation unless it penetrates the armor, where it will be pushed back to the surface.

```yml
rig.rivet:
  geo: msh_armor->shape
  node: elbow_corrective::roots.0
  keepout: on
```

