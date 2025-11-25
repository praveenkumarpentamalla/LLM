
---

### **Alternative Python Setup Guide: Using `venv`**

#### **1. Introduction & Goal**

*   **What is this for?** This is a backup plan if the primary installation method (using Anaconda) failed or was too complex.
*   **What are we setting up?** We are creating an isolated Python environment on your computer to run the course projects without interfering with other software.
*   **Tool Used:** `venv` (short for "virtual environment").
*   **Comparison to Anaconda:**
    *   **Pros:** Quicker, simpler, less heavy on system resources.
    *   **Cons:** Slightly less guaranteed compatibility across all systems.

---

#### **2. Step-by-Step Setup Instructions**

##### **Step 1: Install Python (PC Users Only)**
*   **For Mac Users:** Python comes pre-installed. You can skip this step.
*   **For PC Users:** You must install Python manually.
    1.  Go to the Python website: [https://www.python.org/downloads/](https://www.python.org/downloads/)
    2.  For best compatibility, download **Python 3.11.9**.
    3.  Run the Windows installer and follow the setup prompts.

##### **Step 2: Open Your Command Line Interface**
*   **On Mac:** Open the **Terminal** application.
*   **On PC:** Open **PowerShell** (just the regular one, not the Anaconda Prompt).

##### **Step 3: Navigate to Your Project Folder**
*   Use the command line to go into the directory (folder) where your course project is stored.
*   Example command (your path may differ):
    ```bash
    cd projects/LM-Engineering
    ```

##### **Step 4: Create the Virtual Environment**
*   Run the following command to create a new, empty environment named `venv`:
    ```bash
    python -m venv venv
    ```
*   **Explanation:**
    *   `python -m venv`: Tells Python to run the "venv" module to create a virtual environment.
    *   The second `venv`: This is the name of the folder that will be created. It's a common convention to name it `venv`.

##### **Step 5: Activate the Virtual Environment**
*   This command "turns on" the isolated environment, ensuring all subsequent Python commands use this space.

    *   **On Mac:**
        ```bash
        source venv/bin/activate
        ```
    *   **On PC:**
        ```bash
        venv\Scripts\activate
        ```
*   **How to know it worked?** You will see `(venv)` at the beginning of your command line prompt, indicating the environment is active.

##### **Step 6: Install Required Packages**
*   With the environment active, install all the necessary software libraries for the course.
*   Run this command:
    ```bash
    pip install -r requirements.txt
    ```
*   **Explanation:**
    *   `pip`: The Python package installer.
    *   `install -r`: Tells pip to install from a list in a file.
    *   `requirements.txt`: A file that contains the names of all the packages needed for the course.
*   **What's happening?** Pip will download and install packages like:
    *   **ChromaDB:** A vector database (for storing and searching data).
    *   **SQLAlchemy:** A tool for working with databases.
    *   **Tokenizers:** A library for processing text for language models.
*   **Note:** This may take a few minutes on a first-time setup as everything downloads.

##### **Step 7: Launch JupyterLab**
*   JupyterLab is our main workspace for the course. To start it, simply run:
    ```bash
    jupyterlab
    ```
*   This will automatically open a new window in your web browser where you can see all your project files and folders, including the `venv` folder.

---

#### **3. Key Concepts Explained**

*   **Virtual Environment (`venv`):**
    *   **Definition:** An isolated container on your computer that has its own specific version of Python and its own set of libraries (packages).
    *   **Simple Explanation:** Think of it like a dedicated workshop for a specific project. All the tools (Python packages) you need for this course are kept in this workshop. This prevents them from getting mixed up with tools for other projects on your computer, avoiding conflicts.
    *   **Real-World Example:** You might have one virtual environment for a web development project that uses Django and another for a data science project that uses Pandas and NumPy. They won't interfere with each other.

*   **`pip` and `requirements.txt`:**
    *   **`pip`:** The standard package manager for Python. It's like an "app store" for Python code libraries.
    *   **`requirements.txt`:** A simple text file that acts as a shopping list for `pip`. It tells `pip` exactly which packages (and which versions) to install, ensuring everyone has the same setup.

*   **JupyterLab:**
    *   **Definition:** An interactive, web-based development environment. It allows you to write and run code in small pieces, visualize data, and add text explanations all in one document.
    *   **Simple Explanation:** It's like a super-powered digital notebook where you can write code, see the results immediately, and take notes alongside it.

---

### **Summary of Key Points**

*   This guide provides a **simpler, fallback method** for setting up your Python environment using `venv`.
*   **PC users must install Python first;** Mac users can skip this step.
*   The core process is:
    1.  **Create** the environment with `python -m venv venv`.
    2.  **Activate** it with `source venv/bin/activate` (Mac) or `venv\Scripts\activate` (PC).
    3.  **Install** packages with `pip install -r requirements.txt`.
    4.  **Launch** your workspace with `jupyterlab`.
*   A virtual environment **isolates your project's dependencies**, preventing conflicts with other software on your computer.
*   If you encounter any issues, the instructor encourages you to reach out for help.
