---
title: plug
description: Creates, edits, or deletes attributes (plugs) on rig nodes.
---

# plug

Creates, edits, or deletes attributes (plugs) on rig nodes.

The `plug` modifier is the primary tool for managing the interface of your controllers. It allows you to expose custom attributes for animators, set default values, manage min/max limits, or lock/hide transform channels to clean up the Channel Box.

## Parameters

### Core Parameters

| Parameter        | Type                | Default | Description                                                                                         |
|:-----------------|:--------------------|:--------|:----------------------------------------------------------------------------------------------------|
| `node` / `nodes` | *node / list[node]* |         | The target node(s) on which the plugs will be edited or created.                                    |
| `<plug_name>`    | *dict / value*      |         | The name of the attribute to create or edit. See [Plug Configurations](#plug-configurations) below. |

### Plug Configurations

To define a plug, add its name directly as a key within the `plug` dictionary (e.g., `my_attr:` or `t.x:`). Its value can be a direct value (shorthand for setting its value) or a dictionary of configurations:

| Option                   | Type                        | Default | Description                                                                                                           |
|:-------------------------|:----------------------------|:--------|:----------------------------------------------------------------------------------------------------------------------|
| *(Direct Value)*         | *float / int / bool*        |         | Shorthand syntax. Directly assigns the value to the plug.                                                             |
| `type`                   | *str*                       | `float` | The data type. Required only if the plug doesn't exist yet. Valid values: `float`, `int`, `bool`, `enum`.             |
| `set`                    | *float / int / bool / list* |         | Assigns the plug a value. If setting a vector plug (`t`, `r`, `s`), provide a list of 3 values `[x, y, z]`.           |
| `keyable` / `k` / `show` | *bool*                      | `False` | Makes the plug visible and keyable for animation in the Channel Box.                                                  |
| `hide`                   | *bool*                      | `False` | Hides the plug from the Channel Box and makes it non-keyable.                                                         |
| `lock` / `l`             | *bool*                      | `False` | Locks the plug to prevent any editing.                                                                                |
| `min`                    | *float / int*               |         | Sets a minimum value limit.                                                                                           |
| `max`                    | *float / int*               |         | Sets a maximum value limit.                                                                                           |
| `nice_name`              | *str*                       |         | UI-friendly display name for the Channel Box.                                                                         |
| `enum`                   | *list[str] / dict*          |         | List of values for `enum` plugs (e.g., `['A', 'B']`). Also accepts a dict to map specific integer indices to strings. |
| `flip`                   | *bool*                      | `False` | Automatically multiplies `set`, `min`, and `max` values by `-1` when executed on mirrored branches.                   |
| `proxy`                  | *plug*                      |         | Creates a proxy attribute, sharing the exact value of the target plug without creating a separate connection.         |

## Examples

### Channel Box Cleanup

A standard operation to lock and hide scales and visibility on a controller. Notice the shorthand syntax for `vis`.

```yml
plug:
  node: my_ctrl::node
  s.x: { lock: true, hide: true }
  s.y: { lock: true, hide: true }
  s.z: { lock: true, hide: true }
  vis: off
```

### Set value

The simplest way to edit an attribute of an object in the rig.

```yml
plug:
  node: msh_wonderbra->xfo
  vis: off
```

### Vector Expansion

You can target vectors directly (e.g., `t`, `r`, `s`). The modifier will automatically expand them to their `.x`, `.y`, and `.z` components.

```yml
plug:
  node: offset::roots.0
  t: [ 0, 5.5, 0 ]
```

### Custom Animated Attributes

Creates custom floating-point attributes with limits, making them keyable for the animator.

```yml
plug:
  node: shp_face::node
  m_open: { type: float, k: on }
  m_close: { type: float, k: on }
  t.x: { set: 2, min: 0, max: 5, k: 0 }
```

### Enum Menus

Creates a dropdown enum menu for controllers.

```yml
plug:
  node: camera::ctrls.camera
  mask:
    type: enum
    enum: [ '16/9', '4/3', '2.35' ]
    keyable: on
```

### Proxy Attributes

A proxy attribute allows a controller to display and drive an attribute that physically lives on another node, acting as a direct shortcut.

```yml
plug:
  node: source::ctrls.0
  weight:
    type: float
    k: on

plug:
  node: target::ctrls.0
  weight:
    type: float
    proxy: source::ctrls.0@weight
```

