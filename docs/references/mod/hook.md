# hook

> Constrains one or more nodes to one or more target nodes using matrix connections.

This modifier is used to connect transforms (SRT) of nodes directly to targets using matrix constraints.

## Options

- **`node`** (*node*, optional): Single node to be constrained or re-parented.
- **`nodes`** (*list[node]*, optional): List of nodes to be constrained or re-parented. If both `node` and `nodes` are provided, `nodes` takes precedence.
<!-- -->
- **`target`** (*node*): Main driver node.
- **`targets`** (*list[node]*): Additional driver nodes. Combined with `target` if both are set.
<!-- -->
- **`parent`** (*node*, optional): Node under which the hook will be parented after creation. Defaults to the parent of the constrained node(s).
- **`group`** (*bool*, default: `false`): If `true`, a hook group node is created and constrained to the targets, with the specified nodes parented under it.
- **`weights`** (*list[float]*, default: `1.0`): Constraint weights for each target. Defaults to `1.0` per target.
<!-- -->
- **`name`** (*str*, optional): Custom name for the hook node (without the `hook_` prefix). This name is also used in the generated node's ID tag. If not specified, the name is automatically generated from the target names.

## Output

If `group: true`, a hook node named `<id>::mod.hook.<index|name>` will be created and constrained to the targets.

If `group: false`, no hook node is created. Nodes are directly constrained to the targets using matrix connections.

## Example

Constraining multiple nodes directly to targets (no hook node created):

```yml
hook:
  nodes:
    - node_id_1
    - node_id_2
  targets:
    - driver_1
    - driver_2
  weights: [0.5, 0.5]
```

Creating a hook node as a matrix-constrained group:

```yml
hook:
  nodes:
    - node_id_1
  targets:
    - source_node
  group: true
  name: my_hook_group
```
