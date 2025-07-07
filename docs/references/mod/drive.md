# drive

> Connects one or more plugs using animation curves (driven keys).

This modifier allows you to drive plug values via animation curves based on a controller or attribute.

## Notes

- The **Mikan interface** provides a drive command generator under the **"Posing"** tab. It creates the driven key curves and generates the necessary `drive` commands for rig rebuilding.
- You can also **convert any animation curve** (manual or not) using a utility in the **Mikan rig toolbox**. The command will output the required YAML block directly in the Script Editor.
- ⚠️ Only the **recommended syntax** is documented here. You may encounter legacy variations in older rigs — they remain supported but are not advised.

## Options

- **`node`** (*node*): The driver plug or node.
- **`plug`** (*str*, optional): If `node` refers only to the node, use this to specify which plug on it is the driver.
<!-- -->
- **`<id>@<plug>`** (*dict*): Target plug and its animation curve definition.
  - **`<float>`** (*float* | *dict*): Key definition.
    - Direct value (e.g. `0.5: 1`) creates a spline key by default.
    - To customize curves, use a dictionary:
      | Key                   | Type  | Description                                                                              |
      | --------------------- | ----- | ---------------------------------------------------------------------------------------- |
      | `v`                   | float | Value of the key.                                                                        |
      | `tan`, `itan`, `otan` | str   | Tangent types (`spline`, `linear`, `flat`, `step`, `auto`, etc).                         |
      | `ix`, `iy`            | float | Tangent in (x, y), for custom tangents.                                                  |
      | `ox`, `oy`            | float | Tangent out (x, y), for custom tangents.                                                 |
      | `pre`, `post`         | str   | Pre-/Post-infinity behavior (`linear`, `constant`, `cycle`, `cycleOffset`, `oscillate`). |
      | `tan`                 | str   | Default tangent style for the curve (default: `spline`).                                 |
<!-- -->
- **`flip`** (*bool*, default: `false`): Multiplies all curve values by `-1` for mirrored branches.
- **`scale`** (*float*, default: `1.0`): Scales all key values (useful for rig rescaling).
- **`weight`** (*node*, optional): Plug that multiplies all output values from this modifier.

## Examples

Compact syntax:

```yml
drive:
  node: lipsync::ctrls.0@t.x
  chan_face::node@m_stretch_L:
    { -0.3: -1, 0: 0, 0.3: 1, pre: constant, post: constant }
  chan_face::node@m_stretch_R: { -0.3: -1, 0: 0, 0.3: 1 }
```

Expanded syntax:

```yml
drive:
node: lipsync::ctrls.0@t.x
chan_face::node@m_stretch_L:
  -0.3: -1
  0: 0
  0.3: 1
  pre: constant
  post: constant
  chan_face::node@m_stretch_R:
    -0.3: -1
    0: 0
    0.3: 1
```
