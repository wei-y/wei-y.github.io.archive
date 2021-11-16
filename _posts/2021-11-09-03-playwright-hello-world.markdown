---
title:  "0x30: Playwright - Hello World!"
tag: playwright
---

With the packages installed in the last post, it's time to see how it runs. I'll use the example in the official tutorial with some slight changes. The test itself is sort of a hello-world: go to the Playwright website, read the title bar, and verify the string. I applied the `describe-test` idom to it, and experimented on how to debug test cases.


### The hello-world

The test in Javascript is in async mode. There is no choice to write it in sync mode. The test case is as follows.

```javascript
// ./tests/hello-world.spec.js
const { test, expect } = require("@playwright/test");

test.describe("hello world!", () => {
  test("from Playwright", async ({ page }) => {
    await page.goto("https://playwright.dev/");
    const title = page.locator(".navbar__inner .navbar__title");
    await expect(title).toHaveText("Playwright");
  });
});
```

Running the test is by calling the `playwright` executable. By default it uses Chromium in headless mode. There are options to start a different browser, or run the browser in headed mode.

```bash
npx playwright test
```

### Debug it...
#### Using Debugger

When running test cases with an extra option `--debug`, it will start the Playwright Inspector comes with the Playwright package. This GUI tool allows setting break point and step tracing test cases. Also it can be used to record browsing actions as script.

```bash
npx playwright test --debug
```

#### In VS Code

VS code can also be used as the editor/debugger of Playwright test cases. While editing in VS code is straightforward, debugging it is not so direct. The default debug mode in VS code for JS script is `node.js`, which cannot run the test cases here correctly.

To resolve this issue, we need to create a file `launch.json` for VS code to tell it how to run the test code.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "headed mode",
      "runtimeExecutable": "npx",
      "runtimeArgs": ["playwright", "test", "${relativeFileDirname}/${fileBasename}", "--headed"],
      "cwd": "${workspaceFolder}",
      "console": "integratedTerminal",
      "autoAttachChildProcesses": true,
      "env" : {"PWDEBUG":"console"},
      "skipFiles": [
          "<node_internals>/**"
      ],
    }
  ]
}
```

With these settings, the test can be run by pressing `F5`. Breakpoints can be added as normal.

### Things learned
- :pushpin: **Folder of test cases**  
Test case is saved in folder `tests` in this example. The current folder is the default place where Playwright` starts looking for test cases recursively. It can be set to a different place in configuration.
- :pushpin: **Test case file name**  
The name pattern `*.spec.js` is used to allow Playwright to pickup the test. By default, Playwright looks for est cases in files with name pattern `.*(test|spec)\.(js|ts|mjs)`. This pattern is configurable.
- :pushpin: **Not using `playwright` directly yet**  
It uses `@playwright/test` to manage test running and does not use `playwright` package directly. The API in playwright` is accessed via the `page` parameter (a test fixture in the terminology of Playwright) passed in to he test function
- :pushpin: **Asynchronous**  
It uses `async` to define the test function, and `await`each API calls on the `page` object and `expect()`
- :pushpin: **Assertions**  
Assertion is done by calling the `expect()` function in the `@playwright/test` package. It holds the same set f assertion functions as `Jest` with some expansions specifically for web testing. Most of the assertion in test ases should be using this function as well.
- :pushpin: **Debugging launch file**  
It replaces the default `program` with `runtimeExecutable` to allow running `npx` with `runtimeArgs` ontaining playwright command and arguments. There is a `--headed` parameter to run the browser in headed mode hen debugging

### The project

Find the project so far in [this repo](https://github.com/wei-y/test-playwright-js).

```bash
git chekcout 0.02 -b 0.02
```
