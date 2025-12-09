# plug

> Edits or adds plugs (attributes) to one or more nodes.

The `plug` modifier allows the creation, editing, or deletion of attributes on rig nodes. It's especially useful for exposing controls to animators, adjusting values, or mirroring behavior.

## Options

- **`node`**, **`nodes`** (*node* | *list[node]*): The target node(s) on which the plugs will be edited or created.
- **`<plug_name>`** (*dict* | *value*): Each plug is defined by its name and configuration. A shorthand form (`<plug_name>: <value>`) is accepted to directly set a simple value.
  - **`type`** (*str*, default: `float`): The data type of the plug. Required if the plug doesn't exist yet.
    - Valid values: `float`, `int`, `bool`, `enum`
  - **`set`** (*float* | *int* | *bool*): Assigns the plug a value.
  - **`keyable`**, **`k`**, **`show`** (*bool*, default: `off`): Makes the plug visible and keyable for animation.
  - **`hide`** (*bool*, default: `off`): Hides the plug from animation.
  - **`min`** (*float* | *int*): Sets a minimum value.
  - **`max`** (*float* | *int*): Sets a maximum value.
  - **`nice_name`** (*str*): UI-friendly display name.
  - **`enum`** (*list[str]*): List of values for enum-type plugs.
  - **`lock`** (*bool*, default: `off`): Locks the plug to prevent editing.
  - **`flip`** (*bool*, default: `off`): Multiplies `set`, `min`, and `max` values by `-1` on mirrored branches.
  - **`proxy`** (*node*): Creates a proxy attribute, shared value between two different nodes.

## Examples

### Add a boolean plug and make it keyable

```yml
[mod]
plug:
  node: leaf_base::ctrls.0
  toto:
    type: bool
    set: on
    keyable: on
```

### Assign a simple static value

```yml
[mod]
plug:
  node: msh_wonderbra->xfo
  vis: off
```

### Add multiple animated plugs with min/max constraints

```yml
[mod]
plug:
  node: shp_face::node
  m_open: {type: float, k: on}
  m_close: {type: float, k: on}
  t.x: {set: 2, min: 0, max: 5, k: 0}
```

### Create an enum plug

```yml
[mod]
plug:
  node: camera::ctrls.camera
  mask:
    type: enum
    enum: ['16/9', '4/3']
    keyable: on
```

### Create a proxy plug

```yml
[mod]
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

