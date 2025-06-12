+++
title = 'Setting Up A Flask App With A Virtual Environment'
date  = 2024-11-05T10:39:00+03:00
tags  = ["python", "flask", "virtual environment"]
draft = false
+++

Flask is a lightweight and powerful web framework for Python, making it a popular choice for web development. Virtual environments are crucial for Python projects because they allow you to manage dependencies separately for each project, avoiding conflicts. In this post, I'll walk you through setting up a Flask app with a virtual environment from scratch.

---

### **1. Install Python**

Before diving in, ensure that Python is installed on your system. You can verify this by opening your terminal or command prompt and running the following command:

```bash
python --version
```

Or, depending on your system:

```bash
python3 --version
```

If Python is not installed, head over to the [official Python website](https://www.python.org/downloads/) and download the latest version.

---

### **2. Set Up a Project Directory**

Next, let's create a directory for your Flask project. This directory will hold all your project files.

```bash
mkdir my_flask_app
cd my_flask_app
```

Here, `my_flask_app` is the name of your project directory. Feel free to name it anything you like.

---

### **3. Create a Virtual Environment**

You can create a virtual environment either through the terminal or VSCode. When created through VSCode, the environment can automatically activate for the project, making it convenient to switch without manual activation.

### [**Using the Create Environment command](https://code.visualstudio.com/docs/python/environments#_using-the-create-environment-command)** In VSCode

Open the Command Palette ( ⇧⌘P ), search for the **Python: Create Environment** command, and select it.

![screenshot_01](../screenshot_01.webp)

Select Venv,

![screenshot_02](../screenshot_02.webp)

And you will be presented a list of interpreters that can be used as a base for the new virtual environment.

![screenshot_03](../screenshot_03.webp)

### [In the Terminal](https://code.visualstudio.com/docs/python/environments#_create-a-virtual-environment-in-the-terminal)

To create a virtual environment inside your project directory, run the following command:

- **On Windows:**

    ```bash
    python -m venv .venv
    ```

- **On macOS/Linux:**

    ```bash
    python3 -m venv .venv
    ```


This command creates a new directory named `venv` inside your project directory, which will contain the virtual environment.

To start using your virtual environment, you need to activate it. The activation command differs depending on your operating system:

- **On Windows:**

    ```bash
    .venv\Scripts\activate
    ```

- **On macOS/Linux:**

    ```bash
    source .venv/bin/activate
    ```


Once activated, you'll notice that your terminal prompt is prefixed with `(venv)`, indicating that your virtual environment is active.

![screenshot_04](../screenshot_04.webp)

Also, you will see the selected interpreted version on the right side of the Status Bar.

![screenshot_05](../screenshot_05.webp)

---

### **5. Install Flask**

With your virtual environment activated, the next step is to install Flask. This can be done easily using `pip`, Python’s package manager:

```bash
pip install Flask
```

This will install Flask and its dependencies in your virtual environment.

---

### **6. Create a Simple Flask App**

Now that Flask is installed, let's create a simple Flask application. In your project directory, create a new file named `app.py` and add the following code:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, Flask!"

if __name__ == '__main__':
    app.run(debug=True)
```

This basic Flask app displays "Hello, Flask!" when you visit the home page (`/`).

---

### **7. Run the Flask App**

To see your Flask app in action, run the following command in your terminal:

```bash
python app.py
```

*Note: If your app file isn’t named `app.py`, set the environment variable `FLASK_APP=<filename>` before running `flask run`.*

Alternatively, you can use the Flask CLI:

```bash
flask run
```

Your Flask app should now be running locally on `http://127.0.0.1:5000/`. Open this URL in your web browser, and you should see the "Hello, Flask!" message.

---

### **8. Deactivate the Virtual Environment**

If you’re finished working in this environment, you can deactivate it by running:

```bash
deactivate
```

This will return your terminal to its normal state, where the virtual environment is no longer active.

---

### **9. Save Your Dependencies**

It's a good practice to save your project's dependencies in a `requirements.txt` file. This file allows you to easily recreate the same environment later or share it with others. To generate this file, run:

```bash
pip freeze > requirements.txt
```

Later, you or anyone else can install these dependencies in a new environment using:

```bash
pip install -r requirements.txt
```

---

{{< adsense >}}

### Conclusion

Setting up a Flask app with a virtual environment is a straightforward process that ensures your project remains clean and manageable. By following these steps, you can easily create isolated environments for your Flask projects, helping you avoid conflicts and manage dependencies efficiently.

Now that you have a basic Flask app running, you're ready to start building more complex applications. Happy coding!

{{< buymeacoffee-widget >}}