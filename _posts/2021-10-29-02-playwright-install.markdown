---
title:  "0x02: Playwright - Installation"
tag: playwright
---

I'll try `Playwright` in both Javascript and Python flavour. The library installed and how they are used are different due to the feature of the language and what is available in there, but generally it's pretty straightforward to install `Playwright`.


## Installing in Javascript

### Preparing

Install [node LTS version](https://nodejs.org/en/download/) and keep it up to date.

### Installing
First, create a folder for the test project, and go into the folder, run `npm init -y` to initialize the porject with all default settings.

```bash
mkdir test-playwright-js
cd test-playwright-js
npm init -y
```

In the folder, we can install `@playwright/test` now.

```bash
npm i @playwright/test
```

There's one more step. Let's use the installed `@playwright/test` to install browsers.

```bash
npx playwright install
```

Not necessarily, but will be used later, let's install the `playwright` library as well.

```bash
npm i playwright
```

Now that `playwright` is installed, test cases are ready to be created.

### Notes

#### What's the differences between `@playwright/test` and `playwright`?

  > `@playwright/test` is test runner similar to Mocha, while `playwright` is the browser automation library. The test runner is built with `playwright` in mind, which can be accessed from the fixture parameter in the test function defined in test cases. On the other hand, the `playwright` library can be used without the test runner, allowing users to choose a different test framework. The `@playwright/test` runner provides works more closely with the automation library. It is a natual choice as test runner unless there is a good reason not to.

#### Why installing browsers by `playwright`?

  > `playwright` handles the browser version and compatibility by itself, bypassing the already installed browser. The details can be found [here](https://playwright.dev/docs/1.15/cli#install-browsers). I guess the reason here is that it want to keep the browser simple and under control. There is another option to use the default Chrome or Edge installed on the machine. Details are [here](https://playwright.dev/docs/1.15/browsers#google-chrome--microsoft-edge)<br>
  > An interesting thing here is that if you go to the directory where the browser is downloaded, you will not find webdrivers. Actually `playwright` uses Dev Tools protocol in stead of Webdriver protocol giving it much more control over browsers.

#### Where is the _-D_ option?

  > The `-D` option is used to add a development dependency which will be installed in development mode but skipped in production. Different than the tutorial, I didn't add the `-D` option to the install command because I am using a standalone project for the test automation. If the test automation is part of the project to be tested, it is necessary to add the `-D` option to keep it only installed in development mode.

### The project

The example project following the steps in this and following posts can be found in [this repo](https://github.com/wei-y/test-playwright-js). The steps in this post is tagged as `0.01`. Use the command below to checkout the change so far.

```bash
git chekcout 0.01 -b 0.01
```

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

#### Why is there no equivalent to `@playwright/test` as in Javascript?

  > The Mocha style test cases organization is Javascript convention, not Python. In Python world, tests are organized in the way of unittest, and `pytest` is a de facto standard of organizing tests. It has a active and rich plugin ecosystem so instead of inventing a new wheel, `playwright` choose to implement a plugin to work with `pytest`.

#### What's the role of each package installed?

  > `playwright` is the automation library for automating browser, `pytest` is the test runner, and `pytest-playwright` is the plugin to `pytest` to expost browser automation to the runner. In the example later we will see that `pytest` got a `page` parameter to test cases representing the `playwright` page object.

### The project

The example project following the steps in this and following posts can be found in [this repo](https://github.com/wei-y/test-playwright-python). The steps in this post is tagged as `0.01`. Use the command below to checkout the change so far.

```bash
git chekcout 0.01 -b 0.01
```
