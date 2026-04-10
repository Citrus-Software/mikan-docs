---
title: hook
description: Constrains nodes to target nodes using matrix connections.
---

# hook

Constrains one or more nodes to one or more target nodes using matrix connections.

This modifier is a powerful alternative to standard transform constraints. By using matrix math to drive the transforms, it provides better evaluation performance, avoids Euler flipping, and allows for clean "virtual parenting" setups without cluttering the scene hierarchy.

## Usage

The behavior of this modifier fundamentally changes based on the `group` parameter.

### Direct Connection (`group: false`)

This is the default behavior. No new nodes are created in the scene hierarchy. The modifier directly drives the transform matrix of the specified nodes using the targets' matrices. This is perfect for seamless, invisible space-switching or blended constraints.

### Hook Group (`group: true`)

The modifier creates a brand new physical transform node (the "hook group") in the hierarchy. This new node is matrix-constrained to the targets, and your driven nodes are physically **re-parented** under it. This is ideal when you need a clean pivot point or a dedicated group to attach a prop, a limb, or a sub-rig without altering the driven node's local
coordinates.

## Parameters

### Driven (Destination)

| Parameter | Type         | Default | Description                                                                                   |
|:----------|:-------------|:--------|:----------------------------------------------------------------------------------------------|
| `node`    | *node*       |         | Single node to be constrained or re-parented.                                                 |
| `nodes`   | *list[node]* |         | Multiple nodes to be constrained or re-parented. *(If both are provided, they are combined).* |

### Drivers (Sources)

| Parameter | Type         | Default | Description                                                          |
|:----------|:-------------|:--------|:---------------------------------------------------------------------|
| `target`  | *node*       |         | Main driver node.                                                    |
| `targets` | *list[node]* |         | Additional driver nodes. *(Combined with `target` if both are set).* |

### Behavior & Settings

| Parameter            | Type           | Default              | Description                                                                                                                                                                    |
|:---------------------|:---------------|:---------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `group`              | *bool*         | `False`              | Determines the operation mode. If `true`, a physical hook group is created and the nodes are parented under it. If `false`, applies direct matrix connections.                 |
| `parent`             | *node*         | Parent of `nodes[0]` | *(Only if `group: true`)* The node under which the newly created hook group will be parented.                                                                                  |
| `maintain_offset`    | *bool*         | `True`               | Maintains the relative spatial offset between the targets and the driven nodes at build time.                                                                                  |
| `weight` / `weights` | *float / list* |                      | Constraint weights for each target. Defines the blending between multiple drivers. If explicitly provided, it forces the creation of weight attributes (see [Output](#output). |
| `name`               | *str*          | optional             | Custom base name for the generated hook node and its ID. Highly recommended for cross-referencing (see [Output](#output)).                                                     |

:::tip Single Target Switch
By default, if you only have a single `target`, the connection is direct and absolute. However, explicitly defining a `weight` parameter will force the system to generate a weight attribute (`@w0`). This gives you a convenient on/off toggle for the constraint.
:::

## Output

**Attributes:**
If multiple targets are used, or if the `weight` parameter is explicitly provided, the constraint weights are exposed as animatable attributes on the constrained node (e.g., `@w0`, `@w1`, etc.). This allows animators to blend targets or toggle constraints at runtime.

**Generated IDs:**
If **`group: true`**, the new transform node is prefixed with `hook_` and registered internally.
This is where the `name` parameter becomes crucial. Naming your hook allows you to easily target it later in your build process:

- `<id>::mod.hooks.<name>`

:::info Best Practice
Always provide a `name` when `group: true`. If omitted, Mikan will assign a numerical index or an auto-generated string based on the targets. These fallbacks are unpredictable and make it very difficult to reference your new hook group reliably in subsequent modifiers.
:::

:::info Reparenting Safe
Because this modifier relies on robust matrix math both the generated hook group and the constrained nodes can be freely reparented anywhere in your rig hierarchy after creation. No parasitic offsets or double-transformations will occur.
:::

## Examples

### Direct Matrix Blend (No Group)

Constraining multiple nodes directly to two targets with a 50/50 blend. No hierarchy is changed, and no new groups are created.

```yml
hook:
  nodes:
    - my_prop::roots.0
    - my_other_prop::roots.0
  targets:
    - driver_A::node
    - driver_B::node
  weights: [ 0.5, 0.5 ]
```

### Creating a Hook Group (Virtual Parent)

This setup creates a physical `hook` transform driven by the `source_node`. The `node_id_1` is then reparented under this new hook. This is a classic setup for attaching the root of a template to another part of the rig.

```yml
hook:
  node: knee_pad::roots.0
  target: leg.L::skin.dn.2
  group: on
  weight: 1
  name: pad
```
