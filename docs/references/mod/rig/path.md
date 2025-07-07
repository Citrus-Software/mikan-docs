# rig.path

> Attaches one or more nodes to a curve, similar to a motion path setup.

This modifier allows a node (or several) to follow a curve, optionally supporting features like parameter override, percentage-based sliding, or closest-point constraints. It’s typically used for effects like objects sliding along surfaces or for attaching geometry to motion paths.

## Options

- **`geo`** (*node*): The curve (geometry) the path will follow.
- **`node`** (*node*): Node attached to the path. Used by default to calculate the initial curve parameter.
- **`nodes`** (*list[node]*, optional): List of nodes to create multiple path attachments at once.
<!-- -->
- **`u`** (*float*, optional): Overrides the default curve parameter. By default, calculated by projecting the node on the curve.
- **`percent`** (*bool*, optional): Uses a 0-1 normalized curve length instead of the parametric `u`. Disabled by default.
- **`length`** (*bool*, optional): Enables distance-based parameterization for slide effects. Disables Maya-style up object.
<!-- - `io` (*bool*, optional): Enables interactive switching between parameter modes (parametric, keep distance, etc.) via an animatable attribute.-->
<!-- -->
- **`parent`** (*node*): Parent for the generated path. Defaults to the parent of `node`.
- **`hook`** (*bool*, default: on): Whether the node should be reparented under the path.
- **`snap`** (*bool*, default: off): Resets the node under the path transform (zeroes transform).
- **`attach`** (*node*, optional): Node used as the source to compute the initial curve parameter.
- **`closest`** (*node*, optional): Similar to `attach`, but remains dynamic. Parameter is driven by projection of this node.
<!-- -->
- **`flip`** (*bool*, default: off): Inverts forward and up vectors for mirrored branches.
- **`forward_vector`** (*str* | *vector*, optional): Forward direction for orientation (default: `x`). Accepts string like `x`, `-z`, or vector like `[1, 0, 0]`.
- **`up_vector`** (*str* | *vector*, optional): Up direction. If not set, falls back to defaults based on `up_object_vector` or `up_object`.
- **`up_object_vector`** (*str* | *vector*, optional): Absolute up vector. Used if `up_object` is not specified.
- **`up_object`** (*node*, optional): Node providing world reference for up orientation. Used in Maya-style path constraints if no `up_object_vector`.
<!-- -->
- **`geo_up`** (*node*, optional): Secondary curve used to place a locator for `up_object` reference.

---

## Examples

### Basic path attachment

```yml
[mod]
rig.path:
  geo: cv_necklace->shape
  node: necklace01::roots.0
```

### Motion path with animatable percentage-based parameter

```yml
[mod]
plug:
  node: bone::ctrls.0
  anim_path:
    type: float
    k: on
    min: 0
    max: 1

rig.path:
  geo: crv_path->shape
  percent: on

connect:
  input: bone::ctrls.0@anim_path
  node: bone::mod.path.0@u
```

This setup allows the controller `bone::ctrls.0` to slide along `crv_path`, controlled by the `anim_path` float attribute (ranging from 0 to 1).

