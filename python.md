> Course website (this) and GitHub are new and still under construction. Please bear with us and notify us of any issues/suggestions.

| [Discussions](https://github.com/orgs/uiuc-cs448/discussions) | [Canvas](https://canvas.illinois.edu/courses/52926) | [Repositories](https://github.com/orgs/uiuc-cs448/repositories) | [Python Setup](https://uiuc-cs448.github.io/python) |
| ------------------------------------------------------------- | --------------------------------------------------- | --------------------------------------------------------------- | --------------------------------------------------- |

> ℹ️ This website focuses on the **online platforms** of this course to help you onboard. Refer to the syllabus, day-1 lecture slides, announcements, etc. on Canvas for complete coverage on everything else.

# Python Setup

> This reference (_not_ a tutorial/guide) is written by a CA who doesn't know everything. Some information can be incomplete, confusing, or just plain wrong, but that's what documentation, Discussions, StackOverflow, etc. is for:)

![](images/python.png)
_Necessary [XKCD comic](https://xkcd.com/1987/)_

There are _many_ ways to setup a Python environment and dealing with it can be [a pain in the ass](https://xkcd.com/1987/). Fortunately, this class isn’t too picky beyond having Python 3.9+ with a few dependencies/libraries. If you already have an existing Python setup that you like, you can probably skip to [installing dependencies](https://uiuc-cs448.github.io/python#installing-dependencies).

## Global vs. Multiple Environments

An environment encapsulates a set of development tools (e.g. a set of dependencies and even a Python version) that you can use for a project, multiple projects, or all projects across your entire OS. Choose between:

- **A global environment**: Every project on your computer shares one Python version and uses the **same set of dependencies**. This is easy to start with but hard to maintain when projects require conflicting versions of dependencies, etc.
- **Multiple environments**: Each project (or a subset of projects) can access a **dedicated, isolated set of dependencies** or even a separate Python version. This is the standard if you often use Python.

### Install a Python version manager

Recommended, also enables using multiple environments.

- [`pyenv`](https://github.com/pyenv/pyenv) ([`pyenv-win`](https://github.com/pyenv-win/pyenv-win) for Windows) manages **multiple Python versions** so you can switch between them for different projects (see [`pyenv-virtualenv`](https://github.com/pyenv/pyenv-virtualenv) for also managing environments)
- [`conda`](https://docs.conda.io/projects/conda/en/latest/index.html) manages **multiple environments** that also each have their own Python version, unlike `pyenv` which lets you share a Python version across different environments.

### Install a global Python version

Mac preinstalls a system-dependent version of Python in `/usr/local/bin/`, but we recommend not touching/breaking that. Windows and Linux do not preinstall their own Python.

- Install via a general _software package_ manager (lets you reinstall different Python versions, but usually not switch between them–you'd still only have one version at a time).
  - Mac: [Homebrew](https://brew.sh/), see [their docs for Python](https://docs.brew.sh/Homebrew-and-Python).
  - Windows: [Chocolately](https://chocolatey.org/), although not many devs use Windows so this isn't popular, most people default to installing from [python.org](https://www.python.org/).
  - Linux: use your specific distribution's package manager. ~~Go figure it out, this is your fault!~~
- Install via [python.org](https://www.python.org/). Easy to start with, hard to maintain.

### Dependency Environments with a global Python version

`pyenv`, `conda`, etc. already have their own support for managing Python environments (so skip this if that applies). But if you installed a single Python version via [homebrew](https://formulae.brew.sh/formula/python@3.12), [python.org](https://www.python.org/), etc., use virtual environments ([`venv`](https://docs.python.org/3/library/venv.html)s).

`venv` is built-in with Python and lets you manage multiple environments for dependencies that still **share the same global Python** that you installed earlier. It requires remembering a few commands to activate/open etc. for a project, but you can automate that or opt for `pyenv-virtualenv`, `conda`, etc. which do it for you.

## Installing Dependencies

Each lab requires the same dependencies, which we listed in the `requirements.txt` file of each repo. So if you're using a dedicated environment, you can use the same one for each lab.

For most cases (and once you enter the dedicated environment), run `pip install -r requirements.txt` at the repo level to install dependencies using the `pip` Python package manager. Beware that some previous options recommend a different package manager, e.g. `conda install --yes --file requirements.txt`.

## IDEs!

Once you clone and open your lab in your favorite IDE, set it up to run Jupyter notebooks. Some popular ones:

- [VSCode](https://code.visualstudio.com/docs/datascience/jupyter-notebooks): Install the `ms-toolsai.jupyter` extension, then click `Select Kernel` → `Python Environments...` → `Python 3.X /path/to/python`.
- [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/getting_started/starting.html) (dedicated offline in-browser IDE): Run `jupyter lab` in the terminal at the repo level, which opens the IDE (`http://localhost:...`)
- Others (e.g. [PyCharm](https://www.jetbrains.com/help/pycharm/configuring-jupyter-notebook.html), [Spyder](https://docs.spyder-ide.org/current/plugins/notebook.html)): you may need to run `jupyter server --no-browser --NotebookApp.alloworigin="\*"` in the terminal and copy-paste that local server URL into the IDE settings. Follow platform-specific instructions.
