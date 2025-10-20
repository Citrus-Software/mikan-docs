# group

> Creates a controller group within the template hierarchy, primarily used to define selection sets for the **SHOW/HIDE** feature in the right-click **Anim** menu.

This modifier allows you to logically group control nodes together for visibility toggles or selection sets.

A group defined with a `vis.` prefix will be treated as a visibility group and exposed in the animator UI.


## Options

- **`nodes`** (*node* | *list[node]*): Nodes to include in the group.
- **`tag`** (*str*): Identifier for the group.
- **`name`** (*str*, optional): Display name for the group. If omitted, it defaults to a formatted version of the `tag`.
- **`vis`** (*node* | *list[node]*): Will create a visibitlty switch for the group and connect it to the given nodes.


## Notes

- To create a visibility group (used for SHOW/HIDE), use the prefix `vis.` in the `tag` value.


## Example

```yaml
group:
  nodes:
    - fingers.L:::ctrls
  tag: vis.fingers
  name: fingers
```
