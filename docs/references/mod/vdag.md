---
title: vdag
description: Modifies virtual parent relationships between nodes, bypassing the Outliner hierarchy.
---

# vdag

Modifies the **Virtual DAG** (Directed Acyclic Graph) relationships between nodes in the rig.

This modifier allows you to redefine parent-child links logically, completely independently of the actual scene hierarchy in the Maya Outliner.

## The Concept of Virtual Hierarchy

When building advanced rigs, you often need to organize nodes for technical reasons that conflict with logical workflows.

**The Problem:** Imagine you group all secondary clothing controllers into a `clothes_grp` parented to the World. This is great for keeping your Outliner clean and preventing animators from accidentally selecting them when grabbing the main body. However, because they are technically disconnected from the body in the Outliner:

1. **Pickwalking is broken:** An animator cannot press "Up" on a shirt controller to navigate to the chest controller.
2. **Skin Export is broken:** If you do this with skin joints, the engine will export a disconnected skeleton where clothing joints are parented to the world root instead of the spine.

**The Solution:**
The `vdag` modifier fixes this by drawing an invisible, logical link between these nodes. Mikan tools (like the pickwalk setup or the skeleton builder) will read this *virtual* hierarchy instead of the physical Outliner hierarchy, giving you the best of both worlds.

## Parameters

| Parameter        | Type                | Default | Description                                                                                                                                                                                                        |
|:-----------------|:--------------------|:--------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `node` / `nodes` | *node / list[node]* |         | The target node(s) whose virtual parenting will be modified.                                                                                                                                                       |
| `filter`         | *bool*              | `on`    | Ensures pattern safety. If `on`, the system will only allow virtual parenting if the nodes share the same domain (e.g., a `ctrls` to a `ctrls`, or a `skin` to a `skin`). Set to `off` to force an unnatural link. |

### Assignment Modes

You must provide exactly one of the following methods to assign the new parent:

| Mode      | Type         | Description                                                                                                                                                         |
|:----------|:-------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `parent`  | *node*       | Explicit assignment. The specific node to set as the virtual parent.                                                                                                |
| `closest` | *list[node]* | Proximity assignment. Provides a list of candidates. The modifier will automatically calculate the 3D distance and set the closest candidate as the virtual parent. |

## Examples

### Explicit Virtual Parenting

Fixing the pickwalk and logical hierarchy for the clothing example mentioned above. We force the virtual parent of the shirt controllers back to the main chest controller.

```yml
vdag:
  nodes:
    - shirt_top::ctrls
    - shirt_mid::ctrls
  parent: spine::ctrls.spine2
```

### Proximity-Based Parenting (Closest)

A highly procedural setup. If you generated 50 secondary skin joints for a dress, you can automatically virtually parent each of them to the closest leg or spine skin joint, without having to write them out one by one.

```yml
vdag:
  nodes: tweakers::skin
  closest:
    - leg::skin
    - spine::skin
```
