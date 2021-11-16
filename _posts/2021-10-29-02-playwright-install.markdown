---
title:  "0x20: Playwright - Installation"
tag: playwright
---

`Playwright` is installed by using the standard `npm` or other package management tool. There are not too much happening here, but it still deserves some explaination.

### Preparing

Install [node LTS version](https://nodejs.org/en/download/) and keep it up to date.

### Installing
**Initialize a project**
```bash
mkdir test-playwright-js
cd test-playwright-js
npm init -y
```

**Install Playwright**
```bash
npm i @playwright/test
npx playwright install
npm i playwright
```

The last command is not necessary at the moment but will be useful if it is installed. Now that `playwright` is ready, test cases can be created.

### Things learned
- :pushpin: **`@playwright/test` and `playwright` are not the same**  
`@playwright/test` is test runner similar to Mocha, while `playwright` is the browser automation library. The utomation library is accessible from `@playwright/test` via the test fixture `page`, which exposes the majority f test automation API. It could use a different test framework like `Mocha` with the core `playwright` only.
- :pushpin: **Browsers installed by Playwright**  
`playwright` handles the browser version and compatibility by itself, bypassing the already installed browser. he details can be found [here](https://playwright.dev/docs/1.15/cli#install-browsers). There is nother option to use the default Chrome or Edge installed on the machine. Details are [here](https://playwright.ev/docs/1.15/browsers#google-chrome--microsoft-edge)
- :pushpin: **No webdriver**  
Checking the place where browsers are installed, there is no webdrivers in there. `Playwright` uses Dev Tools rotocol in stead of Webdriver protocol allowing more control over browsers

### The project

The example project following the steps in this and following posts can be found in [this repo](https://github.com/wei-y/test-playwright-js). The steps in this post is tagged as `0.01`. Use the command below to checkout the change so far.

```bash
git chekcout 0.01 -b 0.01
```
