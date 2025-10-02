# ffd

To set up a lattice, select your meshes and apply a lattice as you normally would in Maya using the **Deform > Lattice** menu.  

Once your lattice and lattice base are created, parent them under a group named **dfm**.  

![dfm group](./img/dfm_grp.png)  

Mikan will then know that it should look for the deformers created inside this group.  

Adjust the shape of your lattices as you normally would.  

Finally, as with any other deformer, save the deformation information as described in the *Deformers Overview* section, by selecting the mesh to which the lattice was applied.  

:::note
If you apply additional deformers (e.g. skinning) directly on the deformation objects (here, a lattice),  
make sure to also save them by selecting the **dfm** group that contains the lattice with deformers,  
then follow the backup process described in *Deformers Overview*.  
:::