---
title: delete
description: Deletes one or more nodes from the scene.
---

# delete

Deletes one or more nodes from the scene.

This modifier is straightforward and allows you to remove specific nodes by targeting their IDs.

:::tip[Cleanup Helper]
This is particularly useful at the end of a build process to clean up temporary nodes, helpers, or construction groups that are no longer needed in the final rig.
:::

## Parameters

Unlike most modifiers that require a dictionary of parameters, `delete` accepts its targets directly.

| Parameter        | Type             | Description                                                |
|:-----------------|:-----------------|:-----------------------------------------------------------|
| *(Direct Value)* | *id \| list[id]* | The ID (or list of IDs) of the node(s) you want to delete. |

## Examples

### Single Node

If you only need to delete a single node, you can write it as a concise one-liner:

```yml
delete: temp_node_id
```

### Multiple Nodes

If you need to delete several nodes at once, provide them as a standard YAML list:

```yml
delete:
  - node_id1
  - node_id2
```
