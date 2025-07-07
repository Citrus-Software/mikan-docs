---
sidebar_position: 1
---

# Template Blueprints

Mikan is a modular rigging engine built around explicit rig construction hierarchies. Its main units are **templates**, enriched with **modifiers** and **deformers**, and organized under a unified structure in Maya.

Each rig is encapsulated in an **Asset**, which acts as the top node containing and orchestrating the entire build process.

## Asset & Hierarchy

An **Asset** is the root node for a rig in Maya. It holds all the elements required for construction: templates, modifiers, deformers, and helper nodes.

- Assets can coexist in the same scene.
- Each Blueprint can be exported using a hierarchical 3D format (e.g., Alembic).
- All elements must be parented under the Asset's `template` group to be considered part of the template.

The Asset node schedules the build process and controls the execution order.

## Templates Modules

### Role & Structure

Template modules are the building blocks of the rig. Each template:

- Has a unique **ID** within the Asset.
- Is defined by a **root node** (blue in Maya) and its internal hierarchy.
- Ends where another template begins.

All construction data is stored on the root node (ID, options, etc.). Child nodes under the template root are considered part of it unless explicitly hidden using a name starting with `_`.

> ⚠️ Nodes with `_` are treated as **helper** and ignored during parsing.

The root node is critical:

- It defines the existence of the template.
- It stores all configuration attributes (prefixed with `gem_`).

> 🔹 The `gem_` prefix originates from the original engine name "Gemini" and is preserved for compatibility.

Template modules can be easily identified by their blue color in Maya’s Outliner.

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

### Output (Hooks)

When the rig is built, each joint in a template receives a corresponding **rig node**, referred to as a **hook**.

- These hooks determine how template hierarchies are connected.
- A child template’s rig will be parented under its parent’s hook.

### Branches & Symmetry

The `branches` option allows duplicating a template multiple times with the same configuration. Commonly used for **symmetry**:

- Each branch is identified by a **suffix** (e.g., `L`, `R`).
- Branches are created temporarily during build and destroyed unless retained for asymmetry.
- Symmetry is automatically recognized for suffix pairs: `L/R`, `up/dn`, `ft/bk`.

Mirrored branches automatically invert axes and apply the necessary flipping to joints and controllers.

### Branch Visualization & Editing

- Use the context menu option **"Build template branches"** to generate temporary branches.
- These branches are visible in the Outliner and show where the rig will be constructed.
- These preview branches are destroyed on the next build unless explicitly frozen.
- To freeze branch placement, switch branches to edit mode using **"Set branch edit"** in the context menu.

## Helper Nodes

Any node whose name begins with an underscore (`_`) is considered a **Helper Node**. These nodes are skipped by the template parser, meaning they are not treated as part of a module’s hierarchy — but they can still be used to carry controller shapes, modifiers or deformers.

If a node is named with double underscores (e.g. `__meta__`), it becomes an **Internal Helper Node**, completely hidden from the interface.

## Scheduler & Build Process

Mikan scans the full hierarchy and plans construction in logical order.

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

Each mode signals the build system how to act — whether for dev, debug, or production.

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

⚠️ These modes should be defined and managed by **production** to match the studio’s specific delivery workflows.

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
