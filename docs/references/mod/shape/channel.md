---
title: shape.channel
description: Aggregates, routes, and blends multiple shape plugs into a centralized controller.
---

# shape.channel

Aggregates and routes multiple plugs through an intermediate buffer node to a main controller.

The `shape.channel` modifier is the ultimate "switchboard" for facial rigging. It mimics the behavior of advanced facial systems (like the BCS plugin), allowing you to take hundreds of raw blendshapes or rig attributes scattered across multiple source nodes, and unify them into a clean, logic-driven interface on a single animation controller.

Beyond simple 1-to-1 routing, it natively handles **corrective combinations** (auto-subtracting drivers), **weighted masks**, and **driven blends** (one slider driving multiple shapes).

## Parameters

| Parameter            | Type         | Default     | Description                                                                                                     |
|:---------------------|:-------------|:------------|:----------------------------------------------------------------------------------------------------------------|
| `sources`            | *list[node]* |             | Nodes containing the raw shape attributes to aggregate. The modifier will scan these nodes for available plugs. |
| `target`             | *node*       |             | The animation controller where the finalized, unified interface will be exposed to the animator.                |
| `node`               | *node*       | `self.node` | The intermediate buffer node where the math channels are created.                                               |
| `shapes`             | *dict*       |             | The main dictionary defining the plugs to create and route (see [Shapes](#shapes-definition-shapes) below).     |
| `combo` / `combos`   | *dict*       |             | Defines logic-driven corrective shapes (see [Combos](#corrective-combos-combo) below).                          |
| `weight` / `weights` | *dict*       |             | Defines masking logic, where one shape multiplies another (see [Masking](#shape-masking-weights) below).        |

### Shapes Definition (`shapes`)

The `shapes` parameter is a dictionary where each key is the name of the attribute you want to expose on your `target` controller.

If the requested shape is found on any of the `sources`, the connection is established. If the shape is not found, it will only be created if it acts as a logic driver (via `driven`).

#### Plug Configuration Options

| Option           | Type         | Default | Description                                                                                                                               |
|:-----------------|:-------------|:--------|:------------------------------------------------------------------------------------------------------------------------------------------|
| `type`           | *str*        | `float` | Explicit plug type: `float`, `int`, `bool`, `enum`, `separator`. *(Note: Automatically inferred if the plug already exists on a source).* |
| `div`            | *str / list* |         | Suffix strings for mirror/regional variants. E.g., `div: [L, R]` will automatically generate and route `m_smile_L` and `m_smile_R`.       |
| `driven`         | *dict*       |         | Defines rules for this controller attribute to drive multiple raw source shapes. See [**Driven Logic**](#driven-logic-driven) below.      |
| `default` / `dv` | *float*      | `0.0`   | Default value on the controller.                                                                                                          |
| `min` / `max`    | *float*      |         | Hard limits for the controller plug.                                                                                                      |
| `enum`           | *list[str]*  |         | Custom list of names for enum types. *(Automatically extracted from the source node if omitted).*                                         |
| `sep`            | *bool*       | `False` | Forces the plug to be a visual UI separator. *Shortcut: Naming your plug with `__` (e.g., `__mouth__`) does this automatically.*          |
| `force`          | *bool*       | `False` | Forces the creation of the plug on the buffer channel, even if no direct source is found.                                                 |

#### Driven Logic (`driven`)

Allows a single attribute on the controller to drive multiple raw shapes on the sources, blending them using animation curves.

| Option           | Type           | Description                                                                                                                                                                                                                                    |
|:-----------------|:---------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `driven`         | *dict*         | A dictionary mapping raw source shapes to their trigger values.                                                                                                                                                                                |
| `<source_shape>` | *float / dict* | The threshold or curve mapping. If a `float` is provided (e.g., `m_smile: 1`), it assumes an auto-clamped 0-to-1 linear curve. If a `dict` is provided, it acts like the [`drive`](../drive.md) modifier (e.g., `{0: 0, 1: -1, pre: linear}`). |

### Corrective Combos (`combo`)

Combos define shapes that activate only when a combination of parent drivers reaches specified values (e.g., Wide + Smile = Wide_Smile).

:::info[Anti-Double Transform (Auto-Subtraction)]
When a combo activates, this modifier **automatically subtracts** its value from the original drivers. For example, if `m_wide` and `m_smile` trigger `m_wide_smile` at 100%, the underlying raw `m_wide` and `m_smile` shapes are automatically driven down to 0% to prevent the geometry from exploding.
:::

| Option          | Type         | Description                                                                                             |
|:----------------|:-------------|:--------------------------------------------------------------------------------------------------------|
| `<combo_shape>` | *dict*       | The target corrective shape to trigger.                                                                 |
| `div`           | *str / list* | Optional suffix (applies to both the combo shape and its drivers).                                      |
| `drivers`       | *dict*       | The rules defining which shapes trigger this combo. Format: `<driver_shape>: <trigger_value_or_curve>`. |

### Shape Masking (`weights`)

The `weights` parameter allows you to define multipliers (masks) between shapes. This is primarily used to dynamically cancel out a specific shape when another one is activated, preventing unnatural geometry intersections.

| Option           | Type         | Description                                                                                                                                                     |
|:-----------------|:-------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<target_shape>` | *dict*       | The shape that will be multiplied/masked.                                                                                                                       |
| `div`            | *str / list* | Optional suffix (applies to both the target shape and its drivers).                                                                                             |
| `drivers`        | *dict*       | The shapes acting as multipliers. Typically mapped using a curve dictionary (e.g., `{0: 1, 1: 0}`) to drive the target shape's multiplier from 100% down to 0%. |

## Examples

### Basic Routing & Interface

Creates separators, simple routing (if `m_blink` exists on `shp_face`, it is connected), and uses `div` to automatically handle Left and Right sides.

```yml
shape.channel:
  sources:
    - shp_face::node
  target: face::ctrl.main
  shapes:
    __eyes__:
      type: separator
    m_blink:
      div: [ L, R ]
      min: 0
      max: 1
```

### Driven Blend (One-to-Many)

A classic setup: creating a single m_emote slider (-2 to 2) that drives the smile when pushed to positive values, and drives the frown when pushed to negative values.

```yml
shape.channel:
  sources:
    - shp_face::node
  target: face::ctrl.mouth
  shapes:
    m_emote:
      div: [ L, R ]
      min: -2
      max: 2
      driven:
        m_smile: 1      # Triggers at +1
        m_frown: -1     # Triggers at -1
```

### Complex Corrective Combo

This triggers the `m_wide_smile` corrective shape when both `m_wide` and `m_smile` are active. The modifier handles the math to smoothly fade in the corrective while fading out the base shapes.

```yml
shape.channel:
  sources:
    - shp_face::node
    - shp_lips::node
  target: face::ctrl.mouth
  shapes:
    m_wide: { div: [ L, R ], min: 0, max: 1 }
    m_smile: { div: [ L, R ], min: 0, max: 1 }

  combo:
    m_wide_smile:
      div: [ L, R ]
      drivers:
        m_wide: 1
        m_smile: 1
```

### Shape Masking (Weights)

Using the `weights` parameter, you can make one shape multiply the output of another. Here, the `m_jaw_open` slider masks out the `m_lip_seal` shape as the jaw opens.

```yml
shape.channel:
  sources:
    - shp_face::node
  target: face::ctrl.jaw
  shapes:
    m_jaw_open: { min: 0, max: 1 }
    m_lip_seal: { min: 0, max: 1 }

  weights:
    m_lip_seal:
      drivers:
        m_jaw_open: { 0: 1, 1: 0 } # At jaw 1, lip seal multiplier is 0
```
