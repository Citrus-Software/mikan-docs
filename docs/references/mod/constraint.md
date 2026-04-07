---
title: constraint
description: Creates a constraint between a target and a constrained node.
---

# constraint

Creates a constraint between a target and a constrained node.

This modifier supports common constraint types such as `orient`, `aim`, `point`, `parent`, and `scale`. It handles multiple targets and offers fine control over offsets, blending, and skipped channels.

## Usage

A constraint modifier defines a specific constraint setup that can be applied to a single node or broadcasted across multiple nodes.

1. **Target Setup (Single or Batch):** Determine the type of constraint you need. You can apply it to a single DAG node (using `node`), or provide a list of nodes (using `nodes`). If a list is provided, the exact same constraint setup (same targets, weights, and offsets) is reproduced identically on every node in the list.
2. **Channel Validation & Blending:** Before applying the constraint, the framework automatically evaluates the state of the target node's channels based on the constraint type:
    - **Free Channels:** If the channels are clear, the connection is made directly.
    - **Connected Channels:** If connections already exist, the modifier automatically creates a blend between the new constraint and the old connections. It adds a `blend_***` attribute (e.g., `blend_translate`, `blend_orient`) to the constrained node.
    - **Forced Blending:** You can force the creation of a blend attribute even if no prior connections exist by setting `blend: true`. This is very useful for creating manual switches on the constrained node.

:::warning Limitation
The modifier will abort and refuse to apply the command if a blend setup already exists on the targeted channels.
:::

:::info 🚀 Coming Soon
A dedicated tool is on the roadmap! It will automatically convert constraints applied manually in your viewport into YAML modifier notes.
:::

## Parameters

### Core Parameters

- **`type`** (*str*) : Type of constraint. Must be one of: `orient`, `aim`, `point`, `parent`, `scale`.
- **`node`** (*node*, optional) : Node being constrained.
- **`nodes`** (*list[node]*, optional) : List of nodes being constrained (if applying the exact same setup to multiple objects).
- **`target`** (*node*) : Main constraint driver.
- **`targets`** (*list[node]*, optional) : Additional constraint drivers. If both `target` and `targets` are provided, they are combined.

### Behavior & Offsets

- **`weights`** (*list[float]*, optional, default: 1.0) : Weights per target.
- **`maintain_offset`** (*bool*, default: false) : Maintains the current offset between the target and the node.
- **`blend`** (*bool*, default: false) : Forces the addition of `blend_translate` / `blend_orient` attributes to the constrained node (if they don't already exist).
- **`skip`** (*str*, optional) : Channels to skip during connection. Use axes like `x`, `y`, `z`, or `xy`.
- **`skip_translate`** (*str*, optional) : Same as `skip`, but strictly applies to translate channels (useful for `parent` constraints).
- **`skip_rotate`** (*str*, optional) : Same as `skip`, but strictly applies to rotate channels.
- **`skip_scale`** (*str*, optional) : Same as `skip`, but strictly applies to scale channels.

### Aim Specific Parameters (`type: aim`)

- **`aim`** (*str* | *vector*, default: x) : Main aim axis of the constrained node.
- **`up`** (*str* | *vector*, optional, default: y) : Local up axis of the constrained node.
- **`up_vector`** (*str* | *vector*, optional, default: null) : World or object space up vector for the constraint.
    - If `up_object` is set, this behaves as the *object rotation up vector*.
    - If not, it is treated as a world space vector (*vector* mode).
- **`up_object`** (*node*, optional) : Reference object for the up vector.
    - If `up_vector` is **not** set, this node is used as the target up object (*object up* mode).
    - If `up_vector` **is** set, it's used in *object rotation up* mode.
- **`flip`** (*bool*, default: false) : Inverts the aim and up axes if the mod is executed in a mirrored branch.

:::tip Vector formatting
Whenever a parameter expects a vector, you can provide it as:

- **Named axes**: `x`, `y`, `z`, `-x`, `-y`, `-z`
- **3D array format**: `[0, 1, 0]`
  :::

## Output

The generated constraint node follows this naming convention:
`<id>::mod.constraint.<type>.<index>`

Its target weights will be automatically exposed on plugs: `@w0`, `@w1`, `@w2`, etc.

## Examples

### Basic Aim Constraint

Creates an aim constraint maintaining offset, aiming down the Y axis with X as the up vector.

```yml
constraint:
  type: aim
  node: <tpl>::infs.0
  target: <target>::node
  maintain_offset: on
  aim: y
  up: x
```

### Multi-Node Point Constraint

Applies the exact same point constraint to a list of multiple nodes simultaneously.

```yml
constraint:
  type: point
  nodes:
    - <tpl-a>::infs.0
    - <tpl-b>::infs.0
    - <tpl-c>::infs.0
  target: <target>::main_ctrl
```
