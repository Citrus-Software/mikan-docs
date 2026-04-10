---
title: group
description: Creates a logical controller group for selection sets and visibility toggles.
---

# group

Creates a logical controller group within the template hierarchy.

This modifier is primarily used to organize control nodes and populate the right-click **Anim** menu. It allows animators to quickly select entire parts of a rig or toggle the visibility of secondary controllers without cluttering the viewport.

## Usage

Depending on how you name your group's `tag`, the modifier will behave differently and expose different features in the Mikan UI.

### Visibility Toggles (SHOW / HIDE)

If you prefix your group's `tag` with `vis.` (e.g., `vis.fingers`), Mikan will treat it as a visibility group. It will automatically generate a **SHOW/HIDE** toggle in the right-click Anim menu.

By default, this SHOW/HIDE menu is injected into **all controllers** of the parent template. However, you can restrict this menu to appear only when right-clicking specific controllers by providing a list to the `vis` parameter.

### Selection & Keying Sets

If you use a standard tag, the group acts as a logical selection set. These groups appear in the right-click menu, allowing the animator to instantly select or set keys on all controllers within the group at once.

:::info Further Reading
For more details on how animators interact with these groups in the viewport, check out the [**Controllers Usage**](/usage/controllers) page.
:::

## Parameters

| Parameter | Type                 | Default            | Description                                                                                                                                                             |
|:----------|:---------------------|:-------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `nodes`   | *node \| list[node]* |                    | The controllers to include in the group.                                                                                                                                |
| `tag`     | *str*                |                    | Identifier for the group. Use the `vis.` prefix to define a visibility group.                                                                                           |
| `name`    | *str*                |                    | Display name for the group in the UI. If omitted, it defaults to a cleanly formatted version of the `tag`.                                                              |
| `vis`     | *node \| list[node]* | All template ctrls | *(For visibility groups only)* Specific controllers that will receive the right-click SHOW/HIDE menu. If omitted, the menu is added to all controllers in the template. |

## Examples

### Creating a Visibility Group

This groups all left finger controllers and creates a visibility toggle. The SHOW/HIDE menu will be available on all controllers of the arm if you parent the modifier in this part of the template.

```yml
group:
  nodes:
    - fingers.L:::ctrls
  tag: vis.fingers
  name: fingers
```
