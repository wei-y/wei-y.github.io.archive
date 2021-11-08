---
title:  "0x03: Playwright - Hello World!"
tag: playwright
---

With the packages installed in the last post, we can start looking at how to run `Playwright` for testing. To keep it simple, I'll just follow the official tutorial by checking the Playwright website, to see how it works in both Javascript and Python.


The test itself is pretty simple: go to the Playwright website, read the title bar, and verify the string. It can be viewed as a hello-world test case and there is not too many things to say about it. I'll also use it as a chance to explore how to debug test cases in VS code and other places.

### The hello-world

The test in Javascript is in async mode, which means test function needs the `async` keyword, and calling Playwright API needs `await`. There is no choice to write it in sync mode. The test case is as follows.

```javascript
const { test, expect } = require('@playwright/test');

test('basic test', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  const title = page.locator('.navbar__inner .navbar__title');
  await expect(title).toHaveText('Playwright');
});
```

It uses `@playwright/test` to manage test running and does not use `playwright` package directly. The API in the latter is exposed and used via the `page` parameter (a test fixture in the terminology of Playwright) passed in to the test function. Notice how it uses `async` to define the test function, and `await` each API calls one the `page` object and `expect()`

The file is saved in `tests/hello-world.spec.js`. The folder is the place where all test cases are saved. It can be customized to a different place by configuration. <<TODO: what is the default location??>>

Assertion is done by calling the `expect()` function in the `@playwright/test` package. It holds the same set of assertion functions as `Jest` with some expansions specifically for web testing. Most of the assertion in test cases should be using this function as well.

Running the test is by calling the `playwright` executable. By default it uses Chromium in headless mode. There are options to start a different browser, or run the browser in headed mode.

```bash
npx playwright test
```

### Debug it
#### Debugger

When running test cases with an extra option `--debug`, it will start the Playwright Inspector comes with the Playwright package. This GUI tool allows setting break point and step tracing test cases. Also it can be used to record browsing actions as script.

```bash
npx playwright test --debug
```

#### VS Code

VS code can also be used as the editor/debugger of Playwright test cases. While editing in VS code is straightforward, debugging it is not so direct. The default debug mode in VS code for JS script is node.js, which cannot run the test cases here correctly. We need to find a way to run tests by calling `playwright`. The steps are as follows.

1. generate launch.json
2. modify the content, call npx, playwright with correct parameters
3. run test with the setting
4. enable debug mode
5. debugger keyword
