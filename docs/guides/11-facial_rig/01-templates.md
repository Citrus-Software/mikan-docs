# Template Hierarchy

In this guide, we will build a facial rig based on skin joint deformation. This approach is generally more constrained than blendshape-based systems, but it offers significant advantages in terms of flexibility, robustness, and reusability. Historically, this type of setup has been well suited to television series production, where short schedules and
frequent model iterations require rigs that can adapt quickly.

Joint-based facial rigs are a proven solution. They have been used successfully on many productions and provide a solid, predictable foundation on which more complex behaviors can be layered. While they may require a bit more discipline in their construction, they tend to scale better across assets and episodes.

In the following chapters, we will go through the core steps required to assemble such a system using Mikan:

- Building a template module hierarchy for the facial rig and its animator interface
- Adding logic through modifiers to create the actual functioning of the rig
- Recording facial poses and linking them to the animator controls

:::note
Even if you are more familiar with blendshape-based facial rigs, following this guide is still relevant. The type of system presented here is fully compatible with blendshapes, and many of the concepts apply regardless of the deformation method. While some sections may not need to be followed in detail, the parts related to pose connections and the animator
interface remain essential.
:::

## Anchoring

Before building any facial structure, we first need to decide where the facial rig will attach to the rest of the character. It must be anchored to an existing part of the body hierarchy so that transforms, space, and evaluation remain consistent.

In our case, the body template is already set up. It consists of a simple biped, augmented with clothing modules and additional features. The head controller has already been created as part of the `neck.legacy` module, and it provides a natural attachment point for the facial rig.

This attachment point will serve as the root of the entire facial setup.

### Using a Group Module as a Facial Root

Rather than connecting facial modules directly to the head controller, we introduce an intermediate `core.group` module. This group acts as the root container for the facial rig.

Group modules serve several purposes:

- They provide a clean structural boundary for a rig subsystem
- They allow all facial controllers to be selected at once
- They help organize the animator interface hierarchy
- They are used by animator tools that operate on controller groups (mirror, select, key...)

In practice, this makes the group module an essential building block for both rig organization and animator workflow, even if it does not introduce any deformation or logic by itself.

### Creating the Facial Group Module

To set up the facial template:

1. Select the head hook output of the `neck.legacy` module
2. Add a `core.group` module at this connection point
3. Name the group module **`face`**

![add_group](./img/add_group.png)

This face group now becomes the root of the facial rig hierarchy. All subsequent facial modules and controls will be created under this group, ensuring that the facial system remains clearly organized, easy to manage, and properly integrated with the existing body rig.

## Skull hierarchy

The facial base starts with three parallel modules: **`skull`**, **`skull_mid`**, and **`skull_dn`**. These form the foundation for controlling the overall shape and motion of the face. This separation gives you the flexibility to create subtle rotations, offsets, and squash/stretch without affecting unrelated areas.

These modules are created using `core.joints` with the following option:

- **Type**: `transform`, each element is parented in a simple hierarchy and scales accumulate,

Once they are placed, enable toggle shapes to edit the controller shapes, then repeat the operation for each module.

![tpl skull](./img/skull_tpls.png)

## Lower face

### Jaws hierarchy

Next, we add two modules to open the mouth: **`jaw`** and **`jaw_up`**, parented under **`skull_dn`**.

For this, we use `core.bones` modules, which create an FK hierarchy, with the following options:

- **Rotate order**: `zxy` (to reduce gimbal issues)
- **Add pose node**: `on` (to anticipate the creation of facial poses for mouth opening)

![tpl jaw](./img/jaw_tpl.png)

### Lips group

Under **`skull_dn`**, we then create a **`lips`** group.
Like the `face` group, this group is used to gather all mouth controllers in the animator's right-click menu.

Then we add:

- **`lip_up`**, **`lip_dn`**: `core.joints`, type `transform`, to handle upper and lower lip movements.
- Tweakers: **`mouth1`**, **`2`**, **`3`**, **`up`** and **`dn`**, and **`mouth4`**: `core.joints`, type `joint`, to give animators the ability to sculpt the mouth as they wish.
- Second level: **`lip1`**, **`2`**, **`3` `up`** and **`dn`**, and **`lip4`**: `core.joints`, type `joint`, scale parent: `on`.

### Teeth and tongue

Under **`jaw`**, we add the lower teeth:

- **`teeth_dn`**, **`teeth_dn_tip`**, **`teeth_bend`**, **`teeth_bend_tip`**
  - All created as `core.joints`, type `joint`, with parent scale `on`.
  - Flip orient enabled for the teeth bend L/R.

Then we create the tongue:

- **`tongue_base`**, type `transform`, to scale the entire tongue,
- a chain **`tongue`** with `core.bones` of 3 joints for the rest of the tongue, auto-oriented, with up axis X and up dir +X.

The same logic as the **`teeth_dn`** is used for the **`teeth_up`**, parented under **`jaw_up`**.

![tpl mouthLips](./img/lips_tpls.png)

### Lips controllers

We add a global controller **`mouth`** as well as **`lips_corner`** for the mouth corners, both using `core.joints` modules, type `transform`, with flip orient `on` for the **`lips_corner`**.

:::note
👉 They will be connected later through the modifiers to the rest of the rig.
:::

## Mid face

We add a nose module and nostrils modules.

- **nose**: **core>joints**, type **joint**, parent scale **off**
- **nostrils**: **core>joints**, type **joint**, parent scale **on**

They stabilize the nasal area and connect the upper and lower parts of the face.

![tpl skukll mid](./img/skull_mid_tpl.png)

## Upper Face

For the upper part of the face, we add:

- an **Eyeroot** module, **core>joint**, type **joint**, with:
    - L/R branches,
    - flip orient enabled,
    - rotate order : ZXY.

This module allows the eye to move and scale on the face.

Then :

- **eye_socket**, core>xform, placed at the eye pivot,
- **eye**, core>joint, type transform, rotate order zxy, used to control the rotation of the eye (ctrl and skin tags unchecked),
- **eyeball**, core>joint, type transform, in preparation for the look-at rig.

For the eyelids:

- **eyelid_up** and **eyelid_dn**, core>joints, rotate order YZX,
- **eyelid_bend_up** and **eyelid_bend_dn**, core>joints, type transform, rotate order YZX,
- **eyelid_corner_ext** and **eyelid_corner_int**, same type as the bend modules,
- **eyelids_socket_up** and **eyelids_socket_dn**, same modules as the bend ones.

Ears:

- **ear_base**: core>joint, type joint, used to slide the ear over the skull, and
- **ear**: core>bones, rotate order ZXY, auto-orient, to move the ear itself.

Eyebrows:

- **eyebrow_base**: core>joint, type transform, used to control the global movement of the eyebrow, then
- **eyebrow1, 2 and 3**: core>joints, type joint, nb : 3, unchain, flip orient, for finer control.

:::note
👉 As always: for each template module, enable parent scale when needed, flip orient for branches, and do pose when required.
:::

![tpl face up](./img/eyes_tpl.png)

## Secondary offsets

Adding modules to enrich the control over the cheek area:

- **core>joints**, type **transform**,
- **flip orient** enabled,
- **do pose** enabled (anticipation of facial poses).

![tpl cheeks](./img/cheeks_tpl.png)

## look at

Next, we add a hierarchy to manage the **look-at system** with:

- an **eyes** module: core>joints, type transform, skin tag unchecked, placed between the two eyes,
- a **look_at** module, identical but placed in front of the face, controlling the gaze direction of both eyes,
- a **look_target** module, used to control each eye individually.

![tpl look_at](./img/look_at_tpl.png)

## Facial interface

To set up the **facial interface** intended for animators, we start with:

- a **face_panel**, created using a **core>xform** module, with the **parent = parent** option so that the panel group is attached to the face group.

We then create the rigger section and the animator interface.

### Rigger section

- **shp_lids**: core>xform module where all blink and eyelid-movement parameters will be stored.
- **shp_lips**: module for lip-related settings.
- **chan_face**: centralizes and manages all facial-rig connections, in the spirit of the BCS plugin, by aggregating all attributes onto a single controller.
- **shp_face**: node on which the facial poses will be recorded.

### Animator section

On the animator side, we set up the hierarchy used to build the interface:

- **frame_look**, a core>xform module providing a 2D interface for eye animation.
  - a look **core>joints** module for the global movement of both eyes,
  - a look_eye **core>joints** module for the individual movement of each eye,
  - a loc_look **core>xform** used to collect the movements of both c_look and c_look_eye.
- **frame_lipsync**, a core>xform module for the 2D mouth-movement interface.
  - a **lipsync** core>joints module to handle mouth opening.
- **face_up**, a core>joints module grouping all upper-face poses (eyes, eyebrows).
- **face_dn**, a core>joints module grouping all lower-face poses (mouth, teeth, cheeks).

![tpl dace UI](./img/faceUI_tpl.png)  
