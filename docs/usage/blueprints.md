---
sidebar_position: 1
---

# Template Blueprints

Mikan is a modular rigging engine built around explicit rig construction hierarchies. Its main units are **templates**, enriched with **modifiers** and **deformers**, and organized under a unified structure in Maya.

Each rig is encapsulated in an **Asset**, which acts as the top node containing and orchestrating the entire build process.

## Asset & Hierarchy

An **Asset** is the top-level container and orchestrator for a rig.

- **Isolation:** Multiple Assets can coexist in the same scene as independent rig roots.
- **Export:** Blueprints are designed to be exported via hierarchical formats (like Alembic).
- **Scope:** Only elements parented under the Asset's `template` group are considered part of the build process.

## Anatomy of a Blueprint

A Mikan blueprint defines structure and logic using conceptual object types, inferred from naming conventions and metadata.

| **Type**   | **Role in the Rig**                              | **Notes**                                                                   |
|:-----------|:-------------------------------------------------|:----------------------------------------------------------------------------|
| `Asset`    | Top-level container.                             | Multiple assets can exist in a scene. Each acts as an isolated rig root.    |
| `Template` | Main building blocks (skeleton guides & pivots). | Represented by transforms and joints. Blueprint logic starts here.          |
| `Helper`   | Logical groupings or offset spaces.              | Used to define behaviors or structure without affecting the guide layout.   |
| `Shape`    | Visual handles for animation.                    | Ignored by the mechanical build process but used to define controllers.     |
| `Modifier` | Logic metadata (YAML).                           | The programmable layer. Defines custom rigging behaviors, constraints, etc. |
| `Deformer` | Binding metadata.                                | Executed after modifiers. Covers `skinClusters`, wraps, lattices, etc.      |

## Templates Modules

Template modules are the fundamental units of a Mikan rig. In Maya’s Outliner, they are easily identified by their **blue color**.

### ID and Scope

Each template is a self-contained module within the Asset, defined by its position and naming in the Maya hierarchy:

- **Unique ID:** Every template is identified by a unique **ID** assigned to its **root node** (the blue node in the Outliner).
- **Hierarchical Range:** A template's scope starts at its root and encompasses all descendant nodes until another template root is encountered.
- **Configuration:** All settings and options are stored on the root node as attributes prefixed with `gem_` (a legacy prefix from the *Gemini* engine preserved for compatibility).
- **Order Independence**: The relative order of sibling nodes is ignored. The build logic relies strictly on parent/child relationships, ensuring consistent results even after an Alembic export.
- **Helper Nodes:** Use an underscore (`_`) prefix for nodes that should be excluded from the skeletal build (e.g., controller shapes, logic notes, or groups).
- **UI Automation:** While the Mikan UI handles this nomenclature for you when adding elements, understanding this rule is essential for manual inspection of the blueprint.

### Output and Hooks

When the rig is built, every joint in a template generates a corresponding **rig node** called a **Hook**. Hooks are the connection points: a child template's rig will automatically be parented under its parent’s hook.

### Symmetry and Branches

The `branches` system allows for procedural duplication (e.g., L/R limbs).

- **Suffixes:** Standard pairs like `L/R`, `up/dn`, `ft/bk` are recognized automatically.
- **Automation:** Mirrored branches automatically invert axes and flip transformations for joints and controllers.
- **Edit Mode:** You can "freeze" branch placement using **Set branch edit** in the context menu to manually tweak asymmetrical poses.

### Creation & Editing

- Templates are created via the **"Add"** tab in the Template UI.
- The name defines the node IDs and must be unique within the Asset.
- Hierarchies can be modified in the Maya scene (e.g., reparent the root, add children).
- Changing a template's type is more complex as it may require adapting its structure.

Templates can also be imported as Maya **references** and follow the same construction rules.

### Options

Each template instance has configurable **options**:

- Modified values are stored on the root node as attributes (`gem_opt_*`).
- Options are shown in **blue** in the interface if modified.
- If the value is reset to default, the label turns **dark blue**, but the attribute still exists.

## Shapes & Controllers

Shapes are the visual curves used as animation controllers. In Mikan, they are authored directly within the Blueprint hierarchy.

### Automatic Organization

To keep the Outliner clean and prevent shapes from being interpreted as joints, Mikan automatically organizes them:

* Shapes are parented under a specific group (usually starting with `_`) within the template.
* This group acts as a "container" that the parser ignores during the skeletal build pass.

### Editing Shapes

While you can manually edit shape nodes in the Outliner, the most efficient way to customize your controllers is through the **"Shapes" tab** in the main Mikan UI.

* **UI-Driven Editing:** The Shapes tab allows you to quickly replace, scale, or tweak the look of selected controllers. Mikan automatically handles the replacement and ensures the new shapes are correctly parented and tagged within the blueprint.
* **Safe Manual Tweaks:** If you prefer working directly in the viewport or Outliner, you can still edit, scale, or swap curve nodes manually. As long as they remain within their designated [**Helper Node**](#helper-nodes) containers, Mikan will preserve these changes during the next build.

## Helper Nodes

Any node whose name begins with an underscore (`_`) is considered a **Helper Node**. These nodes are skipped by the template parser, meaning they are not treated as part of a module’s hierarchy, but they can still be used to carry controller shapes, modifiers or deformers.

If a node is named with double underscores (e.g. `__meta__`), it becomes an **Internal Helper Node**, completely hidden from the interface.

### Why use them?

Since Helpers are "invisible" to the skeletal build but "visible" to the logic build, they allow you to attach complex data exactly where you need it in the hierarchy without affecting the final joint chain. This is how Mikan attaches a curve shape to a guide without that curve being interpreted as a bone.

If you are inspecting a Blueprint and see nodes starting with `_`, you know they are "Logic/Visual" nodes and not part of the final joint chain.

## Scheduler & Build Process

Mikan uses a specialized **Scheduler** to scan the hierarchy and plan the construction order.

### Build Flow:

1. Traverse all template hierarchies top-down.
2. Build structural skeleton and rig.
3. Identify modifiers and deformers.
4. Schedule execution.

### Execution Rules:

- **Modifiers run before deformers**.
- **Hierarchy determines order** (top to bottom).
- **Sibling order is ignored** (e.g., Alembic does not preserve it).
- **Command priorities** are respected.
- Commands with missing dependencies are **retried at the end**.
- Active **build modes** and **node activation states** are evaluated.

## Build Modes

Mikan uses a **flexible flag-based system** to control construction behavior. Multiple modes can be active simultaneously.

Each mode signals the build system how to act, whether for dev, debug, or production.

### Mode Categories

#### Development

- `dev`: Skips cleanup final steps, easier rig inspection.
- `debug`: Halts on first error, useful for troubleshooting.

These are usually combined during rigging work.

#### Production Targets

Defined by the pipeline. Examples:

- `anim`: Full animation-ready build.
- `layout`: Minimal layout/previs rig.
- `render`: Export or rendering rig.

These modes help control visibility, performance, or structure depending on the context. For example, an `anim` rig might include deformation controllers, while a `layout` rig might not.

:::warning
These modes should be defined and managed by **production** to match the studio’s specific delivery workflows.
:::

#### Host-Specific

These are internal flags automatically enabled depending on the environment where the build is launched:

- `maya`
- `tangerine`

They can be used for fine-tuned adjustments specific to the host application.

### How to Use Modes

Modes can be added to any element of the rig template. Each element supports:

- A list of **required modes** (the element is built only if **all** are active)
- A list of **excluded modes** (the element is skipped if **any** are active)
- A **disable** flag (to skip the element regardless of modes)

Child nodes inherit the mode settings from their parent unless overridden.

#### Syntax

These settings can be configured in the **Edit** tab of each element.

- Multiple modes: `anim, render`
- Exclusion: `~debug, ~dev`
- Disable: check the **Disable** box

### Example: Bind Pose vs Animation Pose

A common use case is separating the **technical bind pose** from the **final animation pose**. For example, a character might be delivered with a straight tail to simplify deformation setup. To match the artistic pose, a **modifier** applies driven keys after the bind.

To avoid issues during rigging, condition the modifier to exclude the `dev` mode. This prevents it from running while authoring, making iteration faster and reducing errors. At delivery time, since `dev` mode won’t be active, the final pose will be correctly applied.
