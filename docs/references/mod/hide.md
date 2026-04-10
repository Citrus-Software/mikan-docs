---
title: hide
description: Hides a node from the animation interface.
---

# hide

Hides a node from the animator's interface.

:::tip Organizing Controller Visibility
If your goal is to manage the display of secondary controller groups or to create visibility toggles for the animator, use the [**`group`**](./group) modifier instead. It is specifically designed to organize the rig's display layers.
:::

## Parameters

Unlike most modifiers that require a dictionary of parameters, `hide` accepts its targets directly.

| Parameter        | Type            | Description                                              |
|:-----------------|:----------------|:---------------------------------------------------------|
| *(Direct Value)* | *id / list[id]* | The ID (or list of IDs) of the node(s) you want to hide. |

## Examples

### Single Node (Inline)

If you only need to hide a single node, you can write it as a concise one-liner:

```yml
hide: rig::roots.0
```

### Multiple Nodes (List)

If you need to hide several nodes at once, provide them as a standard YAML list:

```yml
hide:
  - rig::roots.0
  - rig::roots.1
  - rig::roots.2
```