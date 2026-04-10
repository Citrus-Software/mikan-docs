---
title: parent
description: Reparents one or more nodes in the hierarchy.
---

# parent

Reparents one or more nodes in the scene hierarchy.

This modifier provides a quick and straightforward way to restructure your rig's hierarchy without needing complex matrix setups or constraint modifiers. It behaves similarly to Maya's native parent command.

:::info Node Order
Just like in standard Maya scripting, the order of the nodes is critical. You must always specify the **child node first**, followed by the **destination parent**.
:::

## Parameters

Unlike most modifiers that require a dictionary of parameters, `parent` accepts its arguments directly either as a space-separated string or a YAML list.

| Parameter        | Type             | Description                                             |
|:-----------------|:-----------------|:--------------------------------------------------------|
| *(Direct Value)* | *str / list[id]* | The nodes to process, ordered as `child` then `parent`. |

## Examples

### Inline Syntax (One-liner)

For a quick reparenting, you can write the child and the new parent separated by a space on a single line:

```yml
parent: clothes::roots.0 spine::skin.7
```

### Expanded Syntax (List)

If you prefer a more readable vertical format, you can provide the child and parent as a standard YAML list:

```yml
parent:
  - hair::roots.0
  - skull::skin.0
```
