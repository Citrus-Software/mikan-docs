# Exporting Blueprints to Tangerine

This guide documents the manual workflow for exporting Mikan assets from Autodesk Maya to Tangerine. This process is designed for **prototyping and testing**, acting as a bridge before fully automated pipeline integration.

### Prerequisites:

- Basic knowledge of the Mikan authoring tools in Maya.
- **Mikan Version**: Ensure you are running the latest version of Mikan (currently **v1.0.4** or higher). Tangerine automatically fetches the latest Mikan version upon installation, but it is best practice to keep your Maya environment up to date.

## 1. Data Transfer Overview
The transfer format between Maya and Tangerine is **Alembic (.abc)**. While Alembic does not natively distinguish between joints and transforms or preserve child order, the Mikan framework is designed to handle these limitations using metadata.

### Metadata Handling
Mikan stores all necessary rig fabrication data within the **Template attributes**:
- `notes`: Contains code blocks and script logic.
- `gem_*`: Attributes with this prefix define object typing, IDs, and other structural data.

## 2. Preparing the Asset in Maya
Before exporting, ensure your scene is organized correctly.

### The Rig Template
You must export the **Root** of the Mikan asset (default name: `asset`).

:::warning[Important]
The rig must be in its **Template state** (unbuilt). Do not export a built control rig. Tangerine requires the skeleton structure found in the `asset|template` group to reconstruct the logic.
:::

### Geometry
Include all relevant geometry groups in your export selection:

- **Main Geometry**: Typically named `geo` (though nomenclature is flexible).
- **Auxiliary Geometry**: Any groups containing geometry for Wires, FFDs, or Blendshapes.

### Shaders
Alembic does not support material definitions. Mikan uses a metadata bridge to reconstruct basic shading in Tangerine for visualization (Previz) purposes.

#### Supported Features:
- Lambert style shading (Diffuse Color / Diffuse Texture).
- Alpha channel.
- Basic animated parameters (restricted support).

**How to export Shader Data**: You must run a process that reads the shaders assigned to your geometry and injects a structure into a `gem_shaders` metadata attribute on the group.

**Option A**: **UI Method** 
1. Select geometry groups to process.
2. Navigate to **Tools > Export > Save Shaders Data**.

**Option B**: **Python Method**

You can execute the following code to inject the data programmatically:

```python
from mikan.maya.lib.shaders import export_shaders

# Injects data to the 'geo' group's gem_shaders attribute 
# and returns the data dict.
data = export_shaders("geo")
```

## 3. Creating the Alembic File
Once the asset is prepared and shader data is saved, you can proceed to export.

### Automated Export (Recommended)
Use the Mikan helper tool to ensure all settings are correct:

1. Select your asset root and geometry groups.
2. Go to **Tools > Export > Export Blueprint ABC**.

### Manual Export
If exporting manually via Maya's standard Alembic export, ensure the following settings are enabled:

- **Attributes**: You must explicitly include `notes` and `gem_*` in the attribute export list.
- **Geometry**: Verify settings for UVs and Visibility based on your specific asset needs.

## 4. Launching Tangerine
To enable the Mikan import tools within Tangerine, a specific environment variable (`MIKAN_MENU="on"`) is required. Mikan provides helper tools in Maya to streamline this.

1. Set Path: Go to **Tools > Export > Set Tangerine Path** and point to the Tangerine executable.
2. Launch: Go to **Tools > Export > Run Tangerine**.
:::note
This command launches Tangerine with the correct environment variables and passes the current path of your Mikan module. This is useful if you are developing with a custom version of Mikan; otherwise, Tangerine will use its pre-installed version.
:::

## 5. Building the Rig in Tangerine
Once Tangerine is running and the Python environment is initialized:

1. Locate the **Mikan** menu in the Tangerine interface.
2. Select **Build ABC**.
3. In the file browser, select the `.abc` file exported from Maya.

**The Build Process**: Tangerine will parse the Alembic file, reconstruct the rig logic (identical to the Maya build), assign shaders based on the metadata, and apply utility settings (e.g., locking geometry selection).

:::info[Troubleshooting]
If the build fails or behaves unexpectedly, check the Console Logs in Tangerine. Errors usually indicate either a fabrication issue in Maya or a bug in the Mikan framework that should be reported.
:::
