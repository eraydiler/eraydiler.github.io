+++
title = 'Installing Your Python Project in Editable Mode with a Virtual Environment'
date  = 2024-11-11T07:21:00+03:00
tags  = ["python", "virtual environment", "editable mode", "setup.py", "project setup"]
draft = false
+++

When working on Python projects, managing dependencies and setting up the project structure right from the start saves a lot of trouble—especially with import errors. One of the best ways to handle this is by installing your project in *editable mode* within a virtual environment. This way, you avoid hardcoded paths and make development simpler.

In this post, I’ll show you how to set up your project for editable installation and why this approach is useful.

## Why Install in Editable Mode?

Editable mode makes your project available to the virtual environment without needing to reinstall it every time you change the code. This setup lets you see the latest changes reflected in the environment automatically, which is helpful in larger projects where code might be organized across multiple folders.

Another reason is to avoid common import issues. I had to use this approach after changing my project’s folder structure, which led to errors like `no module found <name-of-the-imported-module>`. Adding a `setup.py` file and installing in editable mode solved this by making imports consistent and manageable.

Let’s get started!

## Step 1: Create a `setup.py` File

To enable editable installation, you first need a `setup.py` file in the project’s root folder. Here’s a simple example:

```python
from setuptools import setup, find_packages

setup(
    name='your_project',
    version='0.1',
    packages=find_packages(where='src'),
    package_dir={'': 'src'},
)

```

### What This File Does

- **`name`**: Project name
- **`version`**: Project version
- **`packages`**: Uses `find_packages()` to locate packages inside the `src` folder.
- **`package_dir`**: Tells Python to look for packages in `src`.

### Example Folder Structure

Using this `setup.py` file works well if your project follows a structure like this:

```python
project_root/
│
├── src/
│   ├── api/
│   ├── broker/
│   └── ...
├── setup.py
└── requirements.txt

```

This keeps code in `src/` and helps manage larger projects more easily.

## Step 2: Install the Project in Editable Mode

After creating `setup.py`, install your project in editable mode:

```bash
pip install -e .
```

### How Editable Mode Helps

The `-e` flag (for *editable*) installs the project in a way that allows you to see changes without reinstalling. So, you don’t have to worry about modifying paths or reinstalling packages each time you update your code.

## Step 3: Importing Made Simple

Once the project is in editable mode, importing modules is much easier. For example, if there’s a module in `src/api`, you can now import it directly:

```python
from api import some_module
```

Editable mode removes the need to set `PYTHONPATH` or use `sys.path` tweaks—the modules are accessible right after installation.

## Why Use Editable Mode on a Server?

Editable mode is helpful locally, but for production, it’s best to make sure everything works as expected. On a server, using `pip install` without the `-e` flag is usually recommended since it locks in the exact version of your code. However, having the `setup.py` file is still crucial for handling imports, especially if your project has a more complex structure. It’s also useful for tools like Docker, which install dependencies in a similar way.

---

{{< adsense >}}

With these steps, installing your Python project in editable mode should be straightforward, reducing import hassles and keeping everything organized. It’s a quick setup that makes your development process smoother!

{{< buymeacoffee-widget >}}