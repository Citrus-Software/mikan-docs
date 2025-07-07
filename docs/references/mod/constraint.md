# constraint

> Creates a constraint between a target and a constrained node.

This modifier supports common constraint types such as `orient`, `aim`, `point`, `parent`, and `scale`.

It supports multiple targets and offers fine control over offsets, blending, and skipped channels.

### ⚠️ Notes

- For **aim constraints**, `maintain_offset` is not currently supported in Tangerine when using `up_object` or `up_vector`.
- Blending is useful only if the constrained node does **not** already have its SRT attributes connected.

## Options

- **`node`** (*node*, optional): Node being constrained.
- **`target`** (*node*): Main constraint driver.
- **`targets`** (*list[node]*): Additional constraint drivers. If both `target` and `targets` are provided, they are combined.
<!-- -->
- **`type`** (*str*): Type of constraint. One of: `orient`, `aim`, `point`, `parent`, `scale`
<!-- -->
- **`weights`** (*list[float]*, optional): Weights per target. Defaults to `1.0` for each.
- **`maintain_offset`** (*bool*, default: `false`): Maintains the current offset between target and node.
- **`blend`** (*bool*, default: `false`): Adds `blend_translate` / `blend_orient` attributes to the constrained node (if they don't already exist).
<!-- -->
- **`skip`** (*str*, optional): Channels to skip during connection. Use axes like `x`, `y`, `z`, or `xy`.
- **`skip_translate`** (*str*, optional): Same as `skip`, but only applies to translate channels (for `parent` constraints).
- **`skip_rotate`** (*str*, optional): Same as `skip`, but only applies to rotate channels (for `parent` constraints).

Aim constraint options (`type: aim`)

- **`aim`** (*str* | *vector*, default: `x`): Main aim axis of the constrained node.
- **`up`** (*str* | *vector*, optional): Local up axis of the constrained node.
- **`up_vector`** (*str* | *vector*, optional, default: `null`): World or object space up vector for the constraint.
  - If `up_object` is set, this behaves as the *object rotation up vector*.
  - If not, it is treated as a world space vector (*vector* mode).\
    ⚠️ `maintain_offset` is not supported in Tangerine in this case yet.
- **`up_object`** (*node*, optional): Reference object for the up vector.
  - If `up_vector` is **not** set, this node is used as the target up object (*object up* mode).\
    ⚠️ `maintain_offset` is not supported in Tangerine in this case yet.
  - If `up_vector` **is** set, it's used *in object rotation up* mode.
- **`flip`** (*bool*, default: `false`): Inverts the aim and up axes.

Vectors can be written as either:

- Named axes: `x`, `y`, `z`, `-x`, etc.
- 3D vector format: `[0, 1, 0]`

## Output

The generated constraint node will be named: `<id>::mod.constraint.<type>.<index>`. Its target weights will be exposed on plugs: `@w0`, `@w1`, `@w2`, ...

### Example

```yml
constraint:
  type: aim
  node: <tpl>::infs.0
  target: <target>::node
  maintain_offset: on
  aim: y
  up: x
```
