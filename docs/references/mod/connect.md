---
title: connect
description: Establishes a connection between two plugs, optionally applying basic math operations.
---

# connect

Establishes a connection between two plugs, optionally applying basic math operations.

:::note[Bridging Existing Connections]
If the destination plug is already connected, the modifier **will not overwrite it**. Instead, the existing connection is preserved and automatically injected as the **first input** for the math operation.

This means that if a plug is already driven, you only need to provide *one* additional `input` to perform a 2-input operation (like `add` or `mult`). The modifier will insert the math node between the existing driver and the destination.
:::

:::warning[Legacy Modifier]
This modifier is considered legacy for math operations. For more advanced and flexible usage, prefer using the [**`expression`**](./expression.md) modifier.
:::

## Parameters

### Targets (Destination)

You must provide a destination. You can either pass explicit plugs directly via `node`/`nodes`, or pass nodes and specify the attributes via `plug`/`plugs`.

| Parameter | Type                  | Default | Description                                                    |
|-----------|-----------------------|---------|----------------------------------------------------------------|
| `node`    | *plug \| node*        |         | Single destination.                                            |
| `nodes`   | *list\[plug \| node]* |         | Multiple destinations.                                         |
| `plug`    | *str*                 |         | Single attribute name to connect to (if targets are nodes).    |
| `plugs`   | *list[str]*           |         | Multiple attribute names to connect to (if targets are nodes). |

### Sources (Inputs)

| Parameter | Type   | Default | Description                                                  |
|-----------|--------|---------|--------------------------------------------------------------|
| `input`   | *any*  |         | Single source (plug, float, or int).                         |
| `inputs`  | *list* |         | Multiple sources (used with operators requiring two inputs). |

:::tip[Time variables]
You can use the strings `'time'` or `'frame'` in your inputs. The modifier will automatically convert them to connect to the frame number.
:::

### Math & Modifiers

| Parameter          | Type                  | Default | Description                                                                          |
|--------------------|-----------------------|---------|--------------------------------------------------------------------------------------|
| `op` / `operation` | *str*                 |         | Math operation to apply (see [supported operations](#supported-operations-op) below. |
| `clamp`            | *tuple[float, float]* |         | Clamps the final result between `[min, max]`.                                        |
| `flip`             | *bool*                | `False` | Applies a `-1` multiplier on mirrored branches only.                                 |

#### Supported Operations (`op`)

The behavior and validation depend on the total number of inputs (Total Inputs = Existing connection + Provided inputs).

**Requires exactly 1 total input:**

* *(None)*: Direct connection.
* **`reverse`**: `1 - a`
* **`remap`**: Remaps a value from an old range to a new range (requires [additional parameters](#remap-parameters), see below).

**Requires exactly 2 total inputs:**

* **`add`**: `a + b`
* **`sub`**: `a - b`
* **`mult`**: `a * b`
* **`div`**: `a / b`

#### Remap Parameters

If `op` is set to `remap`, all four of the following parameters are strictly required:

| Parameter | Type    | Description                            |
|-----------|---------|----------------------------------------|
| `min_old` | *float* | The minimum value of the input range.  |
| `max_old` | *float* | The maximum value of the input range.  |
| `min`     | *float* | The minimum value of the output range. |
| `max`     | *float* | The maximum value of the output range. |

## Examples

### Direct Connection

A simple direct connection from a source translation to a destination translation.

```yml
connect:
  input: <src>@t.x
  node: <dst>@t.x
```

### Math Operation

A connection using a math operation. Here, the source is multiplied by `2` before being connected to the destination. Note the use of `inputs` (list) instead of `input`.

```yml
connect:
  node: <dst>@t.x
  op: mult
  inputs:
    - <src>@t.x
    - 2
```

### Bridging an Existing Connection

If `<dst>@t.x` is **already connected** to another node, we only provide a single `input` because the existing driver counts as the first input. This will multiply the existing animation/connection by `0.5`.

```yml
connect:
  node: <dst>@t.x
  op: mult
  input: 0.5
```

### Using Time and Remap

Using the built-in time variable and remapping its value.

```yml
connect:
  node: <dst>@r.y
  op: remap
  input: time
  min_old: 1.0
  max_old: 100.0
  min: 0.0
  max: 360.0
```
