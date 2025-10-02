# nonlinear

To set up a nonlinear deformer, the process is the same as for clusters.  
As an example, let’s take the **nonlinear bend deformer**.  

To apply it, select the mesh you want to deform with a bend, then go to:  

- **Tools > Nonlinear > Add Nonlinear to Controller > Bend**  

:::note
You will have a menu entry for each type of nonlinear deformer.
:::

![add bend](./img/add_nonlinear_bend.png)  

Adjust the bend settings as needed.  

When finished, don’t forget to **save this information back to the template**, following the save procedure described in the *Deformers Overview* section.  


## Connecting the Curvature Attribute to the Rig

Once the bend is set up and the deformers are saved, you can expose the **curvature** attribute to animators by creating a small rig with modifiers.  

1. Create a helper node on the controller where you applied the bend.  
2. Add a modifier plug.  

**Example:**  

```yaml
[mod]
plug:
  node: tree_bend::ctrls.0
  bend:
    type: float
    set: 0
    keyable: on
```

Then connect the custom attribute to the curvature attribute of the bend:

```yaml
[mod]
#! -10
connect:
  node: msh_trunk->nonlinear.0@curvature
  op: mult
  inputs:
    - tree_bend::ctrls.0@bend
    - 0.5
```

:::note
Notice the priority defined for the connect note.
Here we want the connection to be made after the deformers have been set up.
By default, Mikan executes in order:

1. Template hierarchy

2. Modifiers

3. Deformers

To ensure this connection happens after the deformer setup, we add the note: `#! -10'`.
:::