---
title: reset
description: Ensures specified nodes are reset to their original transforms during a rig rebuild.
---

# reset

Ensures specified nodes are reset to their original bind transforms during the rig rebuild process.

When building complex rigs, you will often pose the character to test it, then tear it down to rebuild it. If certain objects (like constraint geometry groups, external props, or custom locators) are not part of the standard bind pose hierarchy, tearing down the rig while it is posed can leave them "stranded" in world space.

When the `reset` modifier is executed during the build, it actively stores the current transform of the target node(s). During any subsequent rebuilds, Mikan will use this saved state to force the nodes back to their correct initial position, ensuring a clean and consistent build environment every time.

## Parameters

Because this modifier performs a single, specific action, its syntax is highly flexible. It accepts targets via a standard dictionary, or directly as a single value or list.

| Parameter        | Type                | Description                                                                  |
|:-----------------|:--------------------|:-----------------------------------------------------------------------------|
| `node` / `nodes` | *node / list[node]* | The node(s) whose transform state needs to be stored and reset upon rebuild. |

## Examples

### Dictionary Syntax

The standard format using the `nodes` key.

```yml
reset:
  nodes: geo_group->xfo
```

### Direct Syntax (One-liner)

For a quick reset of a single node, you can write it inline.

```yml
reset: geo_group->xfo
```

### Multiple Nodes (List)

If you have several scattered groups to secure, you can pass them as a simple YAML list.

```yml
reset:
  - my_prop::geo_group
  - face_setup::rivets_grp
  - cloth_sim::collider
```
