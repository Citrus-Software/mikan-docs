# blend

Deformer used for shape morphs.


## Features

- One shape per attribute.  
- Each shape animates from 0 to 1.  
- It is possible to either connect geometries in the targets or directly provide position deltas.


## Limitations

- Tang does not support in-betweens. Only shapes with weight = 1 will be saved (index 6000 in Maya).  
- Tang does not recreate groups or their weights.  


## Options

- `targets (dict[int]: ids)` : dictionary of geometry targets by index  
- `names (dict[int]: str)` : dictionary of shape names by index  
- `weights (dict[int]: float)` : dictionary of initial shape weights  
- `maps (dict[int]: WeightMap)` : dictionary of weight maps for each shape by index  
- `delta (dict[int]: WeightMap3)` : dictionary of shape deltas by index  
- `groups (dict)` : group construction data  


## Hooks

- `enable`, `envelope (float, min: 0, max: 1)` : weight of the deformation application  
- `target.{n} (id)` : geometry connection for target *n*  
- `weight.{n} (float)` : application weight of target *n*