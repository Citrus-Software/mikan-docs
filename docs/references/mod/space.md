---
title: space
description: Creates a constraint-based space switch system on a controller's parent.
---

# space

Creates a constraint switch system on a controller's parent (or a specified root node).

This modifier sets up a space-switching system allowing a controller to dynamically change its space of influence based on a list of targets. This is critical for animators who need to change a controller's follow behavior (e.g., switching an IK hand from world space to head space or pelvis space).

Beyond creating the constraints, this modifier also automatically builds the necessary UI network nodes, ensuring that Mikan's right-click Anim menu can perform **seamless space-matching** out of the box.

## Parameters

### Targets & Nodes

| Parameter            | Type                | Default          | Description                                                                                                                      |
|:---------------------|:--------------------|:-----------------|:---------------------------------------------------------------------------------------------------------------------------------|
| `node`               | *node*              |                  | The controller where the switch attributes will be exposed to the animator.                                                      |
| `root`               | *node*              | Parent of `node` | The actual node that receives the constraint. Usually the buffer group right above the controller.                               |
| `target` / `targets` | *node / list[node]* |                  | List of nodes acting as the drivers for the space switch.                                                                        |
| `default`            | *list[float]*       | `[0.0, ...]`     | Default weights for each target upon build. If omitted, all targets start at 0 (meaning the controller stays in its rest space). |

### Constraint Type

| Parameter | Type   | Default | Description                                                                                                               |
|:----------|:-------|:--------|:--------------------------------------------------------------------------------------------------------------------------|
| `orient`  | *bool* | `False` | Forces an `orientConstraint` (rotation only). Generated attributes will be prefixed with `follow_`.                       |
| `point`   | *bool* | `False` | Forces a position-only constraint. Generated attributes will be prefixed with `pin_`. *(Ignored if `orient` is enabled).* |

*(If neither `orient` nor `point` is specified, a full `parentConstraint` (Translate + Rotate) is created, and attributes are prefixed with `pin_`).*

### UI & Naming

| Parameter   | Type              | Default  | Description                                                                                             |
|:------------|:------------------|:---------|:--------------------------------------------------------------------------------------------------------|
| `rest_name` | *str*             | `parent` | The UI label for the fallback state (when all targets are at 0).                                        |
| `names`     | *dict[node: str]* |          | A dictionary allowing you to explicitly map target nodes to custom attribute names for the Channel Box. |

### Attribute Name Resolution

If you do not explicitly provide a custom name via the `names` dictionary, Mikan tries to generate a clean, UI-friendly attribute name based on the target node's internal ID.

It searches the target's tags in this priority order: `space` > `hooks` > `ctrls` > `skin`.
For example, if your target has the ID `leg::space.world`, Mikan will generate the attribute `pin_world`.

If a name cannot be meaningfully resolved automatically (e.g., resulting in ugly names like `pin_skin_0`), you should:

- Tag the target properly (e.g., give it a `space.custom_name` tag using the [**`tag`**](./tag) modifier).
- Or explicitly define its name using the `names` parameter.

## Examples

### Classic World/Pelvis/Head Switch

Creates a standard translation and rotation space switch for an IK arm. By default, it stays in its hierarchical parent space (labeled "root").

```yml
space:
  node: arm.L::ctrl.ik
  rest_name: root
  targets:
    - *::space.world
    - *::space.move
    - *::space.pelvis
    - *::space.head
```

### Orient-Only Switch (Follow)

Creates a rotation-only space switch for a head controller, allowing it to follow the neck or stay locked to the world. We explicitly map the world target to the name "global" to avoid bad auto-naming.

```yml
space:
  node: neck::ctrls.head
  orient: on
  rest_name: local
  targets:
    - *::space.world
    - *::space.move
```
