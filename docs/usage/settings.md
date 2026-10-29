---
id: settings-menu
title: Settings & Menu Editor
description: Configure Mikan preferences, debugging tools, and deploy custom team menus using YAML.
sidebar_position: 4
---

# Settings & Menu Editor

Mikan is designed to be highly modular and customizable. Configuration is split into two distinct areas: **User Preferences** (handled automatically by the UI) and **Production Configuration** (managed manually via a YAML file for team-wide pipeline deployment).

This page also covers the in-app **Settings Menu** and how to use the **Menu Editor** to deploy custom tools to your team.

## Configuration Files

Mikan stores settings in two different locations depending on their scope:

1. **User Preferences (`prefs.json`)**
   Stored locally in your user documents directory (e.g., `Documents/mikan/prefs.json`). This file contains UI-specific settings and local user preferences. It is entirely managed by the Mikan interface, so you never need to edit this file manually.

2. **Production Configuration (`mikan.yml`)**
   A project-specific YAML file placed directly in the Maya project's `rig` folder. This file is tailored for production and the authoring software (currently Maya and Tangerine). It defines custom asset build modes, shared menu paths for the team, automatic Python patches, and legacy behavior fallbacks.

### Writing a Production Configuration YAML

The production configuration file acts as a dictionary of pipeline settings. It is the central hub where Technical Directors (TDs) can share scripts, automate tasks during production, and enforce standardized rig builds.

Here is an example configuration:

```yaml
# Mikan Production Configuration File

pipeline:
  # Rename or define build modes to match your production's specific nomenclature
  steps:
    - layout
    - anim
    - sim

menu_paths:
  # Shared menu paths for Maya team tools
  Dev: \\dev_server\tools\rig\mikan_menu.yml
  Prod: \\prod_server\my_prod\maya\rig\menu.yml

tangerine_menu_paths:
  # Shared menu paths for Tangerine team tools
  Dev: \\dev_server\tools\rig\mikan_tangerine_menu.yml

build:
  # Execute Python patches automatically during the initialization phase of a build.
  # Useful for automating tasks or ensuring specific asset fixes are always applied.
  maya:
    init:
      - \\prod_server\my_prod\maya\rig\patch\maya_fix_asset.py
  tangerine:
    init:
      - \\prod_server\my_prod\maya\rig\patch\tangerine_fix_asset.py

# Legacy Settings
# Mikan allows blocking or overriding certain behaviors in case of destructive updates.
template:
  wheel.legacy:
    # Rename a legacy module
    name: wheel.default
  arm.legacy:
    # Override default options for a module
    opts:
      clavicle_auto: true
```

:::note About Legacy Settings
Certain modules may contain highly specific legacy settings that date back to before Mikan's open-source release. While some of these are currently undocumented, any new legacy behaviors introduced in future updates will be fully documented in the technical documentation of their respective modules.
:::

## The Settings Menu

The top Settings menu in the Mikan UI provides direct access to pipeline preferences, logging verbosity, UI enhancements, and remote debugging tools.

### General Settings

These built-in options help developers and riggers troubleshoot and monitor their environment directly within Maya:

* **Logger Options**
    * **Timed Code**: Toggles elapsed time profiling. When enabled, the logger will output the exact execution duration of each step during rig builds, which is incredibly useful for bottleneck analysis.
    * **Log Level (`DEBUG` / `INFO` / `SUCCESS`)**: Dynamically adjusts the verbosity of Mikan's logger.
* **UI Features**
    * **Syntax Highlighting**: Toggles syntax coloring and log level color-coding directly within the native Maya Script Editor.
* **Dev / Debugging**
    * Native hooks for IDE remote debuggers. With a single click, Maya opens port `3500` to allow attachment from **VS Code** (via `debugpy`) or **PyCharm** (via `pydevd_pycharm`).

### The Menu Editor

Mikan’s top menu is entirely data-driven and generated dynamically from YAML files. The **Menu Editor** allows you to add new dropdowns, tools, and actions to the menu bar without writing a single line of PyQt/PySide code.

#### Writing a Custom YAML Menu

Menus are constructed using simple key-value pairs. Mikan's parser automatically handles complex Qt features like sub-menus, separators, checkable toggles, and exclusive radio groups.

Here is an example of a custom pipeline menu:

```yml
# A simple clickable item running Python code
- name: Publish Rig
  icon: rocket
  py: |
    from my_pipeline import publish
    publish.run()
  help: Publishes the current rig to the asset manager

- separator: true

# A checkable item (Toggle)
- name: Enable Auto-Save
  toggle: |
    def check_state():
        from my_pipeline import prefs
        return prefs.auto_save_enabled
  py: |
    from my_pipeline import prefs
    prefs.auto_save_enabled = not prefs.auto_save_enabled

- separator: Display Modes

# Exclusive Radio Buttons using "group"
- name: High Res
  group: display_mode
  py: "import cmds; cmds.displaySmoothness(divisionsU=3, divisionsV=3)"

- name: Proxy
  group: display_mode
  py: "import cmds; cmds.displaySmoothness(divisionsU=0, divisionsV=0)"

- separator: Misc.

# Examples of advanced keys
- name: Documentation
  web: 'https://intranet/docs'
  icon: box
  help: 'Opens the local asset manager documentation in your browser'

- name: Run Custom Maya 2024+ Script
  version: 2024
  file: 'Z:/pipeline/scripts/new_feature.py'
```

#### Supported YAML Keys

| Key              | Description                                                                      |
|------------------|:---------------------------------------------------------------------------------|
| `menu`           | Creates a sub-menu. Can contain nested items or a path to another .yml file.     |
| `name`           | The label displayed in the menu.                                                 |
| `icon` / `color` | Name of the icon registered in `MikanUI.ICONS` and its tint color.               |
| `help`           | Tooltip text displayed when hovering over the menu item.                         |
| `separator`      | Set to `true` for a line, or a string (e.g., `Tools`) for a labeled header.      |
| `file`           | Absolute or relative path to an external `.py` or `.mel` script file to execute. |
| `web`            | URL string to open in the system's default web browser.                          |
| `py`             | Python code (string or block) executed when the item is clicked.                 |
| `mel`            | MEL script executed when the item is clicked.                                    |
| `toggle`         | Python function returning a boolean to define the initial checked state.         |
| `group`          | Assigns items to an exclusive QActionGroup (acts as radio buttons).              |
| `version`        | The minimum Maya version required for this item to appear (e.g., `2024`).        |
| `max_version`    | The maximum Maya version supported. The item is hidden in newer versions.        |

:::tip View the Source Examples
To see exactly how far you can push the YAML menu system, you can browse Mikan's own native menu files on GitHub. They are built using this exact same system:

- [tools.yml](https://github.com/Citrus-Software/mikan/blob/main/src/mikan/maya/ui/tools.yml)
- [prefs.yml](https://github.com/Citrus-Software/mikan/blob/main/src/mikan/maya/ui/prefs.yml)
- [help.yml](https://github.com/Citrus-Software/mikan/blob/main/src/mikan/maya/ui/help.yml)
  :::
