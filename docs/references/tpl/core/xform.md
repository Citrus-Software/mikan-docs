# core.xform

> Locator creation utility module

## Overview

The `core.xform` module creates transform locators based on a template chain. These locators can serve various purposes such as helper objects, rigging markers, or secondary constraints. The module is lightweight and configurable.

## Options

- `joint` (*bool*, default: `off`): If enabled, creates a joint instead of a transform group.
- `rotate_order` (*enum*, default: `xyz`): Rotation order applied to the locator.
- `flip_orient` (*bool*, default: `off`): Flips the root orientation to produce mirrored translation behavior.
- `copy_orient` (*bool*, default: `on`): Matches the locator orientation to the template joint.

### Shape Options

- `copy_shapes` (*bool*, default: `off`): Copies visual shapes from the template onto the locator.
- `locator` (*bool*, default: `off`): Adds a basic locator shape to the transform.
