# ffd

Deformation Cage


## Features

Lattice deformers allow you to deform objects using a cage-shaped mesh.  

:::warning
There is still an issue with translation relative to local mode in Tang.  
:::


## Options

- `maps ()` *(dict[int]: WeightMap)* : Weightmaps of the lattice on specific areas of the geometry.
- `lattice` *(string: id)* : lattice ID
- `base` *(string: id)* : baseLattice ID
- `stu` *(default: [2, 2, 2])* : cage subdivisions (resolution)
- `local_stu` *(default: [4, 4, 4])* : cage subdivisions (resolution)  
- `local ()` *(bool, default: off)* : defines calculation space (local vs world)
- `falloff ()` *(float, default: 1.0)* : smooth transition between inside/outside influence  
- `outside ()` *(int, default: 0)* : defines if points outside the cage are affected.


## Hooks

- `enable`, `envelope` *(float, min: 0, max: 1)* : weight of the deformation application  