# tag

> Adds or removes an ID tag from one or more nodes.

This modifier is used to assign or unassign a specific functional tag to nodes in the rig. A common use case is removing the `ctrls` or `skin` role from a node.


## Options

- **`node`**, **`nodes`** (*node* | *list[node]*): Nodes to modify.
<!-- -->
- **`remove`** (*str*): Pattern used to match and remove tags.
- **`add`** (*str*): Tag to add to the node(s). If multiple nodes are targeted, the tag will be indexed (e.g. `tag.0`, `tag.1`, ...).


## Examples

### Remove a tag from a single node

```yml
[mod]
tag:
  node: leg::ctrls  # get all the legs controllers
  remove: ctrls.bend  # remove only this pattern
```

### Add a tag to a specific node

```yml
[mod]
tag:
  node: arm.L::ctrls.ik
  add: space.hand_L
```

