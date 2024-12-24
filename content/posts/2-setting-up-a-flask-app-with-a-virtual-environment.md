+++
title = 'Setting Up A Flask App With A Virtual Environment'
date = 2024-11-04T07:07:07+01:00
draft = true
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

![Screenshot 2024-11-05 at 19.05.39.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9700ee2-6d01-41ea-b9eb-0022c43ac751/d9bd1818-9821-498a-b950-02bcf726a102/Screenshot_2024-11-05_at_19.05.39.png)

Select Venv,

![create_environment_dropdown.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9700ee2-6d01-41ea-b9eb-0022c43ac751/37859485-492d-4477-8e69-ef2304dc6e8a/create_environment_dropdown.png)

And you will be presented a list of interpreters that can be used as a base for the new virtual environment. 

![Screenshot 2024-11-05 at 19.07.51.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9700ee2-6d01-41ea-b9eb-0022c43ac751/33535cbf-fdfa-4e99-beda-d9e71c86da86/Screenshot_2024-11-05_at_19.07.51.png)

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

![Screenshot 2024-11-05 at 19.10.26.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9700ee2-6d01-41ea-b9eb-0022c43ac751/72979e29-d128-4370-ada9-3915974985a4/Screenshot_2024-11-05_at_19.10.26.png)

Also, you will see the selected interpreted version on the right side of the Status Bar.

![Screenshot 2024-11-05 at 19.28.58.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9700ee2-6d01-41ea-b9eb-0022c43ac751/082f6876-322f-4107-9af5-d039d730414e/Screenshot_2024-11-05_at_19.28.58.png)

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

### Conclusion

Setting up a Flask app with a virtual environment is a straightforward process that ensures your project remains clean and manageable. By following these steps, you can easily create isolated environments for your Flask projects, helping you avoid conflicts and manage dependencies efficiently.

Now that you have a basic Flask app running, you're ready to start building more complex applications. Happy coding!