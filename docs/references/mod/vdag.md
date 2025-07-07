# vdag

> Modifies virtual parent relationships between nodes in the rig.

This modifier allows you to redefine virtual parent-child links independently of the actual scene hierarchy. Useful for advanced rig setups where logical relationships differ from transform parenting.


## Options

- **`node`**, **`nodes`** (*node* | *list[node]*): Nodes whose virtual parenting will be modified.
<!-- -->
- **`parent`** (*node*): The virtual parent to assign.
- **`closest`** (*list[node]*): A list of fallback virtual parents or related nodes used for proximity-based logic.

## Example

```yml
[mod]
vdag:
  nodes:
    - eyebrow.L::hooks.outer
    - eyebrow.L::hooks.mid
  parent: head::hooks.eyebrow
```

This will reassign the virtual parent of the eyebrow hook nodes to the eyebrow driver on the head.
