# nonlinear

Deformation using mathematical functions.


## Features

- Nonlinear deformers allow geometry to be deformed using a mathematical formula.  
- They require a manipulator (handle) installed by the rig and a set of parameters specific to each formula.  


## Options

- `type (str)` : formula of the deformer  
  - `bend`  
  - `flare`  
  - `sine`  
  - `squash`  
  - `twist`  
  - `wave`  
- `handle ()` : manipulator node  
- `plugs (dict)` : connection data for attributes  
- `maps (dict[int])` : weight maps  


## Hooks

- `enable`, `envelope (float, min: 0, max: 1)` : weight of the deformation

### Bend

- `curvature` : amount of bend applied to the geometry (positive = one direction, negative = opposite)  
- `low_bound` : defines the lower limit of the geometry (Y axis of the handle) that is influenced.  
- `high_bound` : defines the upper limit of the geometry influenced.

### Flare

- `curve` : controls the curvature of the flare between start and end values.
- `start_flare_x` : scaling factor along X axes at the bottom (start) of the object.
- `start_flare_z` : scaling factor along Z axes at the bottom (start) of the object.
- `end_flare_x` : scaling factor along X axes at the top (end) of the object.  
- `end_flare_z` : scaling factor along Z axes at the top (end) of the object.

### Sine

- `amplitude` : height of the sine wave (intensity of the oscillation).  
- `wavelength` : distance between two peaks of the sine wave.  
- `offset` : shifts the sine wave along its cycle (phase).  
- `dropoff` : controls how the wave influence fades away along the geometry.  

### Squash

- `factor` : amount of squash or stretch applied. Positive = stretch, negative = squash.
- `expand` : additional scaling applied outward when squashing/stretching.
- `max_expand_pos` : position where the maximum expansion occurs along the geometry.  
- `start_smoothness` : controls how smooth the deformation transition is at the start bounds.
- `end_smoothness` : controls how smooth the deformation transition is at the end bounds.

### Twist

- `start_angle`: rotation angle at the start (bottom) of the geometry.  
- `end_angle`: rotation angle at the end (top) of the geometry.

👉 Together they define how much twisting happens along the geometry’s length.

### Wave

- `dropoff_position` : position along the wave where the influence starts to fade.  
- `min_radius` : minimum radius of influence (tightest part of the wave effect).  
- `max_radius`: maximum radius of influence (widest part of the wave effect).