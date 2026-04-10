---
title: locator
description: Duplicates nodes to create locators, joints, or custom transform helpers.
---

# locator

Duplicates one or more nodes to create new helper transforms matching their initial positions.

This modifier is a highly versatile utility. It allows you to create custom pivot points, extra joints for skinning, or mechanical setups without having to add these technical nodes directly into your base template. You can customize the duplicated node to be a pure transform, a locator, a joint, or even copy the visual shape of the source node.

## Parameters

### Targets & Hierarchy

| Parameter | Type         | Default          | Description                                                                                                                                            |
|:----------|:-------------|:-----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------|
| `node`    | *node*       |                  | Single node to duplicate.                                                                                                                              |
| `nodes`   | *list[node]* |                  | Multiple nodes to duplicate. *(Overrides `node` if both are provided).*                                                                                |
| `parent`  | *node*       | Parent of source | The node under which the new locator(s) will be parented.                                                                                              |
| `root`    | *bool*       | `False`          | Adds a "buffer" transform node above the created locator, acting as its immediate parent. *Note: Automatically set to `True` if `copycat` is enabled.* |

### Naming

| Parameter | Type  | Default     | Description                                                                           |
|:----------|:------|:------------|:--------------------------------------------------------------------------------------|
| `name`    | *str* | Source name | Base name of the locator(s). Used for display and IDs.                                |
| `prefix`  | *str* | `loc`       | Prefix for the created nodes. *(Automatically changes to `sk` if `skin` is enabled).* |

:::caution Reserved Name
Do not use `root` as a value for the `name` parameter. It is reserved by the system for internal ID generation and will trigger a warning.
:::

### Type & Shape

| Parameter        | Type    | Default | Description                                                                                                         |
|:-----------------|:--------|:--------|:--------------------------------------------------------------------------------------------------------------------|
| `skin` / `joint` | *bool*  | `False` | Creates a `joint` instead of a standard transform node. Useful for creating extra skinning influences procedurally. |
| `locator`        | *bool*  | `False` | Adds a locator shape under the transform.                                                                           |
| `shape`          | *bool*  | `False` | Copies the visual curve/shape from the source node to the new locator.                                              |
| `scale`          | *float* | `1.0`   | Affects the scale of the locator shape or the `radius` if it's a joint.                                             |

### Behavior

| Parameter | Type   | Default | Description                                                                                                                                                                                                                             |
|:----------|:-------|:--------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `copycat` | *bool* | `False` | Connects the source node's local matrix to the duplicated locator. The locator will constantly mimic the source node's local transforms (translation, rotation, scale, shear). *Forces `root: true` to prevent double transformations.* |

## Output

The modifier registers the generated nodes internally. Depending on your parameters, the following IDs are created:

- `<id>::mod.loc.<name>` *(The main created node)*
- `<id>::mod.loc.root.<name>` *(If `root: true` or `copycat: true`)*
- `<id>::skin.loc.<name>` *(If `skin: true`)*

## Examples

### Basic Helper

Creates a simple transform node with a locator shape at the position of the bone tip, parented to the world.

```yml
locator:
  node: bone::tip
  parent: world::node
  locator: true
  name: tip_helper
```

### Skin Joint Generation

Creates a joint instead of a transform, prefixes it with `sk_`.

```yml
locator:
  node: leg.L::skin.dn.0
  root: on
  skin: true
  name: knee_volume
```

### The Copycat Setup

Creates a duplicate of a controller that strictly follows its local matrix. The system automatically creates a `root_` node above it to act as an offset, ensuring the matrix connection works flawlessly.

```yml
locator:
  parent: rig_local::node
  node: spine::ctrls.0
  copycat: true
  name: spine_local
```