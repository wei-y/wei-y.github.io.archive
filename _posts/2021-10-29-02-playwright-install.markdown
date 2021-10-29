---
layout: post
title:  "0x02: Playwright - Installation"
tag: playwright
---

I'll try `Playwright` in both Javascript and Python flavour. The library installed and how they are used are different due to the feature of the language and what is available in there, but generally it's pretty straightforward to install `Playwright`.

<!--more-->

## Installing in Javascript

### Preparing

Install [node LTS version](https://nodejs.org/en/download/)

### Installing
First, create a folder for the test project, and go into the folder

```shell
mkdir test-playwright-js
cd test-playwright-js
```

In the folder, we can install `@playwright/test`.

```shell
npm i @playwright/test
```

There's one more step. Let's use the installed `@playwright/test` to install browsers.

```shell
npx playwright install
```

Not necessary now, but will be used later, let's install the `playwright` library as well.

```shell
npm i playwright
```

### Notes

- What's the differences between `@playwright/test` and `playwright`

  > `@playwright/test` is test runner similar to Mocha, while `playwright` is the browser automation library. The test runner is built with `playwright` in mind, which can be accessed from the fixture parameter in the test function defined in test cases. On the other hand, the `playwright` library can be used without the test runner, allowing users to choose a different test framework. The `@playwright/test` runner provides works more closely with the automation library. It is a natual choice as test runner unless there is a good reason not to.

- Why installing browsers by `playwright`

  > `playwright` handles the browser version and compatibility by itself, bypassing the already installed browser. The details can be found [here](https://playwright.dev/docs/1.15/cli#install-browsers). I guess the reason here is that it want to keep the browser simple and under control. There is another option to use the default Chrome or Edge installed on the machine. Details are [here](https://playwright.dev/docs/1.15/browsers#google-chrome--microsoft-edge)

- Where is the _-D_ option?

  > Different than the tutorial, I didn't add the `-D` option to the install command because I am using a standalone project for the test. When the test automation is the part of the project to be tested, it is a better choice to add the `-D` option to keep it only installed in development mode.


## Installing in Python

### Preparing

First make sure Python is installed. There is no version requirement by `playwright`, so I'll just install the newest version from [here](https://www.python.org/downloads/).

To manage packages dependencies, I'll use `pipenv` as Python environment management tool. The detail on how to use it can be found [here](https://pipenv-fork.readthedocs.io/en/latest/)

### Installing

Similar as in Javascript, first create a folder for the project

Install `playwright` using `pipenv`

Install `pytest` as the test framework.

Install `pytest-playwright` plug-in for `pytest`

Install browsers

### Notes

- Why is there no equivalent to `@playwright/test`?

  > placeholder

- What's the role of each package installed?

  > place holder

[Go Back](/)