# locator

> Duplicates one or more locator-like nodes with optional parenting, shapes, and behaviors.

This modifier is used to create locator transforms, optionally shaped or joint-based, matching the transforms of one or more source nodes.

## Options

- **`node`** (*node*, optional): Single node to duplicate.
- **`nodes`** (*list[node]*, optional): List of nodes to duplicate. Overrides `node` if both are provided.
- **`parent`** (*node*, optional): Parent node under which the new locator(s) will be inserted. Defaults to the parent of the source node.
<!-- -->
- **`name`** (*str*, optional): Base name of the locator(s), used for both display and ID tagging.
- **`prefix`** (*str*, default: `loc`): Name prefix for the created nodes. Changed to `sk` if `skin` is enabled.
<!-- -->
- **`root`** (*bool*, default: `false`): Adds a transform node above the created locator (acts as a parent).
- **`skin`** (*bool*, default: `false`): Creates a joint instead of a standard transform node.
<!-- -->
- **`locator`** (*bool*, default: `false`): Adds a locator shape under the transform.
- **`size`** (*float*, default: `0.1`): Size of the locator shape (if `locator` is enabled).
- **`shape`** (*bool*, default: `false`): Copies the shape (curve) from the source node if present.
<!-- -->
- **`copycat`** (*bool*, default: `false`): Connects the source node's local matrix to the duplicated locator. The result mimics the source node's transforms (translation, rotation, scale, shear).

## Example

```yml
locator:
  node: bone::tip
  parent: world::node
  name: loc_bone
```

