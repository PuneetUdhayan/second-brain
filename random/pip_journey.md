What exactly happens when you run pip install <package> How are the .gz files used?

When you run `pip install <package>`, you’re essentially kicking off a multi-step "find-and-build" mission. Pip isn’t just downloading a folder; it’s resolving dependencies, checking your system architecture, and often compiling code on the fly.

Here is the breakdown of what happens under the hood.

---

## 1. The Discovery Phase

Pip first looks at the **Python Package Index (PyPI)** (or another repository you've specified).

* **Version Check:** It looks for the version you requested (or the latest stable one).
* **Dependency Resolution:** This is the "brainy" part. Pip looks at the package's requirements and checks if they conflict with things you already have installed. If Package A needs "Requests 2.0" and Package B needs "Requests 1.0," Pip has to find a version that satisfies both or throw an error.

## 2. The Download Phase (Where .gz comes in)

Pip looks for two main types of files to download:

1. **Wheels (`.whl`):** These are "Built Distributions." They are pre-compiled and ready to be unzipped directly into your site-packages. They are fast and preferred.
2. **Source Distributions (`.tar.gz` or `.zip`):** These are the **SDists**. If a developer didn't upload a Wheel for your specific operating system or Python version, Pip falls back to the `.gz` file.

### How are the `.gz` files used?

A `.tar.gz` file is a compressed archive containing the original source code, a `pyproject.toml` or `setup.py` file, and some metadata.

* **Extraction:** Pip downloads the `.gz` to a temporary directory on your computer and unzips it.
* **Building:** Since the code is "raw," Pip needs to turn it into a format your system can actually run. It triggers a **build backend** (like `setuptools` or `flit`).
* **Compilation:** If the package contains C or C++ extensions (common in libraries like NumPy), your computer will actually compile that code during this step. This is why you might see a wall of text and high CPU usage when installing certain packages—it's literally building the software from scratch.
* **Wheel Creation:** Pip actually builds a temporary `.whl` file from the extracted `.gz` source before moving to the final step.

## 3. The Installation Phase

Once Pip has a "Wheel" (either one it downloaded or one it just built from the `.gz` file), it:

* Unpacks the files into your Python installation's `site-packages` folder.
* Records the installation in a `dist-info` folder so it knows how to uninstall it later.
* Handles "entry points," which create those handy commands you run in your terminal (like `flask` or `pytest`).

---

### Summary Table: Wheel vs. SDist (.gz)

| Feature | Wheel (`.whl`) | Source Dist (`.tar.gz`) |
| --- | --- | --- |
| **Status** | Pre-built / Compiled | Raw Source Code |
| **Speed** | Very Fast | Slower (requires building) |
| **Reliability** | High | Can fail if you lack compilers (like GCC) |
| **Flexibility** | Specific to OS/Python version | Platform independent (works everywhere) |

> **Pro-Tip:** If you ever see a massive error involving "Command 'python setup.py egg_info' failed," it's usually because Pip is trying to use a `.gz` file but your computer is missing a tool (like a C compiler or a header file) needed to build it.

Would you like me to show you how to check which version (Wheel or SDist) Pip is choosing for a specific package on your system?