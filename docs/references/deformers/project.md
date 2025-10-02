# project

Shrink wrap deformation.


## Features

- Allows projecting one geometry onto another in different ways.  


## Options

- `bidirectional (bool, default: on)` : enables bidirectional projection  
- `closest_if_no_intersection (bool, default: off)` : uses closest point projection if no intersection is found  
- `offset (float)` : offset distance from the projection surface  
- `target_offset (float)` : offset applied specifically to the target geometry  
- `target_subdivision (int, default: 0, min: 0)` : subdivision level of the target


## Hooks

- `enable`, `envelope (float, min: 0, max: 1)` : weight of the deformation  
- `offset (float)` : offset distance for the deformation  
- `target_offset (float)` : offset applied to the target geometry