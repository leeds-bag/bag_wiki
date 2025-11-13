# Python Quick Start Guide

## Introduction
Python is a powerful, easy-to-learn programming language widely used in science, data analysis, web development, automation, and more. This guide will help beginners get started with Python, focusing on best practices for installation, environment management, and basic usage. For scientific and data work, using Conda (or Mamba) is highly recommended—see the [Conda Setup Guide](./conda_setup.md) for details.

## Opening a Terminal
To use Python and Conda/Mamba, you'll need to open a terminal (command line window) on your computer:

- **Windows:**
	- If you have Windows Subsystem for Linux (WSL) installed, open the Start menu, search for "WSL" or your chosen Linux distribution (e.g., Ubuntu), and click to open a Linux terminal.
	- Alternatively, use [MobaXterm](https://mobaxterm.mobatek.net/): install it, then open MobaXterm and start a local terminal or a WSL session.
- **Mac:**
	- Open the Terminal app (find it in Applications > Utilities, or search for "Terminal" in Spotlight).
- **Linux:**
	- Open your system's Terminal app (often found in the applications menu, or use Ctrl+Alt+T as a shortcut).

Once your terminal is open, you can follow the steps below to install Python, set up environments, and run code.

## Why Use Python?
- Simple, readable syntax
- Large ecosystem of libraries for every purpose
- Excellent for beginners and experts alike
- Strong community support

## Step 1: Install Python (Recommended: via Conda/Mamba)
While you can install Python directly from python.org, the best way for scientific work is to use Conda or Mamba. This lets you easily manage multiple Python versions and install packages without conflicts.

**See the [Conda Setup Guide](./conda_setup.md) for step-by-step instructions.**

## Step 2: Create a Python Environment
Environments keep your projects isolated, so packages for one project don’t interfere with another.

```bash
mamba create -n mypython python=3.11
conda activate mypython
```

## Step 3: Install Packages
Use Mamba (or Conda) to install packages from the conda-forge channel. This ensures compatibility and avoids dependency issues.

```bash
mamba install numpy pandas matplotlib
```

## Step 4: Write and Run Python Code
You can write Python code in a text editor (like VS Code) and run it from the terminal:

```bash
python myscript.py
```
Or use interactive notebooks (Jupyter) for data analysis:
```bash
mamba install jupyterlab
jupyter lab
```

## Step 5: Learn the Basics
Here are some key concepts for beginners:
- **Variables**: Store data (numbers, text, etc.)
- **Data types**: int, float, str, list, dict, etc.
- **Control flow**: if, for, while
- **Functions**: Reusable blocks of code
- **Modules**: Import libraries to extend Python’s capabilities

Example:
```python
# myscript.py
name = "World"
print(f"Hello, {name}!")
```

## Best Practices
- Always use environments for your projects
- Prefer conda-forge for package installs
- Keep your Python and packages up to date
- Use version control (e.g., git) for your code
- Read error messages—they help you debug!

## Useful Resources
- [Conda Setup Guide](./conda_setup.md)
- [Python Official Documentation](https://docs.python.org/3/)
- [Real Python Tutorials](https://realpython.com/)
- [Jupyter Project](https://jupyter.org/)

## Troubleshooting
- If you have issues with packages, check you’re using conda-forge and not mixing channels
- If Python isn’t found, make sure your environment is activated
- For help, search error messages or ask the community

---
Python is a great language for learning and doing real-world work. With Conda/Mamba and conda-forge, you’ll avoid most installation headaches and be ready to explore!
