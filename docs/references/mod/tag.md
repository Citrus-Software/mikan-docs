---
title: tag
description: Adds or removes internal ID tags from existing nodes.
---

# tag

Adds or removes internal ID tags (`gem_id`) from one or more existing nodes.

While a node must already have an ID to be targeted by this modifier, editing its tags allows you to change how Mikan's tools and other modifiers interact with it.

The two primary use cases for this modifier are:

1. **Customizing Modifiers:** Injecting specific tags (like `space.custom_name`) so that subsequent modifiers (like `space`) can generate clean, readable UI attributes.
2. **Domain Exclusion:** Removing reserved domain tags (like `ctrls` or `skin`) from a node to exclude it from broad-spectrum modifiers, selection sets, or animator tools.

## Parameters

| Parameter        | Type                | Default | Description                                                                                                                                                                                  |
|:-----------------|:--------------------|:--------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `node` / `nodes` | *node / list[node]* |         | The target node(s) to modify.                                                                                                                                                                |
| `add`            | *str*               |         | The pattern to add to the node's IDs. Mikan automatically prefixes this with the current template ID. If multiple nodes are targeted, an index is automatically appended (e.g., `.0`, `.1`). |
| `remove`         | *str*               |         | The pattern used to match and remove existing tags from the node.                                                                                                                            |

:::info Smart Controller Hiding
If you use the `remove` command to strip a `::ctrls.` tag from a node, Mikan will automatically find and hide any NURBS curve shapes attached to that node. This safely converts the controller back into a pure, invisible construction group for the animator, without needing an extra `hide` or `plug` modifier.
:::

## Examples

### Customizing Space Switch Names

Adding a specific `space` tag to an IK controller so that when it is used as a target in a `space` modifier, the generated attribute is cleanly named (e.g., `pin_hand_L`).

```yml
tag:
  node: arm.L::ctrl.ik
  add: space.hand_L
```

### Excluding a Controller

Removes the bend controller from the ctrls domain. It will no longer be seen by tools acting on *::ctrls, and its NURBS curve shape will automatically be hidden in the viewport.

```yml
tag:
  node: leg.L::ctrls
  remove: ctrls.bend
```

### Excluding a Skin Joint

Removes a specific helper joint from the `skin` domain so it doesn't get picked up by automatic skin-export scripts or broad skinning selections.

```yml
tag:
  node: spine::skin.7
  remove: skin
```

### Renaming an ID (Replace)

You can combine both remove and add in the same modifier block to effectively rename an ID.

```yml
tag:
  node: weapon::ctrls.0
  remove: ctrls.0
  add: ctrls.sword_base
```
