# rig.rivet

> Constrains a node to a point on the surface of a mesh.

This modifier allows you to attach a node to a mesh using a UV pinning system or a closest-point projection. It is useful for rivets, attachments, or sticky locators.

⚠️ Does not work in Tangerine if the mesh scale is not (1, 1, 1).

## Options

- **`node`** (*node*, optional): The reference node used to calculate the rivet position.
- **`parent`** (*node*, optional): The node under which the rivet will be parented. Defaults to the parent of the `node`.
- **`geo`** (*node*): The mesh to rivet onto (required).
<!-- -->
- **`hook`** (*bool*, default: `true`): If true, parents the `node` under the created rivet transform.
- **`orient`**, **`rotate`** (*bool*, default: `true`): Aligns the rivet's orientation with the surface normal.\
  ⚠️ This defaults to `false` when using `closest` or `keepout` modes.
<!-- -->
- **`uv`** (*float2*, optional): Specific UV coordinates to follow. Defaults to the UV closest to the node position.\
  ⚠️ Not yet supported in Tangerine.
- **`closest`** (*bool* | *node*, default: `false`): Enables closest-point projection instead of UV pinning. If a node is passed, it is used as the projection source. If no node is provided, defaults to `node`. Disables `hook` automatically.
- **`raycast`** (*float3*, optional): A direction vector for the closest-point projection instead of using the node position.
- **`keepout`** (*bool* | *node*, default: `false`): Like `closest`, but activates only when the projection source is inside the mesh. Same source rules as `closest`. Disables `hook` automatically.
<!-- -->
- **`subdiv`** (*int*, default: `0`): Subdivides internally the mesh to increase projection precision.

## Example

```yml
rig.rivet:
  geo: msh_body->shape
  node: <id>::<node>
  parent: <parent>::<node>
  orient: true  # default
```

