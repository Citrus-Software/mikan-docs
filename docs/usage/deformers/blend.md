# Blend

To apply a blendshape, select the target shapes first, then the destination mesh, and create the blendshape deformer as you normally would in Maya.

Once the blendshape is set up, **save this information into the template**  
by selecting the mesh that carries the blendshape and following the save procedure described in the [Overview](./overview.md) section.

:::warning
Tangerine does not support in-betweens yet. Only shapes with weight = 1 will be saved (index 6000 in Maya).  
Tangerine also does not recreate groups or their weights.  
:::

## Connection to the rig

If you want animators to drive specific blendshape weights directly in the rig, you have to expose these weights through modifiers with corresponding hooks. Detailed informations in the [technical references](/docs/references/deformers/blend.md).
