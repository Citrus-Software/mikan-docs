# skin

Skeletal Deformation


## Features

Attaches a geometry to a skeleton.

:::warning
⚠️ Dual Quaternion does not yet work perfectly with skin layers.
:::

## Options

- `mi` *(int)* : maximum number of influences per vertex  
- `mmi` *(bool, default: off)* : maintains a maximum number of influences per vertex  
- `normalize` *(bool, default: on)*  
- `method` *(int, default: 0)* : interpolation calculation mode  
  - `0`: linear  
  - `1`: dual quaternion  
  - `2`: dq blend  
- `dq` *(bool, default: off)* : enables dual quaternion  
- `infs` *(dict[int]: id)* : list of influence joints  
- `bind_pose` *(dict[int]: id)* : list of joints used for the bind pose reference  
- `bind_pose_root` *(dict[int]: id)* : list of joints used for the bind pose reference when the reference is offset  
- `maps` *(dict[int]: WeightMap)* : list of weight maps for each influence joint  


## Hooks

- `enable`, `envelope` *(float, min: 0, max: 1)* : weight of the deformation application  