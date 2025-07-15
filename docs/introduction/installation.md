---
---

# Installation

## Quick Install

> 🧪 This is the fastest way to get Mikan up and running for everyday use.  
> If you're working in a studio environment and need a more custom setup (e.g. network/server deployment, virtual environments, etc.),  
> please refer to [Full Setup Guide](#full-setup-guide) for detailed instructions.

### 🚀 One-Minute Setup (Windows + Maya)

1. **Close Maya** if it's currently open.
2. **Download** the [latest packaged ZIP](https://github.com/citrus-software/mikan/releases).
3. **Extract** the contents of `mikan-main.zip` anywhere you like.  
   _Example: extract to `C:\` so that you get `C:\mikan-main`._
4. **Open Command Prompt** and go to your Maya installation folder:  
   _Example:_
   ```bash
   cd "C:\Program Files\Autodesk\Maya2022\bin"
   ```
5. **Install Mikan's Maya dependencies** using `mayapy`:
   ```bash
   mayapy -m pip install -r C:\mikan-main\requirements.txt
   ```
6. **Launch Maya**.
7. Pick any **shelf tab** where you'd like to add the Mikan button.
8. Open the **Script Editor**, select the **Python** tab, and paste this code:
   ```python
   import sys
   sys.path.insert(0, r'C:\mikan-main\src')
   from mikan.maya.ui import MikanUI
   MikanUI.start()
   ```
9. In the Script Editor, go to **File → Save Script to Shelf...**
10. When prompted, name the shelf button **Mikan**, then click **OK**.
11. You should now see a **Mikan** button in your chosen shelf tab — click it!
12. The Mikan UI will appear, docked on the right.

## Full Setup Guide

### Obtain Mikan

#### **Option 1**: Download the [latest packaged ZIP](https://github.com/citrus-software/mikan/releases)

1. Download the latest release from the Releases page.
2. Extract the ZIP archive.

#### **Option 2**: Clone the repo:

```bash
git clone https://github.com/citrus-software/mikan.git
```

### Maya Integration

Make sure the project folder is in Maya's PYTHONPATH. You can do this by editing your user userSetup.py or setting the environment variable.

Installing Python packages for Maya can be tricky depending on your setup.

There are two main approaches:

#### **Option 1**: Install requirements into Maya's built-in Python

You can try installing the required packages directly into Maya's `site-packages`, using Maya's bundled Python. This can be done via the command line (adjust path to your version):

```cmd
"C:\Program Files\Autodesk\Maya2024\bin\mayapy.exe" -m pip install -r requirements.txt
```

:::warning
**Not recommended**: This modifies Maya’s internal Python environment, which can lead to conflicts with other tools or future updates.
:::

#### **Option 2**: Use a custom Python environment via `PYTHONPATH` ✅ (recommended)

Alternatively, install the requirements in a separate Python environment and expose the installed packages to Maya via `PYTHONPATH`.

```cmd
:: Create a virtual environment somewhere
python -m venv C:\maya_env
C:\maya_env\Scripts\activate
pip install -r requirements.txt
deactivate

:: Add the site-packages folder to PYTHONPATH (e.g. in userSetup.py or system environment variables)
set PYTHONPATH=%PYTHONPATH%;C:\maya_env\Lib\site-packages
```

This approach is safer and more flexible, especially if you're managing multiple tools.

:::note

- Adjust paths based on your system setup.
- You can share the same virtual environment across multiple tools or versions of Maya if compatible.

:::

#### Start Mikan

Open the **Script Editor**, select the **Python** tab, and paste this code:

```python
from mikan.maya.ui import MikanUI
MikanUI.start()
```

:::tip
For easier access, add this command to a **shelf button** in Maya.  
Use the icon at `src/mikan/core/ui/logo.svg` for a nice touch!
<img src="../img/logo.svg" alt="Mikan Logo" width="48" />
:::
