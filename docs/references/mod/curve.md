# curve

> Creates a curve by connecting nodes' transforms to curve CVs.

This modifier generates a curve where each control vertex (CV) is driven by the local transform of a specified node. It's commonly used for visual debugging or helper overlays in rigging.

## Options

- **`nodes`** (*node* | *list[node]*): One or more nodes whose local matrices will drive the curve CVs.
- **`parent`** (*node*): Optional parent for the curve. Defaults to the parent of the first node.
- **`degree`** (*int*): Degree of the resulting NURBS curve (typically 1 for linear, 2 for quadratic and 3 for cubic).
<!-- -->
- **`name`** (*str*, optional): Custom name for the curve. Defaults to the formatted `nice name` of the node tag.

## Example

```yml
[mod]
helper:
  nodes:
    - spine::ctrls.0
    - spine::ctrls.1
    - spine::ctrls.2
  parent: spine::hooks.mid
  degree: 3
  name: spine_curve
```

This will create a cubic curve named `spine_curve`, parented under `spine::hooks.mid`, and driven by the transforms of the three spine controllers.

