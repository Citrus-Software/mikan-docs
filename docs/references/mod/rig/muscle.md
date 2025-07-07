# rig.muscle

> Builds a muscle rig that stretches, squashes, and shears between two targets.

This modifier creates a fake muscle rig that connects two target nodes and deforms one or more constrained nodes with stretch, squash, and shear behavior.

## Options

- **`nodes`** (*list[node]*, optional): Nodes to be driven by the muscle rig.
- **`targets`** (*list[node]*): Exactly two nodes that serve as the start and end of the muscle (like tendons).
- **`parent`** (*node*, optional): Node under which the muscle rig will be parented. Defaults to the parent of the first node in `nodes`.
<!-- -->
- **`hook`** (*bool*, default: `false`): If enabled, connects the muscle output to the driven nodes via matrix constraints instead of parenting.
- **`weight`** (*float*, optional, default: `0.0`, min: `0`, max: `1`): Switch weight for blending the hook behavior.
<!-- -->
- **`orient`** (*bool*, default: `true`): Whether the tendon rig drives orientation.
- **`scale`** (*bool*, default: `true`): Whether the tendon rig drives scale.
<!-- -->
- **`stretch`** (*float*, optional, default: `1.0`, min: `0`, max: `1`): Blending value for the stretch behavior.
- **`squash`** (*float*, optional, default: `0.0`, min: `0`, max: `1`): Blending value for the squash behavior.
- **`shear`** (*float*, optional, default: `1.0`, min: `0`, max: `1`): Blending value for the shear behavior.
<!-- -->
- **`name`** (*str*, optional): Name used for the muscle rig. If not set, defaults to a combination of the target names.

## Example

```yml
rig.muscle:
  nodes:
    - eyebrow_mid::roots.0
    - eyebrow_crease::roots.0
  targets:
    - eyebrow.R::skin.0
    - eyebrow.L::skin.0
  orient: false
  name: eyebrow_mid
```

