# reset

> Ensures specified nodes are reset to their bind transform when the rig is rebuilt.

When this modifier is executed, it stores the current transform of the node(s). This saved state will then be used during rig rebuild to restore their position.

This is useful for objects like constraint geometry groups that aren't included in the automatic bind pose group, it ensures they return to the expected position even after rig deletion, which can leave them "stranded" in world space after a teardown. Using this modifier ensures those nodes are correctly reset alongside the rest of the rig.

## Options

- **`node`**, **`nodes`** (*node* | *list[node]*): Nodes to reset.

## Example

```yml
[mod]
reset:
  nodes: geo_group->xfo
```

This will ensure that `geo_group` is reset when the rig is rebuilt if it has been moved.
