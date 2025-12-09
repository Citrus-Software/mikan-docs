# Quickstart Guide

Welcome to **Mikan**, the modular rigging toolkit built for both **Maya** and **Tangerine**. This guide helps you get up and running with Mikan inside Maya, from creating your first rig blueprint to assigning controller shapes and binding geometry with deformers.

By the end, you'll understand the full rig authoring loop: building, binding, and rebuilding rigs that are production-ready and fully modular.

## 🚀 Launching the UI

To open the Mikan interface inside Maya, run the following Python snippet in the script editor:

```python
from mikan.maya.ui import MikanUI
MikanUI.start()
```

## 🧭 UI Overview

The Mikan interface is divided into several tabs, each aligned with a core phase of the rigging process:

| Tab           | Purpose                                | Features                                                  |
| ------------- | -------------------------------------- | --------------------------------------------------------- |
| **Templates** | Build and organize the rig blueprint   | Rig Outliner, Add/Edit tools, build logs                  |
| **Shapes**    | Assign and customize controller shapes | Preset library, color and size adjustments                |
| **Deformers** | Save and manage deformation data       | SkinClusters, wrap deformers, backups, metadata injection |
| **Posing**    | Facial pose system (WIP/optional)      | Pose mirroring, management (early stage)                  |

Additional tools can be found in the main menu bar : scene utilities, rigging tools, pipeline scripts, and other production shortcuts.

## 🏗️ Building Your First Rig

### About Templates

Mikan rigs are built from **Template** modules. These are the components that define structure. Template modules contain metadata embedded within the hierarchy nodes, making it possible to reuse and export prebuilt blocks (like arms, legs, spines) across projects.

Template modules appear as joint chains in the scene. This means pre-built template parts can easily be exported, shared, and reused across characters and projects.

### Creating a Biped Blueprint

#### Create a New Asset

- Go to the **Templates** tab, then select the `Add` subtab and click `New Asset`
- Name the asset, this will become the root of your rig

#### Add Template Modules

With your new asset created, the next step is to populate it with **template modules**, these are the rig building blocks (spine, limbs, props, etc.). Each one must be inserted **manually and hierarchically**, to reflect the structure of your character.

#### Step-by-step logic

1.  **Select the parent template module**  
    In the **Template outliner**, click the module you want to attach your new part to. For example, to attach an `arm` or `leg`, you’ll typically select the `spine` module as the parent.

2.  **Open the Add tab**  
    In the **Templates > Add** section, choose the category (e.g. `arm`, `leg`, `spine`, `neck`, etc.), then select a specific module.  
    Module names follow a `family.variant` naming scheme. For example: `spine.default`, `arm.legacy` or `world.character`
3.  **Choose the right attachment point**  
    Once a parent is selected in the outliner, the **Add** subtab will let you pick the **attachment slot**. These are context-dependent and vary by module.

    For instance: To attach arms or the neck to a `spine`, choose the slot `spine.torso`

4.  **Click "Add New Template"**  
    This will insert the module in the scene as a guide, essentially a joint chain, that you can now position like any standard skeleton.

    You’re free to move, rotate, and align the joints to match your character mesh.

5.  **Configure Template Settings**
    - Select a template module: go to the `Edit` subtab
    - Adjust build options like joint orientation types, mirror behavior, rotation order, and more

#### 🧍 Quick Guide: Building a Basic Biped

Here’s a minimal and functional template setup to get a humanoid rig up and running:

1.  `world.character` defines the global root of the character
2.  `spine.default` creates the central spine chain under `world.root`
3.  `leg.default` adds a left and right leg under `spine.hips`
4.  `arm.default` adds a left and right arm under `spine.torso`
5.  `neck.default` adds a neck (this module also includes the head)

Once these are placed and adjusted in the scene, you’ll have a complete rig structure ready for configuration and controller shaping.

:::warning
At this stage, the modules are only skeleton guides. No controllers or visual shapes are generated yet, that comes next.
:::

📁 You can find a working example of this setup in `examples/tpl_biped.ma`.
It’s a great starting point or reference if you want to see the result in context.

## 🎮 Controller Shapes

In Mikan, all rig controllers are **NURBS curves**. Initially, a new blueprint contains no shapes.

To generate them:

1. Select one or more modules in the Template Outliner (or just the Asset to do all at once)
2. Click the `Shapes` button in the main toolbar (Templates tab)

This creates default controller curves for each part.

### Customizing Shapes

- Use the **Shapes** tab to change shape presets, colors, and scale quickly
- You can also manually edit the curves in Maya:
  - Move CVs to customize visuals
  - Adjust transform nodes if needed

As long as the curves remain correctly placed in the scene hierarchy, your edits will persist.

## 🚀 Building the Rig

With template placed and shapes assigned, it’s time to **build the rig**:

1. In the Templates tab, select any node from your rig in the Outliner
2. Click the **rocket icon** in the top-left toolbar to launch a build

By default, Mikan builds the rig in **Animator mode**, meaning it's immediately usable. To enable development-friendly behavior (e.g. easier inspection, custom debug modes), toggle **Dev Mode** using the wrench 🛠️ icon on the right side of the toolbar. This gives you more visibility into the rig internals during iteration.

### Rebuilding and Cleaning

To make changes:

- Click the **broom icon** to wipe the rig and go back to the editable template
- Then make edits and rebuild as needed

:::note
This fast iteration loop is one of Mikan’s core strengths. You can build, adjust, and rebuild with minimal friction.
:::

## 🔗 Binding Geometry

### Default Setup

By default, Mikan integrates the **bind joints** directly inside the rig structure. This works well for most cases, especially during early development, but **it doesn’t create a separate binding hierarchy** for game-ready or export-friendly skeletons.

This can be configured later using custom build options, but the initial setup assumes everything is embedded.

### Selecting Bind Joints

1. In the **Templates** tab, **right-click** on a module in the Template Outliner (preferably the top template `world` module)
2. Choose either `Select Bind Joints` or `Select Bind Joints Hierarchy` depending on how much you want from the outliner.

You can now perform a standard Maya `Bind Skin` operation.

:::info
Mikan supports many Maya deformers, though not all are fully covered yet. See the [Deformers Guide](../usage/deformers/overview.md) for details.
:::

## 💾 Saving & Reinjecting Deformer Data

Once binding is done, you'll want to **save the skinning weights** (and any deformers) back into the rig blueprint, so future builds can restore them automatically.

### Step-by-Step

1. Select your bound mesh (or group of meshes)

2. In the **Deformers** tab, click the **Create Backup** button (📦)

   This generates a group at the root of the scene, which includes all the relevant deformer metadata (skin, wrap, lattice, etc.)

   :::note
   📌 At this stage, the group is just a neutral transform and not yet part of the rig blueprint.
   :::
   
3. To link it with the rig:

- Parent the backup group into your Mikan **template group** in the scene
- If it doesn't appear, click the `Refresh` button in the toolbar

From now on:

- Rebuilding the rig will **automatically restore** your deformers
- Wiping the rig will **also remove** those deformers

:::warning
It’s crucial to ensure backups are properly saved in the rig blueprint before rebuilding, otherwise, you risk losing your skin weights.
:::

## ✅ What’s Next?

In this Quickstart, you've seen the full authoring loop:

- Building a modular rig blueprint
- Assigning controller shapes
- Binding geometry and saving deformer data

Next, we’ll dive into more advanced topics like:

- Custom **modifiers** for extending templates
- Facial rigs and **pose systems**
- Publishing rigs for **Tangerine scenes**
- Advanced workflows like **release vs anim layouts**
- Shape correction and deformation layers

For now, you’re ready to start building rebuildable, production-grade rigs with Mikan.
