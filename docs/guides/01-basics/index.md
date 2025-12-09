# Building a Basic Rig

## Main Interface Overview

Mikan's interface is divided into **four main tabs**:

- **Templates**: Create rig template modules and edit their options.

  ![templates tab](./img/onglet_template.png)

- **Deformers**: Manage deformation data for your meshes.

  ![deformers tab](./img/onglet_deformers.png)

- **Posing**: Create and edit poses for your rig.

  ![posing tab](./img/onglet_posing.png)

- **Shapes**: Edit the appearance of rig controllers.

  ![shapes tab](./img/onglet_shapes.png)

### Templates Tab in Detail

![template add tab](./img/onglet_template_add.png)

- **Toolbar Section**: Buttons to build or delete a rig, edit shapes, or select elements.
- **Outliner Section**: Hierarchy of the rig currently being built.
- **Build Section**:
    - **Add**: Choose template modules to add to the rig and set their initial options (these can be modified later).
    - **Edit**: Update options after a module has been created.
    - **Logs**: Track progress and messages during the build process.

## Creating Your First Rig

### Setting Up the First Modules

The first step is to create the **top node of the rig**: the **asset node**.  
This acts as the **rig container**. It stores all template data (module hierarchy, modifiers, deformation info) and manages their execution.

To create it:

- Go to the **Add** tab in Templates,
- Fill in the **Asset** field with your rig's name,
- Then click the **orange plus button** on the left.

![add asset](./img/add_asset.png)

With the top node in place, you can begin building your module hierarchy.

We typically start by adding a **`world` module**, the base module used to move the entire rig within the scene.

![add world module](./img/add_module_world.png)

This creates the **`c_world`** controller, along with:

- **`c_move`** for managing the asset's trajectory,
- **`c_fly`** _(optional)_ similar to move but with a pivot centered on the character's center of gravity,
- **`c_scale`** _(optional)_ for scaling the asset, with squash options and an adjustable pivot.

Click the **Toggle Shapes** icon to display controllers directly in Maya's viewport:

![world](./img/viewport_world.png)

From there, you can continue adding the modules required for your rig.

---

### Example 1: A Simple FK Chain

From the **Add Template** dropdown menu:

- Select the **Core** type,
- Then choose the **Bones** module.

![template module menu](./img/type_core.png)

**Main options include**:

- Number of joints,
- Root placement relative to the parent,
- Chain orientation.

Click **Add Template** to create the module.

:::tip
When you modify an option, it turns **blue**.  
👉 Right-click: **Reset** to restore the default value.

![reset options](./img/options_reset.png)
:::

### Example 2: Spine Module for a Character

From the **Add Template** dropdown menu:

- Select the **Spine** type.

As with the Bones module, you'll find general options (branch, symmetry), plus module-specific options such as:

- Number of bones,
- Controller orientation,
- Pivot of the first IK controller,
- Stretch.

![spine options](./img/spine_options.png)

### Placing and Adjusting Modules

After creating a module, you can position it relative to your geometry.

👉 No need to worry about joint orientation – **Mikan automatically adjusts this during the build, based on your chosen options**.

You can also **rename a module** and **update its options** by double-clicking it in the outliner (this opens the Edit tab).

![spine edit options](./img/spine_edit.png)

At any time, you can test the rig by running a **build** via the rocket icon at the top of the Templates tab.  
Build logs are displayed in the **Logs** tab.

![build rig](./img/build_rig.png)

## Customizing Controller Shapes

To display all controller shapes:

- Select the **asset top node**,
- Click the **Toggle Shapes** icon.

Then, select the shapes you'd like to customize and either:

- Use the **Shapes** tab,
- or modify them directly in the scene.

The **Shapes** tab provides:

- A library of preset shapes
- A color library
- Resizing tools

![shapes tab](./img/onglet_shapes.png)

:::warning

Always customize shapes on the template.

If you edit shapes directly on the rig, these changes will be lost on the next rebuild since only the template stores the information.

To preserve your adjustments, use the Rig to Tpl button to push those modifications back into the template.

![shapes rig to tpl](./img/shapes_rig_to_tpl.png)

:::

## Binding

Once your template is ready, the next step is **binding**, linking the rig to the model.

In complex hierarchies, skin joints can be hard to select.  
From the **Mikan outliner**: right-click > **Select skin joints**.

![select skin joints](./img/select_skin_joints.png)

Then skin your geometry as you normally would.

:::warning
Anything added manually (skinning, clusters, deformers, etc.) will be removed with each **rebuild**, unless those changes are saved in the **blueprint**.
:::

### Backing Up Deformers

1. Open the **Deformers** tab.
2. Select the **top node** of your modeling hierarchy.
3. Click **Create Backup Group** > this generates a transform node containing your deformation data.

   ![skin backup](./img/create_backup.png)

4. Parent this node under the **template group**.

   ![dfm geo](./img/dfm_geo.png)

This ensures the **blueprint** preserves your deformation data and reapplies it with every rebuild.

![deformer infos](./img/infos_deformer.png)

You can safely adjust your template, rebuild the rig, and keep your deformations intact.

:::info

### Good to Know

The Add tab gives you access to a set of default template modules provided by Mikan. These modules act as the basic building blocks for creating rigs.

👉 By combining these modules and enhancing them with modifiers, you can go far beyond simple setups. This allows you to design more complex and customized templates, perfectly suited to your production needs.

✨ Once created, these templates can be exported and easily reused across different assets, ensuring consistency and saving valuable time throughout the rigging process.
:::

## Conclusion

You've now covered the basics of rigging with Mikan:

- the **template / blueprint / rig** workflow,
- and the seamless process of **building, customizing, and iterating**.
