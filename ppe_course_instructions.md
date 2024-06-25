# Python Project Essentials: A Comprehensive Setup Guide

Contents:

- Intro
- Lecture 1
    - VSCode setup
    - Python setup
    - Poetry setup
- Lecture 2
    - Project setup
    - pyproject.toml file setup
    - Code formatting setup
    - Linting setup
- Lecture 3
    - Git setup
    - Pytest setup
    - Pre-commit hook setup
    - GitHub Actions setup
- Lecture 4
    - Jupyter setup
    - Command line argument setup
- Lecture 5
    - Debugger setup
- Lecture 6
    - Secrets setup
 
# Lecture 1

## Codespaces setup

The first step is to set up Codespaces, this is optional if you use your own machine. Log in to GitHub and from your home page:
- Create a new private repo
- Add python-based `.gitignore`, `MIT license`, `README.md`
    - Here's a comprehensive `.gitignore`: [https://github.com/github/gitignore/blob/main/Python.gitignore](https://github.com/github/gitignore/blob/main/Python.gitignore)
- Start codespace (`Code` dropdown, where you usually see the repo clone address)

__Learning Objectives:__
- How to create a repository
- How to start a Linux machine in codespaces

## VSCode setup

Once inside the remote environment, the next step is to setup VSCode, this is optional if you have already done this on your local machine.

- Create `.vscode` directory
- Create `settings.json` in `.vscode`
- Add to `settings.json`

```
{
    "workbench.colorTheme": "GitHub Dark Default",
    "editor.formatOnSave": true,
    "[python]": {
        "editor.defaultFormatter": "ms-python.black-formatter",
        "editor.insertSpaces": true,
        "editor.tabSize": 4
    },
    "python.formatting.provider": "none",
    "workbench.editor.enablePreviewFromQuickOpen": false,
    "workbench.editor.enablePreview": false,
    "comments.visible": false,
}
```

- Go to: Settings (Cog bottom left) -> Keyboard Shortcuts -> Json file (Document icon top right with an arrow wrapped around it)

```
// Place your key bindings in this file to override the defaultsauto[]
[
    {
        "key": "cmd+[",
        "command": "-editor.action.outdentLines",
        "when": "editorTextFocus && !editorReadonly"
    },
    {
        "key": "cmd+[",
        "command": "workbench.action.navigateBack",
        "when": "canNavigateBack"
    },
    {
        "key": "cmd+]",
        "command": "workbench.action.navigateForward"
    },
    {
        "key": "cmd+]",
        "command": "-editor.action.indentLines",
        "when": "editorTextFocus && !editorReadonly"
    }
]
```

- Create `hello_world.py` in the project root
    - This will trigger VSCode to recommend a set of extensions: `Python`, `Pylance` and `Black`. If not, add these extensions manually by clicking the Extensions menu ("Four boxes, one detached") on the left; all three were written by Microsoft and have millions of downloads.
- Add to `hello_world.py`

```
if __name__ == '__main__':
    print('Hello World!' )
```

- Run `python hello_world.py` to test your code.

__Learning Objectives:__
- Settings VSCode with configuration file
- Settings keyboard shortcuts manually or with a configuration file
- Installing extensions
- Checking if the code formatting extension works

## Python setup

We will use pyenv to install the correct python version and set the default python version on the machine. This is optional if you already have the right version and if you are using a different OS see [https://www.python.org/downloads/](https://www.python.org/downloads/) for your install files.

This instructions will use 3.11 but it is straightforward to change it to anything else.

- Run `python --version` to test which version of python the OS has (Ubuntu 22 LTS "Jammy Jellyfish has 3.10 by default)
- Run `curl https://pyenv.run | bash`
- Run `export PYENV_ROOT="$HOME/.pyenv"`
- Run `export PATH="$PYENV_ROOT/bin:$PATH"`
- Run `eval "$(pyenv init -)"`
- Run `pyenv install 3.11 --skip-existing` (this will take about 5 minutes)
- Run `pyenv global 3.11`
- Run `python --version` to test that you have `3.11` as your default python version

The next section will update the shell (`bash` or `zsh`) to make pyenv's version of python to be the default each time we start a terminal.

- Run a nonsense command `asd` in the terminal
    - It will respond with `bash: asd: command not found` on Linux. So we are using `bash` shell, so we need to update `.bashrc`. If it would say `zsh` we would update `.zshrc`.
- Press `Cmd-O` or `Ctrl-O` to open a file and navigate to the home directory by typing `~`
- Select `.bashrc`
- Append these lines to `.bashrc`

```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
alias gs="git status"
export JUPYTER_CONFIG_DIR="."
```

- Start a new terminal by clicking the `+` symbol next to the terminal on the right above the list of terminals
- If you enter `python --version` it should say Python 3.11 instead of 3.10 indicating that `pyenv` was successfully initialised.

You can also include other useful shortcuts in the `.bashrc` file that you can see in the last line (e.g.: aliases). For example I like to abbreviate `git status` to just `gs` to be more efficient.

__Learning Objectives:__
- Install different python versions
- Configure shell to make this the default version
- Update `.bashrc` to make `pyenv` load for every new terminal
- Creating shortcuts with `alias`

## Poetry setup

We will use `poetry` as a package manager. This is one of many package managers available today and it has all the typical features that modern python package managers must have. If you want to use a different package manager (e.g. `pdm`) their features will be familiar after finishing this course.

- Run `curl -sSL https://install.python-poetry.org | python3 -`
    - See the official site for Windows or alternative options [https://python-poetry.org/docs/#installing-with-the-official-installer](https://python-poetry.org/docs/#installing-with-the-official-installer)

__Learning Objectives:__
- Install poetry on a new machine

# Lecture 2

## Project setup

`Poetry` manages packaging instructions and your project through the `pyproject.toml` file. If you are adding poetry to an existing project (like here):

- Run `poetry init --python=~3.11 -n`
     - This will create the `pyproject.toml` in the main repository directory
- Create `src` directory
- Create `src/<project_name>` directory
- Create `__init__.py` file in `src/<project_name>`
- Create `tests` directory
- Create `__init__.py` file in `tests`
- Add `packages = [{include = "<project_name>", from = "src"}]` to `pyproject.toml` at the end of `[tool.poetry]` section
    - If you are starting from scratch (unlike here): Run `poetry new -n --src <project_name>`. This will create the above project directory structure and the correct `pyproject.toml` file.
- Run `poetry config virtualenvs.in-project true`
    - This will make sure that virtual environments created for the project is in the repository's directory. As we added a python based `.gitignore` at the beginning this will be ignored by `git` otherwise make sure `.venv` is in `.gitignore`.
- Run `poetry install`
    - This will install your package and create the `.venv` virtual environment (should be grey in the file explorer if `.gitignore` is correctly set)
    - VSCode will prompt you that it detected a new virtual environment and if you want to use it as a default (which you should as VSCode's extensions will use it to detect installed packages)
    - If it doesn't, select any python file, click on the bottom right where it says "Python" and select from the listed interpreters the `('.venv': Poetry)` one. If this doesn't show either then "Enter interpreter path..."  -> "Find..." and then navigate to `.venv/bin` and select `python`. The selected interpreter should be shown as `('.venv': Poetry)`.
- You should now run all your python scripts/commands using `poetry` like so: `poetry run python <python_script.py>` or `poetry run jupyter notebook`, this will activate the virtual environment and set up the right environment variables.
    - You can also activate the environment with `source .venv/bin/activate`, and the prompt should change to `(<project_name>-py3.11)` indicating that you are using the environment created by `poetry`.

__Learning Objectives:__
- Configure an existing python project with poetry
- Create a new python project with poetry
- Configure VSCode to use the project's virtual environment

## pyproject.toml file setup

This next section will explain how to set up the `pyproject.toml` file. `Poetry` manages the packages through two files `pyproject.toml` and `poetry.lock`. The first one details instructions and settings for the environment, the second the explicit versions of currently installed packages.

These are not necessary the same. Package authors define instructions of which versions of other packages their own is compatible with. When you add packages to `pyproject.toml`, you can specify which versions you are OK with. Then `poetry` uses a resolver based on all the required packages to find an explicit set that satisfies all restrictions. If there are more options, it will pick the latest ones.

More on this: [https://python-poetry.org/docs/dependency-specification/](https://python-poetry.org/docs/dependency-specification/)

If you add a package with `poetry add <package>` by default, it will pick the latest version available on `PYPI` and set the compatibility to "up to next version". For example, if you initialise the project with `poetry init -n` without `--python=~3.11`, you will default to the machine's python version `3.10` and see a line `python = "^3.10"` in `pyproject.toml`. This means anything from `3.10` to `3.x` but not `4.0`, which is clearly too wide for most applications. The correct setting is `python = "~3.11"` which means anything from `3.11.1` to `3.11.x` but not `3.12`. Changing `python` versions can bring compatibility issues, so this is a more sensible setting. But the `--python=~3.11` took care of this at initalisation.

If the resolver can not find a compatible set, it will fail with an error message (it is pretty rare unless you didn't set the python version as above). The resolution is to "pin" the version of some packages with either `~a.b.c` or `=a.b.c` and run `poetry install`.

- Run `poetry add black ruff pytest pre-commit` to install the following packages.
    - `black`: code formatting
    - `ruff`: code linting
    - `pytest`: testing
    - `pre-commit`: package to execute pre-commit hooks

You will see these packages added to `project.toml` under `[tool.poetry.dependencies]` with their latest version in `pypi` with the `^` dependency specification.

This will also create `poetry.lock` file which will store the explicit versions of packages that were installed into this virtual environment. In the future when someone clones the environment and wants to have the exact same setup as you have now they will not run the resolver again and accidentally end up with a different set of versions, but they will run `poetry install` which will use the lock file to create a copy of the environment so this cannot be a source of difference.

__Learning Objectives:__
- Adding packages to a project with poetry
- Managing dependencies with pyproject.toml
- Managing reproducability with lock files

## Code formatting setup

"Customers can have any colour they want, as long as it is black." - Henry Ford

The python community solved arguments around code formatting by adopting a single standard with no options. Simply use `black` for code formatting and be done with the topic.

The only setting I like to change is the length of the rows and I like to use single quotes instead of converting everything into double quotes so if you want to follow suit add the following rows at the end of `project.toml`.

```
[tool.black]
skip-string-normalization = true
line-length = 120
```

VSCode will use this setting to format your code when saving a file (manually, as autosave doesn't trigger code formatting).

__Learning Objectives:__
- Configuring code formatters
- Understanding that the same configuration is used in VSCode

## Linting setup

An emergent tool for linting python code is `ruff` so the following will detail this. In fact, it will probably take over `black` as a code formatter and will be used in VSCode with an extension. It integrates all previously used linting-related packages into a single tool rewritten in an efficient language (rust).

What matters here is the configuration choices. Add the following settings (copied from FastAPI) to the end of `project.toml`. These demonstrate how to exclude files, how to select checked linting features and how to ignore some.

In a CI/CD setup as we will see in pre-commit hooks and GitHub Actions, usually failing the linting will fail the process and the code can not be merged into the main branch. `ruff` will also make an attempt to fix fixable errors if it is run with the `--fix` switch which is usually recommended.

```
[tool.ruff]
# Same as Black.
line-length = 120

exclude = ["excluded_file.py"]
lint.select = [
    "E",  # pycodestyle errors (settings from FastAPI, thanks, @tiangolo!)
    "W",  # pycodestyle warnings
    "F",  # pyflakes
    "I",  # isort
    "C",  # flake8-comprehensions
    "B",  # flake8-bugbear
]
lint.ignore = [
    "E501",  # line too long, handled by black
    "C901",  # too complex
]
```

Standardising `import` statements is another linting issue. Add the following content to the end of `project.toml`. This demonstrates how to specify import order. One particularly important one is `known-first-party`, where you need to list each internal package (directory) that can be imported as `from directory import ...` to group them together separately from third-party libraries (`numpy`, `pandas`, etc.),

```
[tool.ruff.lint.isort]
order-by-type = true
relative-imports-order = "closest-to-furthest"
extra-standard-library = ["typing"]
section-order = ["future", "standard-library", "third-party", "first-party", "local-folder"]
known-first-party = []
```

Most of the time these settings should work "as it is" apart from `known-first-party` so it's worth keeping them in a file and dropping them into every new project.

__Learning Objectives:__
- Difference between code formatting and linting
- Setting up linting with ruff
- Setting up import formatting with ruff

# Lecture 3

## Git setup

In this course we already have a repository (We cloned one when we started the codespace) so jump to [*].

On a local machine, if you started the project from scratch with `poetry new -n --src <project_name>`, it's time to add version control.

- Create a _completely_ empty repository on GitHub (no README, Licence or anything)
- Run the following commands in the terminal:

```
    git init
    git add .
    git commit -m "first commit"
    git branch -M main
    git remote add origin git@github.com:<username>/<project>.git
    git push -u origin main
```

This will push all these changes into the `main` branch in one go. It is not usually recommended to do so and the `main` branch is often protected against direct updates. The regular practice is that you can only merge another branch into `main` after creating a pull request, as this will let `GitHub` to run the actions and check if the code is not breaking any tests and is correctly formatted. Because the repository is empty, I think this is acceptable to push directly.

[*]

If you are already in a git repository (like here), run the following commands:

```
    git checkout -b first
    git add .
    git commit -m "first commit"
    git push    (as this is the first time you push from this branch, this will respond to you with a command you _actually_ need to run something like: git push --set-upstream origin first)
```

If you go to GitHub it will prompt you if you want to create a pull request, create it and the "Merge pull request" button, in the dropdown select "Squash and merge". This will turn all the commits into one commit to keep the repository history clean. Then confirm the merge and delete the branch.

Back here run the following commands:

```
    git checkout main
    git pull
    git checkout -b second
```

This will update the local `main` branch with the code that was just merged and create a `second` branch from it that will be used from now on.

__Learning Objectives:__
- Running a basic git-flow
- Difference between getting a project by cloning a repository and starting a project locally and then adding to GitHub

## Pytest setup

Books can be written on testing so here this is just a basic setup of `unittest` style tests that are part of `python` by default. All tests are classes derived from `unittest.TestCase`, the actual tests are member functions of these classes. To do an assertion you can refer to many different options available in `self.`. The tests are run by `pytest` and don't need to be listed explicitly as they will be "discovered" automatically. To do so all class names must start with `Test...` and all member functions must be named `test_...`.

- Create `test_hello_world.py` in `tests`
- Add to `test_hello_world.py`

```
from unittest import TestCase


class TestHelloWorld(TestCase):
    def test_upper(self):
        self.assertEqual("hello world!".upper(), "HELLO WORLD!")
```

- Run `poetry run pytest`. This will run without a failure and show a green success message.
- Change the `"HELLO WORLD!"` string to something else and rerun `pytest` to see a failing test.

__Learning Objectives:__
- Creating tests with `unittest`
- Running tests with `pytest`
- Verify that tests are passing
- Verify if tests are _not_ passing

## Pre-commit hook setup

Pre-commit hooks are standard checks that run locally each time you try to commit your code. They help to make sure you don't commit code that are not compliant to coding standards. As these happen before committing these changes won't even be part of git commit history. We will look at how to set these up and a couple of useful ones.

- Create `.pre-commit-config.yaml`
- Add to `.pre-commit-config.yaml`

```
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: check-yaml
      - id: end-of-file-fixer
      - id: trailing-whitespace
  - repo: https://github.com/python-poetry/poetry
    rev: '1.5.1'
    hooks:
      - id: poetry-check
  - repo: local
    hooks:
      - id: black
        name: black
        entry: poetry run black
        language: system
        types: [python]
      - id: ruff
        name: ruff
        entry: poetry run ruff check --fix
        language: system
        types: [python]
```

- Run `poetry run pre-commit install`, this will install the hooks into the local copy of the repository. This needs to be run again if the repository is copied into a new location!
- Run `git add .` and `git commit -m "second"` to add and commit latest changes.
- The first time `precommit` will create its own environment but later it will just reuse them.

The first three hooks are standard file formatting ones (for example `end-of-file-fixer` will delete extra spaces at the end of each row). `poetry-check` will check if `pyproject.toml` is correctly formatted. See the explicit script running here: [https://github.com/python-poetry/poetry/blob/master/.pre-commit-hooks.yaml](https://github.com/python-poetry/poetry/blob/master/.pre-commit-hooks.yaml).

The rest will run locally black and ruff again to make sure they are correctly formatted. As `ruff` will run with the `--fix` option if the error is fixable it will do so on its own.

It is important to note that the hooks only run on files that were changed in the commit. If there was no error the hook will report "Passed" if the hook is not relevant to the currently committed files it will report "Skipped". If there was an error it will report "Failed" and try fixing the error. The changes need to be added and committed again, when the hooks run again. These must be repeated until all the hooks pass or skip.

__Learning Objectives:__
- Setting up precommit hooks
- Installing precommit hooks (which you need to do each time you clone the repository)
- Verifying that precommit hooks ran

## GitHub Actions setup

There are many CI/CD (continuous integration/continuous delivery) systems around and they support efficient development and automation. Here we will demonstrate their main features through GitHub Actions. The typical workflow is that after a pull request is created on a remote machine the exact copy of the environment is recreated from `poetry.lock` and a set of functions (linting, testing) is run to make sure that everything is in order. Only once these tests are passed the code can be merged into the `main` branch. This ensures that all collaborators can be sure that the code is in good condition.

- Create `.github` directory
- Create `workflows` directory in `.github`
- Create `python-app.yml` file (naming can be customised)
- Add to `python-app.yml`

```
# This workflow will install Python dependencies, run tests and lint with a single version of Python
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-python

name: Python application

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

permissions:
  contents: read

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Set up Python 3.11
      uses: actions/setup-python@v3
      with:
        python-version: "3.11"
    - name: Install poetry
      run: |
        python -m pip install poetry
    - name: Configure poetry
      run: |
        poetry config virtualenvs.in-project true
    - name: Cache the virtualenv
      uses: actions/cache@v2
      with:
        path: ./.venv
        key: ${{ runner.os }}-venv-${{ hashFiles('**/poetry.lock') }}
    - name: Install dependencies
      run: |
        poetry install
    - name: Lint with ruff
      run: |
        poetry run ruff check .
    - name: Run tests
      run: |
        poetry run pytest
```

What is defined in this file? First there is the name of the workflow, when this is supposed to run (at any PR on the branch and it runs again on `main` after merging). Then there is a specification of the container it should run on (`ubuntu-latest`). The next section specifies a set of standard steps (`checkout`, `setup-python`) that are so common they are maintained communally here
[https://github.com/actions/checkout/blob/main/action.yml](https://github.com/actions/checkout/blob/main/action.yml) and here
[https://github.com/actions/setup-python/blob/main/action.yml](https://github.com/actions/setup-python/blob/main/action.yml). Note the specification of the right `python` version.

The rest are custom shell commands to create the poetry environment and then run `ruff` and `pytest`. Note the `poetry install` command that will use `poetry.lock` to create the exact environment you had locally. Also note that `ruff` doesn't have the `--fix` option so if a PR fails on linting you need to fix that locally. This usually happen when a file was somehow changed without the pre-commit hooks installed (or directly on GitHub) and when you created the PR this file was not changed so your pre-commit hook didn't check it when you committed your changes. TL;DR: Make sure you run `poetry run pre-commit install` on a freshly cloned repository.

Create a second pull request and push all changes (don't forget to add `.github` to the branch) and create the pull request on GitHub.

- Run `git add .`
- Run `git commit -m "ga"`
- Run `git push`
- Create a new pull request on GitHub

At the pull request's "Conversation" page, you will see:

`Python application / build (pull_request) Queued — Waiting to run this check...`

If you go to `Details` you can see the commands executed on the remote machine as detailed in the above script. If you left the error in the test when you experimented in the `Pytest setup` section you will be greeted with `All checks have failed` and `Python application / build (pull_request) Failing after 25s` if you go to `Details` you can see which exact command failed and what was the error message. Fix this and commit the change and push it again. This will trigger the workflow again and hopefully the second time it will pass. The pull request _can not be merged_ until this is resolved!

- Fix the test
- Run `git add .` and `git commit -m "tests fixed"` and finally `git push` to update the remote branch

If the pipeline ran successfully, you will be greeted with `All checks have passed - Show all checks` (you can have more than one and all must pass) and clicking it will tell you the status and running time of each workflow. At this point this pull request can be merged into main.

- Merge the PR
- In the terminal, run: `git checkout main`, `git pull` to update the local `main` branch
- Run `git checkout -b third` to create a new working branch

__Learning Objectives:__
- Setting up GitHub Actions
- Understanding what actions will happen in a workflow
- Verifying if the workflows ran and what was the outcome

# Lecture 4

## Jupyter setup

At last we setup a typical python environment:

- Run `poetry add numpy pandas pyarrow scikit-learn matplotlib jupyter`
    - Note: it is `scikit-learn` not `sklearn`, always check on pypi what is the install command for any packages!!!!
    - We need `pyarrow` for pandas otherwise it throws a `DeprecationWarning` and this affects `pytest` later
- Create `jupyter_server_config.py`
- Add to `jupyter_server_config.py`

```
c = get_config()  # noqa: F821

c.ServerApp.open_browser = False
c.ServerApp.ip = '0.0.0.0'
c.ServerApp.port = 8889
c.ServerApp.allow_origin = '*'
c.ServerApp.password_required = False
```

- Run `export JUPYTER_CONFIG_DIR="."`
- Run `poetry run jupyter notebook`
- Make a note of the access token in the terminal
- Switch to the notebook server
    - Either clicking the popup in VSCode or the PORTS tab in the termnal and hover over "Forwarded Addresses" in the 8889 row and click the globe icon
- Create a new notebook `example_notebook` and select the default kernel (Python)
- Add to the first cell

```
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
plt.plot(np.random.randn(100))
```

- Create `example_class.py` in `src/<project_name>`
- Add to `example_class.py`

```
import pandas as pd


class ExampleClass:
    def __init__(self, param):
        self.param = param

    def get_df(self):
        return pd.DataFrame(data=[self.param])
```

- Append to the end of the first cell

```
from <project_name>.example_class import ExampleClass
```

- Add to the notebook in a new cell

```
example_class = ExampleClass(param=2)

example_class.get_df()
```

- Replace `return pd.DataFrame(data=[self.param])` with `return pd.DataFrame(data=[self.param * 2])`
- Restart the notebook
- Rerun the notebook
- Add to the top of the first cell in the notebook

```
%reload_ext autoreload
%autoreload 2
```

- Rerun the notebook
- Replace `return pd.DataFrame(data=[self.param * 2])` with `return pd.DataFrame(data=[self.param, self.param * 2])`
- Run the cell with `ExampleClass`

Code changes should be automatically imported. This has its limits as it has no effect on already existing objects, but still very useful in fast moving development.

__Learning Objectives:__
- Adding Data Science packages to a project
- Configuring Jupyter notebooks
- Starting the notebook server
- Setup autoreload in a notebook
- Verify that autoreload is running

## Command line argument setup

This section is about passing parameters to a python script. To parse the arguments we will use `typer` to simplify our work.

- Start a new terminal by clicking the `+` button on the bottom right next to the terminal (We need another one as the previous is running Jupyter now). Note that VSCode immediately starts the virtual environment.
- Run `poetry add typer` to add the package to the project
- Replace the content of `hello_world.py` with:

```
import typer


def main(name: str):
    print("Hello World!")
    print(f"Hello {name}!")


if __name__ == "__main__":
    typer.run(main)
```

`typer` is simple to use: Just wrap your script as a function and pass any command line arguments as arguments with typehints. You must add typehints so `typer` can parse the arguments into the correct type.

- Run `python hello_world.py --help` to see the (autogenerated) command line help for your script. This shows that `name` is a required parameter.
- Run `python hello_world.py Test` to check if it is working.

- Replace the content of `hello_world.py` with:

```
import typer


def main(name: str, num: int = 2):
    print("Hello World!")
    for k in range(num):
        print(f"{k} Hello {name}!")


if __name__ == "__main__":
    typer.run(main)
```

This contains an optional parameter that defaults to `2` to set how many times the second print line is repeated.

- Run `python hello_world.py --help` to see the command line help
- Run `python hello_world.py Test` to check if it is working (You still need the required argument).
- Run `python hello_world.py Test --num 5` to see that it is indeed set in the command line (you need to specify the keyword for optional arguments)

See more details on `typer` at [https://typer.tiangolo.com/](https://typer.tiangolo.com/)

__Learning Objectives:__
- Creating command line interfaces for python scripts with `typer`
- Specifying required and optional parameters

# Lecture 5

## Debugger setup

Since the script file now requires an argument, before running the debugger we first need to set up a debugging configuration to supply these parameters.

- Click the Play Button with a Bug symbol on the left
- Click `create a launch.json file`
- In the `Select debugger` popup, select `Python` and next `Python file`
- Add `"args": ["Name", "--num", "5"],` below `"program": "${file}",`, this will pass these values to the python script
- If you know that you always run the same script replace `"${file}"` with `"hello_world.py"`
- If you press the play button top left next to `RUN AND DEBUG` it will execute
    - Since we didn't set any breakpoints this will just run and exit after printing the messages
- Go to `hello_world.py`
- Click on the left of the command line number in the row `print(f"{k} Hello {name}!")`.
    - This will create an unconditional breakpoint. Any time the interpreter reaches this line it will stop.
- Run the debugging by pressing the play button
    - The execution stops on the line and shows the local variables `k: 0`, `num: 5`
    - In the top middle of the editor window the debugging toolbar appears. The first is the `Continue` button and the last is the `Stop` one.
- Rightclick on the breakpoint and select `Edit Breakpoint...`
- At the `Expression` edit box, write `k==3`
    - This will only stop execution when the above condition is satisfied.
- Click `Continue` on the debug toolbar
    - Execution will stop when `k: 3`, this is the _fourth_ cycle but you will only see three `Hello Name!` messages as the current line did not yet run.
- Press `Step Over` to execute this line without continuing debugging _into_ the print() function (That would be the next button `Step Into`).
    - The fourth message will appear and the yellow icon of current row to be executed jumps back to the `for k in range(num):` row.
- To finish the program press `Continue`
    - As the condition will never become true again this will run the whole script and exit (terminal appear again). If there would be another breakpoint, execution would stop there.

The debugger also stops execution if an unhandled exception occurs. Let's see an example for this:

- Add `1/0` in the next row after `print("Hello World!")`
    - This will create a `ZeroDivisionError` at runtime
- Press the play button next to `RUN AND DEBUG`
- Execution will halt and you can examine the error and
- Press `Continue` or `Stop` on the debug toolbar to terminate execution
- Remove the `1/0` and run the script again just to be sure

More one the VSCode Debugger: [https://code.visualstudio.com/docs/python/debugging](https://code.visualstudio.com/docs/python/debugging)

Debugging in Jupyter is done with `pdb`, part of Python.

- Go to `example_class.py`
- Add `import pdb; pdb.set_trace()` after `def get_df(self):`
- Go the the notebook and execute the cell with `ExampleClass` (as we have autoreload, this will immediately work)
- Execution will interrupt on the _next_ line after the `set_trace` command
- Run `p self.param` in the textbox to print the value of `self.param`
- Run `n` to execute the next line of code
- Run `q` in the textbox to exit debugging

More on `pdb` and single character commands: [https://docs.python.org/3/library/pdb.html](https://docs.python.org/3/library/pdb.html)

Often an exception is thrown and we would like to check the state of the program at that point. Jupyter provides a service for this as well.

- Replace the line `import pdb; pdb.set_trace()` with `1/0` in `ExampleClass`
- Run the cell containing it
    - This will create an uncaught `ZeroDivisionError` but the program will terminate
- Into the next cell write `%debug` and run the cell
    - This will move the frame back when the exception happened
    - `pdb` is running in the background so the same commands as before work
- Run `p self.param` to see `self.param`
- Run `q` to exit

__Learning Objectives:__
- Setting up debug configurations in VSCode
- Debugging with VSCode
- Creating conditional and unconditional breakpoints
- Debugging with `pdb` in Jupyter
- Debugging uncaught exceptions

# Lecture 6

## Secrets setup

Storing secrets (passwords, access tokens, anything private) in repositories (even private ones) is a major security issue that you should avoid at any cost. The last section will explain how to use `.env` files and secrets on GitHub to handle these.

We will demonstrate this by modifying the existing test to depend on a secret value that is loaded from a `.env` file locally and from GitHub Secrets during testing.

We will use the `dotenv` package for this that loads environment variables into the running script which will be available in `os.environ`.

- Add the `dotenv` package `poetry add python-dotenv`
    - Mind the difference between importing (`dotenv`) and installing (`python-dotenv`)
- Add `.env` file to the main directory of the repository
    - Mind that the `.env` file is dark grey meaning it will _not_ part of the repository. `.env` is explicitly listed in the `.gitignore` file we added when created the repository.
- Add `SECRET_VARIABLE="this is secret"` into `.env`
- Check that `.env` is in `.gitignore`
    - If you create the `.gitignore` file manually, this is the time to add this to it
- In `test_hello_world.py` add `from dotenv import load_dotenv`
- Also add `import os`, we will get the secret value from the environment variables
- Replace the test with:
    - This will load the variables in `.env` into `os.environ`

```
    def test_upper(self):
        load_dotenv()
        self.assertEqual(os.environ["SECRET_VARIABLE"].upper(), "THIS IS SECRET")
```

- Run the test with `poetry run pytest`
- Create a new branch if you haven't yet with `git checkout -b third`
- Last check that you are not adding `.env` to the repository with `git status`
- Add and commit everything with `git add .` and `git commit -am "dotenv"`
    - Make sure your changes pass the precommit hooks and if `black`/`ruff` suggest changes those must be committed as well
- Push the changes to GitHub with `git push` (then `git push --set-upstream origin third`)
- Create a new pull request on GitHub
- Wait for the GitHub Actions to run the tests

At this point the tests will fail as the `.env` file is not available remotely on GitHub. You can see that it is not `load_dotenv()` that is failing but the `os.environ["SECRET_VARIABLE"]` expression with `KeyError`. Namely, `SECRET_VARIABLE` is not an environment variable so it is not in the `os.environ` directory.

In the next step we will see how this value can be safely stored on GitHub.

- Go to the `Settings` tab of the repository (last one past the `Code \ Issues \ Pull requests...`)
- On the left `Secrets and Variables` and then `Actions`
- Click `New repository secret`
- Set `Name` to `SECRET_VARIABLE` and `Secret` to `this is secret`
    - No quotes regardless of spaces!!! The text will be passed as it is.
- Click `Add secret`
    - Warning: You will _not_ be able to see the value ever again. The only action you can take is to delete the secret and add a new value. This is for security reasons.
- In `.github/workflows/python-app.yml` at the `- name: Run tests` step, replace the section with:

```
    - name: Run tests
      env:
        SECRET_VARIABLE: ${{ secrets.SECRET_VARIABLE }}
      run: |
        poetry run pytest
```

- Commit the changes and push to GitHub
- Go back to GitHub and see if the checks are passing
- Merge the pull request

__Learning Objectives:__
- Using `.env` files locally with secret variables
- Using GitHub secrets remotely
- Updating GitHub Actions to use secrets

More on secrets [https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)

## Closing

Our company, Hypergolic [https://hypergolic.ai/training](https://hypergolic.ai/training), provides extensive training for Data Scientists on structuring Machine Learning projects and maintaining readable and clean code through hands-on exercises. The material here is a small part of our course.

Contents:

- Clean Architecture
- Design Patterns
- Testing
- Readability
- Code Smells
- Refactoring
- Model deployment with FastAPI

The course is held through a set of Zoom sessions in small groups, and exercises are conducted on GitHub Codespaces.

Contact us at [contact@hypergolic.ai](mailto:contact@hypergolic.ai) for more details.