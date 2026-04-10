---
title: expression
description: Connects multiple plugs using a custom mathematical expression.
---

# expression

Connects multiple plugs using a custom mathematical expression.

This modifier allows for advanced control by combining plug values through mathematical and logical operations. It offers significantly more flexibility than standard math operators and is the preferred way to perform complex logic, vector operations, or conditional setups in Mikan.

:::info Node Graph vs. Expression Node
Unlike Maya's native `expression` node, this modifier **does not** create a heavy, script-based node. Instead, it parses your formula and generates a **pure DG node graph** (using `multiplyDivide`, `plusMinusAverage`, etc.). This ensures maximum evaluation performance and compatibility with Parallel Evaluation.
:::

## Parameters

| Parameter | Type                           | Default | Description                                                                                                                                 |
|:----------|:-------------------------------|:--------|:--------------------------------------------------------------------------------------------------------------------------------------------|
| `op`      | *str*                          |         | The expression string to evaluate.                                                                                                          |
| `<var>`   | *plug \| float \| int \| bool* |         | Any alphanumeric key (e.g., `a`, `x`, `val1`) acts as a variable in your expression. Assign a plug reference, a float, or an integer to it. |

## Syntax

The expression engine parses a custom syntax designed specifically for rigging needs.

* **Assignments:** Use `=` to assign the result of the expression to your target variable.
* **Math Operators:** `+`, `-`, `*`, `/`, `^` (power), `%` (modulo), and parentheses `()`.
* **Logical Operators:** `|` (OR), `&` (AND), `!` (NOT).
* **Booleans:** `true`, `false`, or aliases `on`, `off`.
* **Constants:** `e`, `pi`.
* **Vectors:** Declared using brackets, e.g., `[x, y, z]`.

### Keywords

Built-in variables you can use anywhere in your expressions without declaring them:

* `frame` / `time`: The current scene time/frame.
* `flip`: Evaluates to `1` by default, and `-1` when the modifier runs in a mirrored branch.

### Components

You can extract specific components from multi-dimensional data using the dot notation:

* **Vector:** `.x`, `.y`, `.z` *(e.g., `my_vec.y`)*
* **Quaternion:** `.x`, `.y`, `.z`, `.w`
* **Matrix:** *(Not yet implemented)*
* **Transform:** *(Not yet implemented)*

### Conditions (Ternary)

The engine supports ternary operators to create branching logic. Unlike some permissive languages, a condition here must be **explicit** and systematically include three elements:

1. A first value.
2. A comparison operator (`==`, `!=`, `<`, `<=`, `>`, `>=`).
3. A second value.

**Syntax:** `value1 operator value2 ? true_value : false_value`

The returned values (`true_value` and `false_value`) can seamlessly be either **scalars** or **vectors**.

```cpp
// Explicit comparison returning scalars
v > 0.5 ? 1.0 : 0.0

// Explicit comparison returning vectors
v == 1 ? [0, 1, 0] : [1, 0, 0]
```

Conditions can also be safely nested using parentheses:

```cpp
val1 == val2 ? (val3 > 0 ? resultA : resultB) : resultC
```

## Available Functions

### Scalar functions

| Function                           | Description                                        |
|------------------------------------|----------------------------------------------------|
| `inverse(x)`                       | 1 / x                                              |
| `exp(x)`                           | e^x                                                |
| `sqrt(x)`                          | Square root                                        |
| `clamp(v, min, max)`               | Limits value within bounds                         |
| `remap(v, min1, max1, min2, max2)` | Remaps value from one range to another             |
| `lerp(v1, v2, w)`                  | Linear interpolation between v1 and v2             |
| `abs(v)`                           | Absolute value                                     |
| `sign(v)`                          | Returns 1, 0 or -1                                 |
| `min(a, b)` / `max(a, b)`          | Min/Max between two values                         |
| `cos(a)`, `sin(a)`, `tan(a)`       | Trigonometric functions                            |
| `acos(v)`, `asin(v)`, `atan(v)`    | Inverse trig functions                             |
| `noise(v)`                         | Noise (simplex in Tang, Perlin in Maya)            |
| `dnoise(v)`                        | Directional noise (-1 to 1)                        |
| `switch(v, a, b, c...)`            | Enum-style value selection                         |
| `value(v)`                         | Gets value at build time (won’t update at runtime) |

### Conversion functions

| Function                      | Description           |
|-------------------------------|-----------------------|
| `bool(v)`                     | Convert to Boolean    |
| `int(v)`                      | Convert to Integer    |
| `vector(x, y, z)`             | Create Vector         |
| `quat(x, y, z, w)`            | Create Quaternion     |
| `quat([x, y, z], ro)`         | Quaternion from Euler |
| `euler(x, y, z, ro)`          | Convert to Euler      |
| `matrix(x, y, z, t)`          | Create Matrix 4*4     |
| `matrix(v00, v01, v02, ... )` | Create Matrix 4*4     |
| `transform(t, r, s)`          | Create Transform      |

### Vector functions

| Function           | Description                          |
|--------------------|--------------------------------------|
| `len(v)`           | Vector length                        |
| `distance(v1, v2)` | Distance between two points          |
| `angle(v1, v2)`    | Angle in degrees between two vectors |
| `norm(v)`          | Normalized vector                    |
| `dot(v1, v2)`      | Dot product                          |
| `cross(v1, v2)`    | Cross product                        |
| `lerp(v1, v2, w)`  | Linear interpolation between vectors |

### Quaternion functions

| Function         | Description         |
|------------------|---------------------|
| `slerp(a, b, t)` | Slerp interpolation |

## Examples

### Vector Interpolation (lerp)

A simple setup to blend two positions.

```yml
expression:
  op: out = lerp(a, b, factor)
  out: target::node@t
  a: driver1::node@t
  b: driver2::node@t
  factor: master::ctrl@blend
```

### Stretch & Squash

Interpolating dynamically using mathematical functions (`sqrt` and `lerp`).

```yml
expression:
  op: y = lerp(x, 1/sqrt(x), f)
  y: driven::roots.0@s.x
  x: driver::skin.0@s.y
  f: driver::ctrls.0@squash
```

### Procedural Noise

Using the built-in `time` variable combined with `noise` to create an automated idle motion.

```yml
expression:
  op: out = noise(time * speed) * amp
  out: tail::poses.0@r.z
  speed: 2.0
  amp: 15.0
```

### Quaternions

Extracting the `z` component of a generated quaternion to drive a corrective joint.

```yml
expression:
  op: psd = quat([rx, ry, rz], xyz).z
  psd: arm.L::j.clavicle@qz
  rx: arm.L::j.clavicle@r.x
  ry: arm.L::j.clavicle@r.y
  rz: arm.L::j.clavicle@r.z
```
