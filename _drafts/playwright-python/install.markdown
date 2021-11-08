
## Installing in Python

### Preparing

First make sure Python is installed. There is no version requirement by `playwright`, so I'll just install the newest version from [here](https://www.python.org/downloads/). Following the instructions there to get Python ready.

To manage packages dependencies, I'll use `pipenv` as Python environment management tool. The detail on how to use it can be found [here](https://pipenv-fork.readthedocs.io/en/latest/).

```bash
pip install --user pipenv
```

### Installing

First create a folder for the project.

```bash
mkdir test-playwright-py
cd test-playwright-py
```

In the directory created, install `playwright`, `pytest`, and `pytest-playwright` using `pipenv`. The installation will create a virtual environment automatically and separate the dependency from all other parts of the system.

```
pipenv install playwright pytest pytest-playwright
```

Now install browsers using `playwright`. We need to activate the virtual environment created in the previous step to get `playwright` command accessible. There is [a good reason](#why-installing-browsers-by-playwright) for it to install browsers by itself.

```
pipenv shell
playwright install
```

Now that `playwright` is installed, test cases are ready to be created.

### Notes

#### Why is there no `@playwright/test` as in Javascript?

  > The Mocha style test cases organization is Javascript convention, not Python's. In the Python world, tests are organized in the way of unittest, and `pytest` is a de facto standard of organizing tests. It has an active and rich plugin ecosystem so I guess instead of inventing a new wheel, `playwright` choose to implement a plugin to work with `pytest`.

#### What's the role of each package installed?

  > `playwright` is the automation library for automating browser, `pytest` is the test runner, and `pytest-playwright` is the plugin to `pytest` to expost browser automation objects (fixtures) to the runner. In the example later we will see that `pytest` got a `page` parameter to test cases representing the `playwright` page object, similar to the `@playwright/test` package in Javascript.

### The project

The example project following the steps in this and following posts can be found in [this repo](https://github.com/wei-y/test-playwright-python). The steps in this post is tagged as `0.01`. Use the command below to checkout the change so far.

```bash
git chekcout 0.01 -b 0.01
```
