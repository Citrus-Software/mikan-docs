# rig.bank

> Adds a "banking" (rocking/pivot shift) effect to a controller, by displacing its rotation pivot along a curve.

This modifier is used under a `core.bones` template to create a bank effect, where the control pivots shift based on their position along a given curve. It is especially useful for organic or mechanical motions like rocking feet or banking objects.

## Options

- **`target`** (*node*, optional): The controller that receives the bank effect.\
  ⚠️ If not specified, the modifier enters *legacy mode*, requiring the modifier to be applied on a `core.joints` or `bones` template. In that mode, `target` and `parent` are inferred automatically.
<!-- -->
- **`node`** (*node*, optional): The node that is driven by the banking rig. Usually the deform joint or transform.
- **`parent`** (*node*, optional): The parent of the generated bank rig. Defaults to the parent of the `node`.
<!-- -->
- **`curve`** (*node*): The curve used to compute the bank pivot positions. This is typically a reference profile or outline curve.
- **`axis`** (*str*, default: "y"): The axis around which the banking effect occurs (e.g. `x`, `y`, or `z`).
- **`name`** (*str*, optional): Base name for generated nodes in the banking rig. Defaults to the curve name.

## Example

```yml
[mod]
rig.bank:
  curve: cv_pot->shape

  target: flower_pot::ctrls
  node: flower_pot::skin

  name: flower_pot
```

This setup creates a banking effect for the `flower_pot::ctrls` controller, with the effect calculated along the `cv_pot` shape curve. The deformation is applied to the `flower_pot::skin` node.

