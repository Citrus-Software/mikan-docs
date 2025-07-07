---
---

# Installation

## Quick Install

> 🧪 This is the fastest way to get Mikan up and running for everyday use.  
> If you're working in a studio environment and need a more custom setup (e.g. network/server deployment, virtual environments, etc.),  
> please refer to [Full Setup Guide](#full-setup-guide) for detailed instructions.

### 🚀 One-Minute Setup (Windows + Maya)

1. **Close Maya** if it's currently open.
2. **Extract** the contents of `mikan-main.zip` anywhere you like.  
   _Example: extract to `C:\` so that you get `C:\mikan-main`._
3. **Open Command Prompt** and go to your Maya installation folder:  
   _Example:_
   ```bash
   cd "C:\Program Files\Autodesk\Maya2022\bin"
   ```
4. **Install Mikan's Maya dependencies** using `mayapy`:
   ```bash
   mayapy -m pip install -r C:\mikan-main\requirements-maya.txt
   ```
5. **Launch Maya**.
6. Pick any **shelf tab** where you'd like to add the Mikan button.
7. Open the **Script Editor**, select the **Python** tab, and paste this code:
   ```python
   import sys
   sys.path.insert(0, r'C:\mikan-main\src')
   from mikan.maya.ui import MikanUI
   MikanUI.start()
   ```
8. In the Script Editor, go to **File → Save Script to Shelf...**
9. When prompted, name the shelf button **Mikan**, then click **OK**.
10. You should now see a **Mikan** button in your chosen shelf tab — click it!
11. The Mikan UI will appear, docked on the right.

## Full Setup Guide

Clone the repo:

```bash
git clone https://gitlab.teamto.fr/teamto-technology/mikan.git
cd mikan
pip install -r requirements.txt
```

## Maya Integration

Make sure the project folder is in Maya's PYTHONPATH. You can do this by editing your user userSetup.py or setting the environment variable.

## Maya Requirements

Installing Python packages for Maya can be tricky depending on your setup.

There are two main approaches:

### 1. Install requirements into Maya's built-in Python

You can try installing the required packages directly into Maya's `site-packages`, using Maya's bundled Python. This can be done via the command line (adjust path to your version):

```cmd
"C:\Program Files\Autodesk\Maya2024\bin\mayapy.exe" -m pip install -r requirements-maya.txt
```

⚠️ Be careful: this modifies Maya’s internal Python environment and may conflict with other tools.

### 2. Use a custom Python environment via `PYTHONPATH`

Alternatively, install the requirements in a separate Python environment and expose the installed packages to Maya via `PYTHONPATH`.

```cmd
:: Create a virtual environment somewhere
python -m venv C:\maya_env
C:\maya_env\Scripts\activate
pip install -r requirements-maya.txt
deactivate

:: Add the site-packages folder to PYTHONPATH (e.g. in userSetup.py or system environment variables)
set PYTHONPATH=%PYTHONPATH%;C:\maya_env\Lib\site-packages
```

This approach is safer and more flexible, especially if you're managing multiple tools.

📝 Note:

- Adjust paths based on your system setup.
- Make sure the Python version in your virtual environment matches the one used by Maya (e.g. 3.9 or 3.10).
