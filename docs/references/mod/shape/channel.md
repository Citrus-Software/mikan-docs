# shape.channel

> Aggregates and routes multiple plugs through an intermediate buffer node to a controller.

The `channel` modifier is primarily designed to centralize and manage facial rig connections, similar in spirit to tools like the BCS plugin. It aggregates attributes ("plugs") from multiple source nodes and channels them into a single controller node, optionally through an intermediate buffer node.

---

## Options

- **`node`** (*node*, optional): The node on which intermediate buffer channels will be created.
- **`sources`** (*node* | *list[node]*): Nodes that contain the plugs to aggregate.
- **`target`** (*node*): The controller node that will expose the unified channel plugs.
- **`shapes`** (*list[dict]*): List of plug definitions to be connected and exposed on the target controller.
  - **`<plug_name>`** (*dict*): Configuration for the control plug.\
  The modifier will scan all listed sources for each requested plug. If a matching plug is found, a connection will be made from the controller (target) to that plug.
  If no matching plug is found, or if no applicable rule (like driven, combo, etc.) is provided, the plug will not be created on the controller.
    - **`div`** (*str* | *list[str]*, optional): Adds suffixes to plug names to search variants (e.g., `[L, R]` makes `m_smile_L`, `m_smile_R`).
    <!-- -->
    - **`type`** (*str*, default: `float`): Explicitly sets the plug type. Available: `float`, `int`, `bool`, `enum`, `separator`.
    - **`default`**, **`dv`** (*float*, default: `0`): Default value for the plug on the controller.
    - **`min`**, **`max`** (*float*, optional): Value bounds for the plug (applies to controller and buffer).
    - **`enum`** (*list[str]*, optional): Overrides the enum list if `type` is set to `enum`.
    <!-- -->
    - **`driven`** (*dict*, optional): Allows blending of multiple source plugs into a single plug using animation curves.
    - **`<plug_name>`** (*float* | *dict*): Defines the shape of the animation curve between parent and child plug.
        - `float`: Auto mode (e.g., `m_smile: 1` clamps negative values).
        - `dict`: Full curve definition (`{0: 0, 1: 1, pre: linear, post: flat}`). See [drive](../drive) for more details.
    - **`sep`** (*bool*): If true, defines a visual plug separator on the controller. Overrides other plug options.
<!-- -->
- **`combo`** (*dict*): Defines logic-driven plugs that trigger when combinations of parent plugs meet specified values.
  - **`<plug_name>`** (*dict*): New plug to connect based on logic.
    - **`div`** (*str* | *list[str]*, optional): Optional suffixes.
    - **`drivers`** (*dict*): Rules for driving logic:
      - **`<plug_name>`** (*float* | *dict*): Each must match the value for the effect to trigger.
        - `float`: Target value for activation.
        - `dict`: Custom curve definition. See [drive](../drive) for more details.

## Example

```yml
[mod]
shape.channel:
  sources:
    - shp_face::node
    - shp_lips::node
  target: face_dn::ctrls.0
  shapes:
    mouth:
      type: separator

    m_emote:
      div: [L, R]
      driven:
        m_smile: 1
        m_frown: -1
      min: -2
      max: 2

    m_stretch:
      div: [L, R]
      driven:
        m_wide: {0: 0, 1: 1, pre: linear, post: linear}
        m_narrow: {0: 0, 1: -1, pre: linear, post: flat}
      min: -2
      max: 2

  combo:
    m_wide_smile:
      drivers:
        m_wide: 1
        m_smile: 1
      div: [L, R]
```

This setup:
- Aggregates facial shape attributes from `shp_face` and `shp_lips`
- Groups them under `face_dn::ctrls.0`
- Supports mirrored plugs via `div`
- Mixes values with `driven`
- Combines logic conditions with `combo`

