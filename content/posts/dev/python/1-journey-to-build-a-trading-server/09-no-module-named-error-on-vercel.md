+++
title = 'Solving the "No Module Named" Error on Vercel'
date  = 2024-12-12T05:03:00+03:00
tags  = ["python", "vercel", "deployment", "no module named", "error", "debugging"]
draft = false
+++

In this post, I’ll walk you through an issue I faced after restructuring my Python project and deploying it to Vercel. What seemed like a straightforward deployment turned into a frustrating hunt for a missing module. After two days of debugging and experimenting, I finally solved the issue—and here’s how you can too.

---

### The Setup: Restructuring the Project and Adding `setup.py`

I started by reorganizing the project folder structure to keep things clean and manageable. I moved files into appropriate directories and adjusted the import statements accordingly. Here’s how the new folder structure looked:

```bash
/project-root
    /src
        /api
            alpaca_api.py
        app.py
    setup.py
```

The new imports in `app.py` reflected this change:

```python
from api.alpaca_api import AlpacaApi
```

After making these changes, I added a `setup.py` file to ensure everything would work smoothly. The `setup.py` makes it easier to install the project as a package locally or in the deployment environment, which is useful when you’re moving files around.

Here’s a basic version of the `setup.py` I used:

```python
from setuptools import setup, find_packages

setup(
    name="my_project",
    version="0.1",
    packages=find_packages(where="src"),
    package_dir={"": "src"},
)
```

This allowed me to install the project locally using:

```bash
pip install -e .
```

With this setup, everything worked perfectly on my local machine. The tests passed, and the app ran without issues. Confident in my changes, I pushed the project to Vercel—only to encounter an unexpected error.

---

### The Error: "No Module Named 'api'"

After deploying the project to Vercel, I ran into this error in the logs:

```vbnet
ModuleNotFoundError: No module named 'api'
```

Vercel couldn’t find the `api` module, even though it was clearly present in the `src` directory. The same code worked fine locally, so I was stumped as to why the cloud deployment was failing.

---

### Reproducing the Issue with `vercel dev`

While searching for an solution I came across `vercel dev` CLI command in the documentation. It  simply mimics the Vercel cloud environment on your machine allowing you test your Vercel Project before deploying. You can check further in the [Vercel documentation](https://vercel.com/docs/cli/dev).

By running:

```bash
vercel dev
```

I was able to reproduce the exact same error locally:

```vbnet
ModuleNotFoundError: No module named 'api'
```

This confirmed that the issue wasn’t specific to the Vercel cloud environment—it was related to how Vercel was interpreting my project structure and imports.

---

### The Solution: Adding `PYTHONPATH` to `vercel.json`

The root of the problem was that Vercel didn’t know where to look for the `api` module inside the `src` directory. Although my imports were correct, Vercel needed some extra help finding the right path.

The fix was to add the following environment variable to my `vercel.json` configuration file:

```json
{
  "env": {
    "PYTHONPATH": "src"
  }
}
```

This configuration tells Vercel to include the `src` directory in the Python path during execution. Once I added this and redeployed the app, the import errors were gone, and everything worked as expected.

---

### Why `setup.py` Was Necessary ?

After restructuring my project, I added a `setup.py` file to turn my code into a proper Python package. This setup ensures that the project and its dependencies can be installed locally in "editable mode" with `pip install -e .`. This mode is particularly useful because it lets Python directly reference the code from its current location on your filesystem rather than copying it to the site-packages directory. As a result, any changes you make to the code are immediately reflected when you run the project or tests, avoiding the need to reinstall or manually update imports. Without a `setup.py` file, organizing imports across directories becomes cumbersome, and maintaining consistency between local development and cloud deployment environments is significantly harder.

---

### Lessons Learned

1. **Use `vercel dev` for Local Testing:** This command saves time by reproducing Vercel’s cloud environment locally, making debugging much easier.
2. **Adding `PYTHONPATH` for Module Resolution:** If you encounter import issues after restructuring a Python project, don’t forget to update your Python path in the deployment environment.
3. **`setup.py` Ensures Portability:** Adding a `setup.py` file allows you to package your project properly and run it consistently both locally and on deployment platforms.
4. **Platform-Specific Configurations Matter:** What works on your local machine might not work in the cloud. Be mindful of the subtle differences between environments, especially around imports and paths.

---

{{< adsense >}}

### Conclusion

If you’re facing similar import issues on Vercel, try using `vercel dev` to debug locally and consider setting the `PYTHONPATH` in your `vercel.json`. This small change saved me hours of frustration and allowed my project to run smoothly both locally and on Vercel.

---

This was a challenging but rewarding journey, and I hope this post helps others who encounter the same problem!

{{< buymeacoffee-widget >}}