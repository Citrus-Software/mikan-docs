# Authoring Templates

Templates are the fundamental building blocks of a Mikan rig. They define modular components (e.g., an arm, a spine, or a locator) that Mikan assembles to create a complete character.

A Template operates in two distinct phases:

1. **The Guide Phase**: A lightweight skeleton hierarchy used by the rigger to place pivots and define the controllers.
2. **The Rig Phase**: The functional animation setup (controls, constraints, deformers) generated during the build process.

To create a new template module, you must establish a specific directory structure, define a configuration manifest, and implement the Python logic for your target DCC.

## Anatomy of a Template

Before configuring a template, it is crucial to understand its structure within the scene (DCC).

A Template instance is represented as a joint hierarchy identified by a unique **Root Node**. Mikan traverses the full scene hierarchy to identify parent-child relationships between these instances.

Structurally, a Template consists of:

- **The Root**: The top-most joint of the template. It holds all the metadata required by Mikan to identify the module type and its settings.
- **The Hierarchy**: A chain of child joints defining the structure. Other templates can be parented to any joint in this chain (not just the end).
- **Guide Shapes**: NURBS curves parented to the joints. These visual cues serve as the blueprint for the shape of the final rig controllers.
  :::note
  The Template API allows an instance to query its own hierarchy, identifying which joints belong to it versus which joints belong to a child template.
  :::

### Connectivity and Hooks

A key feature of Mikan is flexible parenting. A child template can be attached to any joint of a parent template.

During the build process, every joint in a template creates a **Hook**. A Hook is an anchor point in the final rig where child modules attach their own rig logic.

**Example**: Consider an Arm Template containing a shoulder, elbow, and wrist.

1. The Arm hierarchy ends at the wrist joint.
2. **Finger Templates** are parented directly to the Arm's wrist joint in the Guide.
3. During the build, the Arm module generates a "Wrist Hook".
4. The Finger modules automatically find this hook and attach their respective build logic to it.

## File Structure & Registration

To add a new template module, you simply need to create a directory within the `mikan.templates.template` package. Mikan automatically scans this path on startup.

### Directory Layout

The folder name serves as the unique ID of the module. To organize templates into categories (e.g., biped, prop, vehicle), you should place your module folder inside a category folder.

**Standard Path**: `.../mikan/templates/template/[category]/[module_name]/`

**Required Files**: Inside your module folder, you must provide:
- `template.yml`: The configuration manifest.
- `[dcc].py`: The implementation logic (e.g., `maya.py`, `tangerine.py`).

```plaintext
mikan/
└── templates/
    └── template/
        └── arm/                 <-- Category (appears in UI)
            └── legacy/          <-- Module Name
                ├── template.yml
                ├── maya.py
                └── tangerine.py
```

### Loading the Module

Once your files are created:

- Restart your DCC or reload the Mikan Core.
- Open the Template Editor in the UI.
- Your module will appear in the defined category

:::note
The module name used in code will be derived from the folder names. For the example above, the template ID would be `category.name`.
:::

## Configuration Manifest (template.yml)

The `template.yml` file acts as the manifest for your module. It defines how Mikan presents the template in the UI, its default parameters, its inheritance logic, and crucially, how it maps physical joints to logical tags.

Below is a detailed breakdown of the available keys.

### Metadata & Inheritance

Basic information to identify the template in the Mikan ecosystem.

- **`name`**: The default ID string for a new instance of this template (e.g., "arm", "spine").
- **`desc`**: A short description displayed as a tooltip in the UI.
- **`help`**: A URL pointing to the full documentation for this module.
- **`base`**: Defines inheritance. The Python class for this template will inherit from the specified base class in addition to the standard DCC Template class.
    - *Example*: `base: _common.limb` (inherits methods from a generic limb module).

### Naming Conventions (`names`)

The **`names`** key is a dictionary that allows you to define default naming suffixes for specific parts of the rig. These are logical keys, not final names. They provide a default value that users can override in the UI if they have specific naming charts.

- *Key*: The internal ID used in your Python code (e.g., `cog`, `limb1`).
- *Value*: The default string suffix that will appear in the final node name.

### Structure & addressing

The **`structure`** key is a dictionary (`tag: address`) that maps logical names (tags) to specific joints in the Guide skeleton.

Tagging Strategy:

- **Relative Depth**: You define the location based on hierarchy depth relative to the root.
    - `.` : The Template Root joint.
    - `/1` : The immediate child of the Root.
    - `/2` : The child of the child, etc.
- **Explicit Tagging**: If no address is specified, Mikan searches for a joint that was explicitly tagged during the `build_template()` Python procedure.

:::warning[Important Constraint]
You cannot rely on sibling order (e.g., "the second child of X"). Scene formats like Alembic (`.abc`) do not guarantee child order preservation. Always use specific branches or explicit tags to ensure correct retrieval.
:::

### Parameters (`guides`, `opts`)

Defines the editable attributes exposed in the UI.

- **`guides`**: Options affecting the **Guide Phase** (e.g., initial positions, joint counts).
- **`opts`**: Options affecting the **Rig Phase** (e.g., IK/FK, spacer types).

Common properties for both:

- `value`: The default value. Determining the type (int `1`, float `1.0`, bool `on/off`, string).
- `min`/`max`: Range clamps for UI sliders.
- `enum`: A list of strings or a dict (`int: key`) for dropdown menus.
- `help`: Tooltip description.
- `yaml`: (Boolean) If `true`, the value is parsed as YAML data (useful for list-type options).

### Controllers Geometry (`shapes`)

A dictionary (`id: dict`) defining the default controller shapes. The `id` must match the controller ID generated during the rig build (e.g., if the build generates `arm.L::ctrls.1`, use `ctrls.1`).

- `parent`: The node that "holds" this shape curve in the Guide. **Must refer to a tag defined in `structure`**.
- `shape`: The shape ID from the internal database (`mikan.templates.shapes`).
- `color`: CSS-style color code (e.g., `red`, `#F00`, `#FF0000`).
- `size`: Scale multiplier (default: 1).
- `aim`/`up`: Entry point structure for the shape rig (for auto-orientation).
- `up_axis`: Axis used for the aim constraint.

### Logical Hierarchy (`hierarchy`)

This is a critical section. It describes the **Virtual Hierarchy** of the rig elements (Controllers and Skin Joints). This abstraction allows Mikan to reconstruct the skeleton logic (pickwalking, export) regardless of the complex physical constraints in the scene.

You typically define two main groups here: one for controllers and one for skin joints.

Key Properties:

- `root`: (Boolean) Declares if this node is a starting point of the local hierarchy.
    - If `on`: This node will look for the parent template's hook to attach itself.
    - If `off` (by default): This node expects a local `parent` to be defined.
- `parent`: Defines the internal parent within the *current* template.
    - *Value*: The ID of another node defined in this hierarchy section (e.g., `ctrls.limb1`).
- `hook`: Defines that this Rig Node acts as the anchor for a specific Structural Tag.
    - *Value*: A tag from the `structure` section.
    - *Behavior*: If a child template is attached to the tag `limb1` in the guide, its rig root will be automatically parented to this node (`ctrls.limb1`) in the final rig.
- `chain`: (Boolean) If the ID refers to a list of nodes, `chain: on` creates a serial hierarchy (0 -> 1 -> 2 -> ...).
- `opts`: conditional overrides. A dictionary allowing you to change `root`, `hook`, or `parent` logic based on a rig option (defined in `opts`).
    - *Format*: `option_name: override_dict`.
    - *Usage*: Useful if a boolean option (like `do_clavicle`) changes the parenting logic.

### Interface (`ui`)

- `icon`: Filename of the icon for the Mikan Outliner. (see: `mikan.core.ui.icons`)
- `hooks`: Defines valid attachment points for child templates.
    - *Format*: `display_name: structure`.
    - These appear in the UI when building a new template on top of this one.

### Example Configuration

Here is an example for a simplified **Arm** module.

```yaml
# Example Snippet
name: arm
desc: Basic biped arm
base: _common.limb

names:
  clav: clavicle # If user leaves default, node becomes "c_clavicle_L" or "sk_clavicle_R", etc.
  limb1: shoulder
  limb2: elbow
  limb3: wrist

# 1. Map physical joints to logical tags
structure:
  clavicle:      # Abstract tag for clavicle
  limb1: .       # Abstract tag for the shoulder/arm root
  limb2: /1      # 1st child
  limb3: /2      # 2nd generation

# 2. Options
opts:
  do_clavicle:
    help: Enables the clavicle rig.
    value: on    # Boolean option

# 3. Rig Hierarchy Definition
hierarchy:

  # The Clavicle Controller
  ctrls.clav:
    root: on            # Attaches to the body (parent template)
    hook: clavicle      # Exposes itself as the anchor for anything attached to 'clavicle'

  # The Arm/Shoulder Controller
  ctrls.shoulder:
    root: on            # DEFAULT: Starts a new chain (attached to body)
    hook: limb1         # Exposes itself as anchor for 'limb1'

    # CONDITION: If the user enabled the 'do_clavicle' option
    opts:
      do_clavicle:
        root: off       # It is no longer a root...
        parent: ctrls.clav  # ...it becomes a child of the clavicle controller.

# 4. UI Exposure
ui:
  icon: limb
  # Where can users attach a hand or prop?
  hooks:
    hand: limb3
    elbow: limb2
```

:::tip
Theoretical explanations can sometimes be abstract. The most effective way to master these configurations is to **inspect the `template.yml` files of existing Mikan modules**.

Analyzing built-in modules (like `legs.legacy` or `spine.legacy`) will give you concrete examples of how `hooks`, `opts` overrides, and inheritance are used in production contexts.
:::

## Python Implementation & DCC Support

Mikan is designed to be DCC-agnostic. While the configuration (`template.yml`) defines the *what*, the Python logic defines the *how*.

To support multiple softwares (Maya, Tangerine, Blender), the geometric construction logic is isolated in specific files. Mikan automatically loads the appropriate file at runtime based on the active environment.

**Inheritance Pattern:**
The API enforces a strict contract: method names and purposes remain identical across all platforms.

* **Maya:** `class Template(mikan.maya.core.Template):`
* **Tangerine:** `class Template(mikan.tangerine.core.Template):`

You must override the following three methods to define the module's behavior:

:::note
The examples below use Maya syntax (`mx`, `cmds`), but the logic applies equally to Tangerine or Blender APIs.
:::

### The Guide Phase: `build_template()`

Called when the user instantiates the module. Your goal is to build the visual skeleton used by the rigger to place pivots and define the volume.

- **Responsibility:** Create joints/locators and position them.
- **Crucial Step:** Use `self.set_template_id(node, 'tag')` to link the physical node to a logical tag defined in your YAML `structure`.

```python
def build_template(self, data):
    root = self.node  # The root node provided by Mikan

    # 1. Create Nodes (DCC Specific)
    with mx.DagModifier() as md:
        # Create a joint under the root
        hips = md.create_node(mx.tJoint, parent=root)

    # 2. Set Default Position
    hips['ty'] = 10.0

    # 3. TAGGING (Universal API)
    # Maps this specific node to the 'hips' tag defined in template.yml
    self.set_template_id(hips, 'hips')
```

### Dynamic Renaming: `rename_template()`

Called when the user renames the module in the UI. You must update the names of the physical objects in the scene to match the new ID.

- **Helper:** Use `self.get_structure('tag')` to retrieve nodes. **Never rely on string names**, as they may have changed or contain prefixes.

```python
def rename_template(self):
    # Iterate over tags defined in your structure
    for tag in ['hips', 'spine1', 'spine2', 'tip']:

        # Get the actual node object(s) associated with the tag
        nodes = self.get_structure(tag)

        if nodes:
            # Generate the new name using Mikan's naming engine
            new_name = self.get_name(tag)

            # Apply rename (DCC specific)
            nodes[0].rename('tpl_' + new_name)
```

### The Rig Phase: `build_rig()`

The main execution logic called during the global build. This method reads the guide, processes options, creates the rig components (controls, constraints), and registers the output.

This process consists of 4 key steps:

#### 1. Fetching Data

Retrieve guide positions and user options before building.

```python
def build_rig(self):
    # 1. Get the Parent Hook (The node where this module attaches)
    hook_node = self.hook

    # 2. Get Guide Nodes (From the Guide Phase)
    # Returns a list of nodes. We usually take the first one [0].
    tpl_hips = self.get_structure('hips')[0]

    # 3. Get User Options (Defined in YAML 'opts')
    # Mikan handles type conversion (bool, float, int) automatically.
    use_stretch = self.get_opt('stretch') 
```

#### 2. The Naming Engine

Mikan uses a modular naming strategy. You manually construct the final string by assembling a **Prefix**, a **Side** (node type), a **Core Name** (from config), and a **Suffix** (side/index).

- **The Suffix** (`get_branch_suffix`) Returns the current topological identifier (e.g., `_L`, `_R`, `_01`). Call this once at the start of your build.
- **The Core Name** (`get_name`) Resolves a logical token (e.g., `cog`) against the YAML `names` registry and any UI overrides.
- **Assembly** Concatenate these elements to create the final name.

```python
def build_rig(self):
    # ... 

    # 1. Get Suffix (Location)
    n_end = self.get_branch_suffix()  # e.g. "_L"

    # 2. Get Core Names (Semantic)
    n_cog = self.get_name('cog')  # e.g. "spine_cog"

    # 3. Assemble: Prefix + Core + Suffix

    # Root (Technical Parent)
    root_cog = mx.create_node(mx.tJoint, parent=hook, name='root_' + n_cog + n_end)

    # Controller (Animatable)
    c_cog = mx.create_node(mx.tJoint, parent=root_cog, name='c_' + n_cog + n_end)

    # Skin Joint (Bind)
    j_cog = mx.create_node(mx.tJoint, parent=c_cog, name='j_' + n_cog + n_end)
```

**Standard Prefixes:**

- `root_`: Zero group / Offset buffer
- `c_`: Controller
- `sk_`: Skinning joint

#### 3. Construction

Use the DCC-specific API or Mikan Helpers (see below) to build your kinematic logic (IK handles, constraints, connections).

#### 4. Registration (The "Binding")

Once the nodes are built, you must register them. This step connects your Python objects to the `hierarchy` IDs in the YAML.

- `set_id(node, 'id')`: Maps a rig object to a hierarchy ID (e.g., `ctrls.cog`).
- `set_hook(guide_node, rig_node, 'hook_id')`: Defines an attachment point for future child templates. (e.g., `hooks.pelvis`)

```python
    # --- Registration ---

    # 1. Register Control
    # Mikan will look at YAML 'shapes', find 'ctrls.cog', and draw the curve on 'c_cog'.
    self.set_id(c_cog, 'ctrls.cog')
    
    # 2. Register Hook
    # "If a child module attaches to the 'pelvis' hook, parent it to the 's_pelvis' node."
    self.set_hook(tpl_hips, s_pelvis, 'hooks.pelvis')
```

:::info[Automatic Shapes]
Do not write code to create controller curves. Mikan automatically draws shapes *after* `build_rig` finishes, based on the nodes you register here using `set_id`.
:::

## API Reference: `Template` Methods

Summary of the essential methods available within your `Template` class.

| Category         | Method                       | Description                                              |
|------------------|------------------------------|----------------------------------------------------------|
| **Data Access**  | `get_structure(tag)`         | Returns list of Guide nodes linked to the tag.           |
|                  | `get_opt(key)`               | Returns value of a Rig Option (YAML `opts`).             |
| **Naming**       | `get_name(token)`            | Generates full name string (Module + Side + Token).      |
|                  | `get_branch_suffix()`        | Returns current suffix (e.g., `_L`, `_01`).              |
|                  | `get_side()`                 | Returns side string (`L`, `R`, `C`).                     |
| **Registration** | `set_template_id(node, tag)` | **(Guide Phase)** Links physical node to YAML structure. |
|                  | `set_id(node, id)`           | **(Rig Phase)** Maps rig node to YAML hierarchy ID.      |
|                  | `set_hook(guide, rig, id)`   | **(Rig Phase)** Registers attachment point for children. |

## Helpers & Libraries

To ensure consistency and speed up development, prioritize using Mikan's internal libraries over standard DCC commands.

### Maya cmdx (`mikan.maya.cmdx`)

Aliased as `mx`, this wrapper speeds up drastically the DAG manipulation.

* `mx.DagModifier()`: Context manager for clean node creation.
* `node['ty'] = 5`: Direct attribute access.

### Rigging Helpers (`mikan.maya.lib.rig`)

Contains high-level rigging logic to avoid boilerplate code.

* `copy_transform(src, dst)`: Snaps position/rotation/scale.
* `orient_joint(joint, aim, up)`: Robust joint orientation.
* `create_joints_on_curve(curve, count)`: Generates chains along NURBS.
* `stretch_spline_ik(...)`: One-line setup for stretchy spline IKs.
* `fix_inverse_scale(chain)`: Automatically handles segment scale compensation.
* ...

## Complete Example

> TODO: Add a comprehensive example of a template implementation.

