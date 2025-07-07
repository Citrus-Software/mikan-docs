---
sidebar_label: About Mikan
sidebar_position: 1
---

# Mikan Documentation

Welcome to the Mikan documentation. This project aims to provide a flexible Python framework for rigging in both Maya and Tangerine.

## What is Mikan

**Mikan** is a rigging toolkit developed for **Autodesk Maya** and **Tangerine** — TeamTO Technology’s high-performance animation platform. It is designed to author rig blueprints that are software-agnostic and production-ready, enabling the creation of complete rigs from start to finish: from guide placement to advanced deformation graphs.

Born from years of production experience, Mikan aims to combine **performance** with **ergonomics**. It generates flexible, customizable rigs while ensuring consistency and reproducibility across projects.

### Key Concepts

- **Cross-Platform Blueprinting**  
  Mikan builds a single, unified **blueprint** — or rig template — that drives rig generation both in Maya and Tangerine.

- **High-Performance Autorig**  
  Mikan provides a performant and user-friendly autorig solution, capable of generating versatile and studio-grade rigs with minimal friction.

- **Round-Trip Workflow**  
  Mikan promotes a continuous **template → rig → template** feedback loop. Every modification — adding controllers, tweaking skin weights, repositioning elements — is performed directly on the blueprint. Rebuilding the rig applies and syncs these changes across both Maya and Tangerine.

- **Pipeline-Ready by Design**  
  Mikan enables the industrialization of rigging: consistent, high-quality rigs that are ready for delivery to external teams and partners.

- **Format Neutrality**  
  The blueprint can be exported as **Alembic**, facilitating interoperability with other DCC tools.

### Target Audience & Prerequisites

- **Target Audience** Studios, clients, and external partners looking to adopt **Tangerine** for animation, and in need of advanced, production-ready rigs optimized for this platform.

- **Prerequisites** Basic knowledge of rigging in **Maya** is expected (joints, constraints, hierarchies). No prior experience with Mikan or Tangerine is required.

## Blueprint Concept

A **Mikan blueprint** is a high-level, software-agnostic description of a rig. It defines both the structure and logic of the rig in a way that can be consistently interpreted and rebuilt across different platforms. In Maya, this blueprint is represented using standard DAG nodes, following conventions that make it both readable and interoperable with formats
like Alembic or USD.

This structure serves as the central reference throughout the rigging process. It enables rigs to be parsed, rebuilt, and synchronized reliably between Maya and Tangerine, making it a solid foundation for iteration, collaboration, and deployment.

Mikan interprets this structure through a set of **conceptual object types**, inferred from metadata and naming conventions. These types assign meaning to raw scene nodes, enabling advanced features such as rebuildability, modular design, and cross-DCC compatibility.

Blueprints can be authored visually in Maya, via Mikan’s UI, or programmatically through its API. Their simplicity also makes them easy to compose: for example, using Maya references, teams can assemble rigs from modular components like facial systems or full skeletal structures.

Understanding this hierarchy and its logic is the foundation for working effectively with Mikan.

| **Type**   | **Role in the Rig**                                                          | **Notes**                                                                                |
| ---------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `Asset`    | Top-level container for a rig blueprint and its generated rig.               | Multiple assets can exist in a scene. Each one acts as an isolated rig root.             |
| `Template` | Main building blocks of the rig: define skeleton guides and pivot hierarchy. | Represented by transforms and joints. Added hierarchically. Blueprint logic starts here. |
| `Graph`    | Nodes embedded in the hierarchy carrying metadata or logical groupings.      | Used to define spaces, behaviors, or structure without affecting the guide layout.       |
| `Shape`    | Controller shapes parented under `Template` nodes.                           | Ignored by the build process but used to define visual handles.                          |
| `Modifier` | Logic metadata: defines custom rigging behaviors, constraints, plugs, etc.   | Acts like a programmable customization layer.                                            |
| `Deformer` | Binding metadata: defines how geometry is connected to the skeleton.         | Executed after modifiers by default. Covers skinClusters, wraps, lattices, etc.          |

## Workflow

Creating a rig with Mikan follows an iterative authoring loop. The blueprint remains the central structure, updated and reused across every build.

1. **Blueprint Assembly**

   - Define the rig’s structure using conceptual elements: `Template`, `Modifier`, and `Deformer` objects.
   - This blueprint encodes hierarchy and logic, and serves as the foundation for all rig builds.

2. **Rig Build**

   - Mikan generates a functional rig from the current state of the blueprint.
   - This includes controller creation, constraints, and deformers — everything needed for a working setup.

3. **Binding and Manual Adjustments**

   - Deformation work (e.g. skinning, corrective shapes, wrap deformers) is performed directly on the built rig.
   - These operations are usually manual and geometry-specific.

4. **Injection and Iteration**
   - All manual changes (skin weights, added deformers, modifiers, etc.) are reinjected into the blueprint.
   - From here, the workflow loops back flexibly to either:
     - **Step 1** to continue assembling or refining the blueprint structure, or
     - **Step 2** to rebuild the rig incorporating the latest changes.
   - This iterative cycle enables continuous improvement of the rig, balancing structural edits with deformation adjustments without losing any work.

This workflow makes it easy to iterate, collaborate, and ensure consistency across all rigs in a production.

## Pipeline Integration

Once a blueprint is complete, Mikan offers flexible options for integrating it into production pipelines. The blueprint becomes a deployable artifact that can be exported, processed, and adapted to meet the needs of various departments — whether in Maya or Tangerine.

To target Tangerine, the blueprint is exported as an Alembic file and processed using Mikan's API to generate the final animation-ready rig. This process can be customized to include updated modeling, versioned assets, and other pipeline-specific logic.

The same approach can be applied to Maya scenes, depending on the project's structure. It's up to pipeline teams to define how these deliverables are generated, versioned, and distributed.

Mikan’s API is intentionally lightweight and modular, making it easy to embed into studio-specific tools or batch scripts. The system is designed to be extensible, encouraging studios to build their own wrappers around it if needed.

In addition, Mikan supports **rig variations** within a single blueprint — enabling multiple rig builds (e.g. for layout, animation, FX, etc.) to be generated from the same source file. These variations are fully customizable and can be tailored to match production workflows.
