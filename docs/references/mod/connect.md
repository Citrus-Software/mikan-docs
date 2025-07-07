# connect

> Establishes a connection between two plugs, optionally applying basic math operations (_multiply_, _divide_, _add_, _subtract_, _reverse_).

If the destination plug is already connected, the new input will be inserted with priority in the input list.

⚠️ This modifier is considered legacy for math operations. For more advanced and flexible usage, prefer using the expression modifier.

## Options

- **`input`** (*plug*): Single source plug.
- **`inputs`** (*list[plug]*, optional): Multiple source plugs (used with operators requiring two inputs).
<!-- -->
- **`node`** (*plug*): Destination plug.
- **`nodes`** (*list[plug]*): List of destination plugs.
<!-- -->
- **`clamp`** (*tuple[float, float]*, optional): Clamps the result between [min, max].
- **`flip`** (*bool*): Applies a \* -1 multiplier on mirrored branches only.
- **`op`** (*str*): Math operation to apply:
  - `reverse` (1 input): 1 - a
  - `add` (2 inputs): a + b
  - `sub` (2 inputs): a - b
  - `mult` (2 inputs): a \* b
  - `div` (2 inputs): a / b
  - `pow` (2 inputs): a ^ b

## Examples

Simple direct connection:

```yml
connect:
  input: <src>@t.x
  node: <dst>@t.x
```

Connexion with math operation:

```yml
connect:
  node: <dst>@t.x
  op: mult
  inputs:
    - <src>@t.x
    - 2
```
