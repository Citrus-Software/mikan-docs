# delta Mush

Filters deformation artifacts and smooths geometry.


## Features

- Works as a low-pass filter to remove deformation artifacts and bring the final result closer to the original rest pose geometry.  
- Useful for:  
  - cleaning rough skinning results  
  - smoothing coarse simulation outputs  
  - quick animation fixes  

The Delta Mush applies a Laplacian smoothing algorithm on the geometry in its rest state and stores the difference (delta) between the rest and smoothed states per vertex, in surface space coordinates.  
Then, the same smoothing is applied to the deformed geometry, and the stored deltas are reapplied in surface space to remove deformation artifacts introduced in the deformation chain.  


## Limitations

- Work in progress: not fully implemented in Tangerine yet.  


## Options

- `iterations (int)` : number of Laplacian smooth iterations  
- `smooth_step (float)` : smoothing strength per iteration  
- `displacement (float)` : amount of delta reapplication


## Hooks

- `enable`, `envelope (float, min: 0, max: 1)` : weight of the deformation application  
- `smoothing iterations (int)` : number of Laplacian smooth iterations  
- `smoothing step (float)` : smoothing strength per iteration  
- `displacement (float)` : amount of delta reapplication