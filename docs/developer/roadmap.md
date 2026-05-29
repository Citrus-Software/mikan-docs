# Public Development Roadmap

This document outlines the long-term technical goals and major features planned for future releases of the **Mikan** procedural autorigging framework. Our main focus is building a robust, modular, and cross-software compatible rigging pipeline suited for feature-film quality animation.

:::note
This roadmap is public and will evolve naturally as development progresses. We welcome technical discussions, community issues, and feedback via our GitHub repository, or feel free to join our community on **Discord**.
:::

## Long-Term Objectives

### Cross-Software Authoring Ecosystem: Blender Integration

Mikan is designed around a blueprint architecture, allowing rigs to be built programmatically across multiple targets, which currently include Autodesk Maya and our internal animation tool, Tangerine. Right now, rig authoring and blueprint creation are decoupled: Maya acts as our sole authoring environment, while Tangerine focuses strictly on animation playback and execution.

A major goal is to bring blueprint authoring capabilities to **Blender**. While Maya has a long-established ecosystem of specialized rigging tools, Blender is a massive and rapidly growing platform with a clear need for production-grade autorigging frameworks. Adding Blender authoring support will allow technical directors to design, edit, and export
framework blueprints entirely within an open-source toolset.

#### Key Focus Areas:

* Porting the Mikan blueprint authoring interface and data serialization layers to Blender's Python API (`bpy`).
* Achieving parity for guide systems, modular component configuration, and blueprint validation between Maya and Blender.
* Ensuring reliable cross-software build execution targeting host DCCs and Tangerine.

### Radial Basis Function (RBF) & Pose Space Deformers

To get realistic anatomical and skeletal deformations, we need a native sculpt-driven pose correction workflow. We plan to develop a high-performance RBF interpolation node built straight into the framework.

While DCC-native solutions exist (like Maya's Pose Editor, mGear's RBF Manager, Brave Rabbit's Weight Driver, or Unreal's Pose Wrangler), Mikan needs a lightweight, optimized, framework-native solution to ensure identical evaluation across all our pipeline targets.

#### Key Focus Areas:

* Development of a robust RBF interpolation node.
* Clear user interfaces for pose-space sculpting and management.
* Maintaining evaluation consistency across different platforms.

### Procedural Weightmaps & Shading Drivers

We want to expand our rigging options by standardizing framework-driven, procedural weightmaps. The primary use case is creating an automated **Tension Map** system to drive dynamic surface details like micro-wrinkles or skin compression.

By calculating vertex-level compression and extension data directly within the rig graph, we can generate dynamic vertex colors or driver attributes that feed directly into shading networks and displacement layers.

#### Key Focus Areas:

* Procedural mesh-tension evaluation nodes (compression and stretch).
* Streamlined integration with studio shader pipelines and render engine overrides.
* General-purpose procedural weight modulation tools for dynamic deformer blending.

### Native Dynamics & Secondary Motion (Rig-Based Physics)

Our current secondary animation tools rely on basic analytical jiggle behaviors on controllers, which doesn't support collisions or complex physical interactions. To handle secondary animation directly in the rig without relying on a downstream CFX department, we want to integrate real-time dynamics into the rig architecture.

We are looking into embedding a physics solver (like **Bullet Physics**) to expose simulation algorithms straight to the animation rig. This will let animators work with automated secondary motion (flesh jiggle, antennas, overlapping action) that respects collisions out of the box.

#### Key Focus Areas:

* Integrating dynamic solver configurations directly into the autorig build modules.
* Building a lightweight configuration UI tailored for rigging and animation workflows.
* Ensuring stable evaluation, loop handling, and animation-switch baking.

### Procedural Templates for Diverse Creatures

To avoid ad-hoc setups during production and make sure our assets are reusable, we plan to expand Mikan's procedural modules into dedicated creature systems. We want to design solid, highly configurable procedural templates for complex anatomy.

#### Key Focus Areas:

* **Avian Modules:** Multi-layered folding wing setups with automated feather fanning and cascading controls.
* **Serpentine Modules:** Procedural spine paths with wave propagation, rolling controls, and automated path-snapping.

### Bipedal Architecture Modernization

Our current biped template is stable and production-proven, but it needs a core architectural cleanup to match modern pipeline paradigms. This update will focus on underlying evaluation optimization, cleaner DAG hierarchies, and tighter integration with matrix-driven transforms.

#### Key Focus Areas:

* Refactoring core systems to maximize DG/Parallel Evaluation efficiency in the host DCC.
* Streamlining control hierarchies and modular limb switching (IK/FK/Spline).

### Next-Generation Facial Rigging Workflows

Facial rigging remains one of the most complex topics to tackle within automation frameworks. Every pipeline has its own philosophy, and implementations often turn into rigid "black boxes." Our goal with Mikan is to provide maximum flexibility and transparency. While Mikan is inherently powerful enough to solve these complex deformation issues, achieving
advanced results currently requires a deep, advanced knowledge of the tool's core mechanics.

To bridge this gap, we are focusing heavily on expanding our documentation, guides, and practical templates to make our facial workflows as open and accessible as possible.

#### Key Focus Areas:

* **Core Methodology Guides:** Documenting the foundational philosophy of handling facial rigs inside Mikan without losing control over raw setups.
* **Production Case Studies:** Providing a complete, step-by-step breakdown of how the facial rig for our studio showcase character was built.
* **Targeted Technical Tutorials:** Creating specialized, hands-on guides focused on blendshape library management, as well as building highly performant, skeletal-driven setups for advanced eyelid and lip mechanics.
