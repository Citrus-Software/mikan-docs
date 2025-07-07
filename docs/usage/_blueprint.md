# Template Blueprints

## Hierarchy

Mikan’s primary function is to manage rig construction templates. These are represented as a hierarchy of nodes in Maya, using specific node types defined by Mikan.

The root node type of a rig is called an **Asset**. It holds both the rig template and the generated rig. Multiple assets can coexist in the same scene, and Mikan is capable of building multiple rigs simultaneously.

The Asset node is responsible for organizing and scheduling the rig build process. All build commands are parented to it. The goal is to enable exporting the rig construction template into an abstract form using a 3D file format that supports node hierarchies (at TeamTO, Alembic is used for this purpose).

## Components

A Mikan template consists of several types of elements: templates, neutral nodes, modifiers, and deformers. The Mikan interface simplifies the hierarchy display to only show these elements, making it easier to inspect and edit.

**Template modules** are the building blocks of the hierarchy used to define the rig’s initial skeleton. Each template is composed of a hierarchy of joints and/or transforms with a single root. This root defines the unique characteristics of each template. In Maya’s Outliner, they are easily identifiable by their blue color (matching the Mikan UI), and they store their settings in attributes prefixed with `gem_`.

> ⚠️ Note: The `gem_` prefix comes from the original name of the rigging engine, Gemini. Although the project has since been renamed to Mikan, the original naming convention was kept for compatibility and clarity.

All transform or joint nodes parented under this root are considered part of that template. The hierarchy of one template ends where another begins. Node names are not significant.

Additional structure can exist in the hierarchy as long as it remains hidden from templates. Any node with a name starting with an underscore (`_`) is considered **neutral** and ignored by the template parser. This is, for example, how autorig scripts parent controller curve shapes without making them visible to the parser. Neutral nodes can also host any type of helper objects for easier positioning or template tuning.

Neutral nodes **are displayed** in the interface hierarchy. To completely hide a neutral node from the interface, name it with double underscores (e.g., `__helpers__`).

## Scheduler

To generate a rig, Mikan scans the entire hierarchy and plans the construction steps in the correct order. For successful execution, this hierarchy must follow a few rules.

1. Mikan builds all template hierarchies **top-down**.
2. Once the rig structure is built, all **modifiers** and **deformers** are listed.
3. Their execution is scheduled according to the following logic:

### Execution Rules

- **Modifiers run before deformers** by default.
- Execution order follows **node hierarchy** (from top to bottom).
- **Sibling order is ignored** — Maya supports it, but formats like Alembic do not, so don’t rely on it.
- **Command priorities**, if defined, are respected.
- Commands that fail due to unresolved IDs are **retried at the end**.
- **Build mode** and **node activation state** are taken into account.

> See the sections on [Modifiers](modifiers.md) and [Deformers](deformers.md) for details.

## Build Modes

Mikan uses a **flexible, flag-based system** to control how rigs are constructed. Instead of a single build mode, multiple modes can be **enabled simultaneously**, allowing for more modular and context-aware builds.

Each mode acts as a **signal** that informs the build process how to behave — whether it's for development, debugging, production delivery, or specific host environments.

### Mode Categories

Build modes are broadly grouped into three categories:

#### 1. Development Flags

These are internal flags used to customize the rig construction behavior during work and debugging:

- **`dev`**: When enabled, the build skips finalization and cleanup steps meant for delivery, allowing faster iteration and easier rig inspection.
- **`debug`**: Forces the build process to stop immediately on the first error. Useful for troubleshooting and investigating failures during early stages.

These modes are typically **used together** during production rigging work.

#### 2. Production Targets

These modes indicate **delivery contexts** and help define what parts of the rig should be built for a specific purpose. They are customizable and pipeline-dependent. Common examples include:

- **`anim`**: For animation-ready builds
- **`layout`**: For layout or previs needs
- **`render`**: For export or rendering purposes

These modes help control visibility, performance, or structure depending on the context. For example, an `anim` rig might include deformation controllers, while a `layout` rig might not.

⚠️ These modes should be defined and managed by **production** to match the studio’s specific delivery workflows.

#### 3. Host-Specific Flags

These are internal flags automatically enabled depending on the environment where the build is launched:

- **`maya`**: Active when the build runs inside Maya
- **`tangerine`**: Active for builds executed through the **Tangerine** framework

They can be used for fine-tuned adjustments specific to the host application.

---

### How to Use Modes

Modes can be added to any element of the rig template. Each element supports:

- A list of **required modes** (the element is built only if **all** are active)
- A list of **excluded modes** (the element is skipped if **any** are active)
- A **disable** flag (to skip the element regardless of modes)

These settings can be configured in the **Edit** tab of each element.

#### Syntax

- Multiple modes: `anim, render`
- Exclusion: `~debug, ~dev`
- Disable: check the **Disable** box

Child nodes inherit the mode settings from their parent unless overridden.

---

### Example: Bind Pose Separation

A common use case is separating the **technical bind pose** from the **final animation pose**. For example, a character might be delivered with a straight tail to simplify deformation setup. To match the artistic pose, a **modifier** applies driven keys after the bind.

By conditioning this modifier to run **only in production modes** (e.g., `anim`, `render`) and excluding `dev` and `debug`, you can avoid triggering it during development, making rigging tasks faster and less error-prone.
