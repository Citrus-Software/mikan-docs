# Cluster

Unlike the standard Maya workflow where you select the mesh first and then create a cluster that builds its own setup, our system follows a different approach.

You must first create the controller within the template, and add the cluster to it afterward once it’s built.
This workflow is required for several reasons:

- It improves the overall cluster behavior and integration.
- It ensures full compatibility with Tangerine.

To create a cluster, select an already built controller and assign a cluster to it using the following menu:

- **Tools > Cluster > Add Cluster to Controller**

![add cluster](./img/add_cluster.png)

Once this step is done, you need to **assign the meshes to the deformer sets** using the following menu:

- **Tools > Deformers > Assign Deformer Tool**

![assign cluster](./img/assign_deformer.png)

Next, adjust the cluster's influence areas on your mesh as usual.

Finally, don't forget to **save this information back to the template**, following the save procedure described in the [Overview](./overview.md) section.
