# cluster

Applies a common transformation to a set of points..


## Features

The cluster allows you to grab a set of points and apply a transformation matrix driven by a rig controller.  


## Options

- `handle (id)` : controller node used for manipulation  
- `bind_pose (id)` : root node of the controller  
- `soft (bool, default: off)` : enables soft mode  
- `falloff_mode (int, default: 0)` : falloff propagation type  
  - `0`: volume  
  - `1`: geodesic  
- `falloff_radius (float, default: 1.0, min: 0)` : radius of the falloff sphere  
- `falloff_center (float3, default: [0, 0, 0])` : world-space position of the falloff center  
- `falloff_pivot (id)` : positioning node for the origin of the soft mode (usually the same as `bind_pose`)  
- `falloff_curve (dict)` : construction data for the falloff curve  
- `maps (dict[int]: WeightMap)` : cluster weightmap (only works with index 0)  


## Hooks

- `enable`, `envelope (float, min: 0, max: 1)` : weight of the deformation application  
- `radius`, `falloff_radius (float)` : radius of the soft mode  