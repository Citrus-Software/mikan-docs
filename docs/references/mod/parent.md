---
title: parent
description: Reparents one or more nodes in the hierarchy.
---

# parent

Reparents one or more nodes in the scene hierarchy.

This modifier provides a quick and straightforward way to restructure your rig's hierarchy without needing complex matrix setups or constraint modifiers. It behaves similarly to Maya's native parent command.

## Parameters

Unlike most modifiers that require a dictionary of parameters, `parent` accepts its arguments directly either as a space-separated string or a YAML list.

| Parameter        | Type       | Description                                                    |
|:-----------------|:-----------|:---------------------------------------------------------------|
| *(Direct Value)* | *list[id]* | The nodes to process. The last node is the destination parent. |

:::info[Node Order]

- If two nodes are provided: The first is the **child**, the second is the **parent**.
- If more than two nodes are provided: The **last node** in the list becomes the parent of **all preceding nodes**.
  :::

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
  - hair_A::roots.0
  - hair_B::roots.0
  - hair_C::roots.0
  - skull::skin.0
```
