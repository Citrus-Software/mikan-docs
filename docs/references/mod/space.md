# space

> Creates a constraint switch on a controller's parent.

This modifier sets up a space-switching system allowing a controller to dynamically change its parent based on a list of targets. It is useful for animators who need to change follow behavior (e.g., switching from world space to head or pelvis space).

## Options

- **`node`** (*node*, optional): The controller where the switch attributes will be created. Defaults to the modifier's `node`.
- **`root`** (*node*, optional): The node that receives the actual constraint. Defaults to the parent of the `node`.
<!-- -->
- **`orient`** (*bool*, default: `false`): Enables rotation-only constraints. The switch will be named `follow`.
- **`point`** (*bool*, default: `false`): Enables position-only constraints. The switch will be named `pin`. Ignored if `orient` is enabled.
<!-- -->
- **`target`**, **`targets`** (*node* | *list[node]*): List of nodes acting as constraint targets (drivers for the switch).
- **`default`** (*list[float]*, optional): Default weights for each target. Defaults to `[0, 0, ...]`.
<!-- -->
- **`rest_name`** (*str*, default: `parent`): Name of the fallback switch state (when no targets are active).
- **`names`** (*dict[node]: str*, optional): Dictionary mapping target IDs to custom attribute names for the switch.

## Attribute Name Resolution

Switch attribute names are derived from the target IDs, searching in order of priority: `space`, `hooks`, `ctrls`, then `skin`. If a name cannot be meaningfully resolved (e.g., `leg::skin.0` > `pin_0`), consider:

- Adding a [tag](tag) to the node (`space.name`, for example), or
- Specifying a custom name via the `names` option.

If no ID is present, the name is inferred from the node name with type prefixes removed.

## Example

```yml
space:
  rest_name: root
  targets:
    - *::space.world
    - *::space.move
    - *::space.pelvis
    - *::space.head
```

