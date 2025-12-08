# world.character

> Global world / root module for animation-ready rigs

## Overview

`world.character` is the very first template that should be added when building a character rig intended for animation.
While it is not strictly required for the technical construction of the rig, it is mandatory for any asset that will be animated, as it provides the global hierarchy and top-level controls expected in the pipeline.

This module sets up:

- A consistent **global hierarchy** shared across all characters
- The standard **world / move** (root) control structure
- Default spaces used by all other modules (arms, legs, spine, etc.)
- A stable entry point for animation tools, exporters, and space-switch systems

All other modules (spine, limbs) are built on top of this structure.

## Template Structure

The *world.character* template is intentionally simple:

- The root joint defines the global position of the rig.
- No joint orientation is required—orientation is handled automatically.
- After the rig is built, all other modules are parented under the world hierarchy.

Despite its simplicity, this module is essential for maintaining compatibility across the entire animation pipeline.

## Options

- `fly_ctrl` (**bool**, default: `off`) : adds a FLY controller above the world hierarchy.
This controller behaves similarly to the Move (root) control but uses the character’s center of gravity as its pivot.
- `scale_ctrl` (**bool**, default: `off`) : adds a dedicated Scale controller for uniformly scaling the entire character.
The controller includes:
  - optional **squash**,
  - the **ability to change the scaling pivot** (ground level, COG, world origin…).