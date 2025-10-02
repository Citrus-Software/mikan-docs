# Wire

To set up a wire, proceed as you normally would in Maya:  
select the curve to be used as the wire, then the target mesh, and apply the wire deformer through Maya's standard menu.  

Once your wire is applied, parent both your wire curves (`wire` and `baseWire`) under the **dfm** group.  
If the group does not exist yet, create it.  

:::tip
Over time, you may end up with many deformation objects under the **dfm** group.  
For better readability, it is recommended to organize them into intermediate subgroups.  

**Example:**

- `dfm_eyes` for lattices applied to the eyes  
- `dfm_eyebrows` for wires applied to the eyebrow meshes  

![dfm organization](./img/rangement_dfm.png)
:::

Finally, as with any other deformer, save the deformation information as described in the *Deformers Overview* section, by selecting the mesh to which the wire was applied.  


## Important

If you add a deformer to your wire curve (for example, skinning), make sure to also save this information into the template.  
Do this the same way you would save deformers on meshes:  

1. Select the **dfm** group  
2. Go to **Mikan > Deformers tab > Create Backup Group**  
3. Parent the created backup group under the template  

![save dfm](./img/save_dfm.png)  
