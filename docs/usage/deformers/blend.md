# Blend

To apply a blendshape, select the target shapes first, then the destination mesh,  
and create the blendshape deformer as you normally would in Maya.  

Once the blendshape is set up, **save this information into the template**  
by selecting the mesh that carries the blendshape and following the save procedure described in the *Deformers Overview* section.  

:::warning
⚠️ Tang does not support in-betweens. Only shapes with weight = 1 will be saved (index 6000 in Maya).  
Tang also does not recreate groups or their weights.  
:::

## connection to the rig

If you want animators to drive specific blendshape weights directly in the rig,  
you can expose these weights through modifiers, in the same way as for nonlinear deformers (e.g. connecting a custom attribute on a controller to a blendshape channel).
