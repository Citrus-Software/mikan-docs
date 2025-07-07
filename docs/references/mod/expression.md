# expression

> Connects multiple plugs using a custom mathematical expression.

This modifier allows for advanced control by combining plug values through expressions, offering greater flexibility than standard math operators.

It is the preferred way to perform complex logic or vector operations in Mikan.

## Options

- **`op`** (*str*): The expression string.
- **`<[a-z0-9]>`** (*plug*): Plug references used as variables within the expression.

## Syntax

- Assignments use `=`
- Math operators: `+`, `-`, `*`, `/`, `^` (power), `%` (modulo), parentheses `()`
- Logical operators: `|` (or), `&` (and), `!` (not)
- Booleans: `true`, `false`, or aliases `on`, `off`
- Constants: `e`, `pi`
- Vectors: `[x, y, z]`
  - Access components using `.x`, `.y`, `.z`
- Keywords:
  - `frame`, `time`: Current frame number
  - `flip`: Equals `1` by default, `-1` for mirrored branches

### Conditional expressions

Use the ternary syntax:

```jade
condition ? true_value : false_value
```

You can nest conditions with parentheses:

```jade
cond1 ? (cond2 ? val2 : val3) : val1
```

Works with scalars and vectors alike.

## Available Functions

### Scalar functions

| Function                           | Description                                        |
| ---------------------------------- | -------------------------------------------------- |
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
| `dnoise(v)`                        | Directional noise \[-1, 1]                         |
| `switch(v, a, b, c...)`            | Enum-style value selection                         |
| `int(v)` / `bool(v)`               | Type conversion                                    |
| `value(v)`                         | Gets value at build time (won’t update at runtime) |

### Vector functions

| Function           | Description                          |
| ------------------ | ------------------------------------ |
| `len(v)`           | Vector length                        |
| `distance(v1, v2)` | Distance between two points          |
| `angle(v1, v2)`    | Angle in degrees between two vectors |
| `norm(v)`          | Normalized vector                    |
| `dot(v1, v2)`      | Dot product                          |
| `cross(v1, v2)`    | Cross product                        |
| `lerp(v1, v2, w)`  | Linear interpolation between vectors |

## Examples

Squash & Stretch interpolation:

```yml
expression:
  op: y = lerp(x, 1/sqrt(x), f)
  y: driven::roots.0@s.x
  x: driver::skin.0@s.y
  f: driver::ctrls.0@squash
```

Using quaternions in expressions:

```yml
expression:
  op: psd = quat([rx, ry, rz], xyz).z
  psd: arm.L::j.clavicle@qz
  rx: arm.L::j.clavicle@r.x
  ry: arm.L::j.clavicle@r.y
  rz: arm.L::j.clavicle@r.z
```
