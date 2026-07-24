---
sidebar_position: 5
description: Learn how to manage set driven keys, blendshape collections, and bridge them with Mikan template modules.
---

# Shape Editor

The **Shape Editor** is designed to manage collection sets for both **Set Driven Key (SDK) poses** and **blendshapes**. It provides a unified interface to build, edit, and seamlessly bridge pose setups into Mikan's template pipeline.

## Prerequisites & Scene Setup

Before using the Shape Editor, make sure your setup meets the following conditions:
- **The rig must be built.**
- **A Driver node** is defined.
- **A Group module** is assigned.

### Driver Setup

The **Driver** is the DAG node that holds the custom attributes used to drive your shapes (similar to Maya’s native *Set Driven Key* driver).

- **Recommended Node:** While any DAG node can act as a driver, it is strongly recommended to use a **Locator** rather than an animation control. This creates a clean separation between animator controls and internal rig logic.
- **Logic & Connections:**
  - **Facial Rigging:** Driven key distribution across controls is typically handled using the `shape.channel` modifier.
  - **Pose Space Deformation (PSD):** Custom network connections are used (dedicated PSD modifiers are planned for future updates).

![Driver Setup](img/init_driver.png)

### Group Setup

Depending on whether you are working with driven key poses or target mesh sculpts (blendshapes), the **Group** setting behaves as follows:

#### 1. Driven Key Poses
The Group defines the root template module used to scan for controls and pose nodes.
- Typically, set this to a `core.group` module to easily wrap a sub-hierarchy.
- When saving a pose, the tool scans all nodes tagged as `*::poses` parented under this group, transferring the control's position onto the corresponding pose transform.

> **Supported Modules:** Pose nodes can be generated automatically from `core.joints`, `core.bones`, `rig.spline`, and `digit.legacy` modules by enabling the `do_pose` option.

#### 2. Blendshapes
The Group defines the target group name under which blendshape targets are organized within Maya's blendshape nodes.
- Targets can be created automatically from the editor interface.
- If created manually (or imported from an external scene), you must manually group them using the exact Group ID name within Maya's Shape Editor.

:::tip Combined Workflow
You can combine both methods on the same setup by ensuring your blendshape group uses the exact same name as the template module used for driven key poses.
:::

## Scene Preparation

### 1. Driven Key Poses Setup

To allow the Shape Editor to record driven keys, your rig hierarchy must contain paired pose and control nodes following this naming convention:

- **Pose Node:** `{tpl}::poses.{n}`
- **Control Node:** `{tpl}::ctrls.{n}`

When saving a pose, the script retrieves the controller's world matrix/transform and applies it directly onto the associated pose node.

:::note Automated Creation
You don't need to create these manually. Enable the `do_pose` option on any `core.joints`, `core.bones`, `rig.spline`, or `digit.legacy` module during template setup.

![Pose Setup](img/init_pose.png)
:::

### 2. Blendshapes Setup

- **Internal Creation:** Mesh targets can be generated directly from the Shape Editor interface as long as base meshes exist in the scene.
- **Ingested Target Pipelines:** When importing external shape scenes, place all target shapes into a group named after the **Group ID** you intend to pass into the interface field.

![Blendshape Group Setup](img/bs_group.png)

> **Key Advantage:** Grouping blendshape poses onto a single driver allows a single attribute to immediately control targets across multiple geometries. The Shape Editor makes multi-mesh blendshape editing straightforward and centralized.

## Interface & Usage

:::info Working Scene
All interface screenshots in this section refer to the sample scene available on [Google Drive](https://drive.google.com/file/d/1CTO1OYbdxel6mbV3iUwIjKN33i96Jjpy/view?usp=drive_link).
:::

The interface is divided into two primary sections:
- **Left Panel (Control Utilities & Pose Shelf):** Handles control selection sets, transform tools (Reset, Mirror, Flip), value multipliers, and temporary iteration poses.
- **Right Panel (Driver Management):** Handles driver assignment, custom attribute creation, and saving/deleting shape poses.

![Shape Editor Overview](img/shape_editor.png)

### Selector Toolbar

- **Driver:** Sets the DAG node that will host driving attributes for your shapes.
- **Group:** Defines the active module/controller set.
  - Clicking **Group** automatically climbs up the hierarchy from the active selection to find the top `core.group` module.
  - *Troubleshooting:* If the field fails to populate, verify that the hierarchy beneath the group contains controls with valid pose nodes.
  - *Manual Override:* **Right-click** the Group field to choose a specific module manually from the dropdown list.
- **Save Mods:**<br/>
  ![Save Mods Buttons](img/btn_save_mods.png)
  - **Save Mods:** Transfers all driver attribute creation commands and existing scene driven keys into template modifiers.
  - **Auto Checkbox:** Automatically saves modifiers back to the template on every pose save operation.
  - **Clean Checkbox:** Removes driven keys associated with deprecated poses that no longer exist on the driver node.

### Controllers

- **Reset / Mirror / Flip:** Quickly clear or transfer transforms across all controls or currently selected controls.<br/>
  ![Reset Mirror Flip Buttons](img/btn_controllers.png)
- **SRT Scale Values:** Numeric buttons allow you to multiply or divide SRT values on selected controls. This is particularly useful when sculpting L/R split poses (e.g., dampening or amplifying values).<br/>
  ![Reset Mirror Flip Buttons](img/btn_mult.png)

### Pose Shelf (Iteration Buffer)

The **Pose Shelf** acts as a temporary storage buffer for poses while you sculpt:

- Useful when iterating on facial skinning setups to quickly compare pose variations.
- **L/R Split Workflow:** Store a full symmetrical pose on the shelf, perform your L/R split, store the split result, and easily flip it to the opposite side using the control tools.

![Reset Mirror Flip Buttons](img/shelf_editor.png)

#### Shelf Controls:
- **Save Edit:** Caches the current transform states of all controllers within the active group.
- **Save Selection:** Caches current transforms and custom attributes for the active selection only.

Saved poses generate shelf buttons for one-click recall. Right-clicking a pose button opens a context menu with the following options:

- **Delete:** Removes the selected pose item.
- **Clear:** Clears all pose items from the shelf.
- **Clear Poses:** Removes all *Save Edit* entries.
- **Clear Selections:** Removes all *Save Selection* entries.
- **Import:** Loads shelf items from a saved configuration file.
- **Export:** Saves all current shelf items to an external file.

### Pose Editor

The **Pose Editor** panel manages all poses via the assigned Driver node. Once both the **Driver** and **Group** fields are set, the attribute list automatically populates with available pose attributes.

Existing blendshapes in the scene that are correctly structured in a matching target group will be loaded and connected via driven keys automatically, making them controllable directly from the driver.

The editor allows creating and recording new pose attributes, both as driven keys on pose nodes and as target mesh blendshapes.

#### Main Actions

![Pose Editor Buttons](img/btn_pose_editor.png)

- **Save:** Records controller transforms onto the selected pose attribute.
- **Clear:** Removes driven keys associated with the selected attribute.
- **Reset:** Zeroes out all attribute values on the driver node.
- **Mute:** Temporarily disables pose node transforms for selected attributes. This is useful when editing a shape at a partial weight (e.g., 50%) without interference from previously recorded pose data.
- **Recall:** Re-applies pose node transform values back onto the controllers for further editing *(Note: this overrides current controller transforms)*.
- **Bake:** Bakes the combined total deformation of pose nodes and current controller transforms directly onto the controllers.

#### Attribute List Context Menu

Right-clicking in empty space within the attribute list provides:
- **Add Shape:** Adds a new shape attribute to the driver.
- **Reset All:** Resets all driver attributes to their default values.

Right-clicking an existing attribute entry provides:
- **Save Pose / Clear Pose / Recall Pose / Bake Pose / Mute Pose:** Performs the corresponding main action on the highlighted attribute.
- **Add Sculpt Target:** Generates and connects a new blendshape target on the selected meshes.
- **Select Attributes:** Selects the attribute nodes in the Maya scene and highlights them in the Outliner.
- **Reset Selected:** Resets selected attributes to default values.
- **Remove Selected:** Deletes the selected pose attributes.
- **Add Shape:** Adds a new shape attribute to the driver.
- **Reset All:** Resets all driver attributes to default.