# Architecture Overview

This section provides a technical deep-dive into how Mikan bridges the gap between a DCC scene (Maya, Tangerine) and its Python framework. Understanding this architecture is essential for extending the framework or integrating it into a production pipeline.

## Core Design Philosophy

Mikan functions as a **stateless translation layer**. It does not maintain a permanent database of the rig; instead, it reads the current state of the scene graph (DAG) to instantiate Python objects on the fly.

The framework is organized into three distinct layers:

1. **Abstract Core**: Defines the logic independent of the software (Base logic, Math, Solvers).
2. **DCC Implementation**: Implements the abstract methods for specific software (Maya, Tangerine).
3. **Content Modules**: The actual library of rigging components (Arms, Legs, Spines) derived from the layers above.

### Package Structure

The codebase is structured to enforce this separation:

| Namespace             | Description                                                                                                                            
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `mikan.core.abstract` | Contains the base classes (Asset, Template, Mod, Deformer). These define the contract but contain no DCC-specific code.                |
| `mikan.[dcc].core`    | e.g., mikan.maya.core. Contains the concrete implementations. This is where calls to maya.cmds or tangerine.api happen                 |
| `mikan.templates`     | Contains the rig components themselves. Each component includes a generic configuration (YAML) and a DCC-specific implementation file. |

## The Object Model

Unlike some frameworks where every object inherits from a single `Node` class, Mikan uses distinct orchestrators for different aspects of the rig.

### Class Hierarchy

#### The `Asset` Class (The Orchestrator)

The `Asset` is the entry point of the API. It represents the root of a rig in the scene. It does not inherit from `Template`, but acts as the manager for the entire build process.

- **Role**: Scene parsing, validation, and build execution.
- **Identification**: Wraps a DAG transform node tagged with `gem_type="asset"`.
- **Key Method**: `asset.make()` triggers the rig build.

#### The Component Classes

The rig content is defined by three primary module types:

1. **Template**:
    - Representation: A hierarchy of DAG nodes (Guides/Joints).
    - Discovery: Identified via attributes on nodes (e.g., gem_type string).
    - Logic: Defines structure, joint placement, and control generation.
2. **Modifier (Mod)**:
    - Representation: "Code blocks" or metadata strings attached to a node.
    - Logic: Post-build script execution (custom constraints, math nodes).
3. **Deformer**:
    - Representation: Links a Geometry Transform to a Deformer logic node.
    - Logic: Handles binding (Skinning, Wraps, Muscles).

## Discovery & Instantiation

How does Mikan turn a Maya node into a Python object?

Mikan relies on a **"Cast on Demand"** approach. While you can manually instantiate a wrapper (e.g., `Template('tpl_arm')`), the `Asset` class typically handles discovery automatically.

### The Identification Protocol

Mikan scans the scene hierarchy looking for specific markers (attributes).

- `gem_type`: Defines the class type (e.g., `asset`, `template`).
- `gem_template`: Defines the template type (e.g., `world.character`, `arm.legacy`)
- `gem_id`: Defines the unique identifier/name within the rig.

```python
# Internal Logic Example (Pseudo-code)
node = mx.encode("asset")

if "gem_type" in node and node["gem_type"] == "asset":
    # Valid Asset Root found
    my_asset = mikan.maya.core.asset.Asset(node)
```

### Context & Data Flow

Mikan does not pass a single "Global Context" object wildly. Instead, it relies on strict hierarchy and ID tagging.

1. **Configuration (YAML)**: Static options for templates (e.g., "Default IK Stretch").
2. **Instance Data (Attributes)**: User overrides stored on the DAG nodes.
3. **Runtime Connectivity (The `Nodes` Class)**: During the build, modules tag generated nodes with specific IDs. The Nodes utility class acts as a directory service, allowing modules to find each other (e.g., an Arm finding the Spine's end joint) without hardcoded paths.

## The Build Lifecycle

Executing a build (`asset.make()`) follows a strict sequence to ensure dependencies are respected.

### 1. Parsing & Validation

The `Asset` instance scans its hierarchy. It creates python instances for every valid `Template`, `Mod`, and `Deformer` found in the DAG under the asset root.

### 2. Scheduling

Mikan calculates the dependency order. Since templates are hierarchical, parent templates (like a Spine) must be built before children templates (like an Arm).

### 3. Execution (The make loop)

The build engine iterates through the sorted modules:

- Pre-Build: Clean up previous rig nodes.
- Template Build: Generate joints, controls, and mechanisms.
- Modifier Execution: Apply custom logic and patches.
- Deformer Binding: Bind geometry to the new structure.
