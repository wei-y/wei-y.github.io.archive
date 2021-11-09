---
title:  "0x30: Playwright - Hello World!"
tag: playwright
---

With the packages installed in the last post, we can start looking at how to run `Playwright` for testing. To keep it simple, I'll just follow the official tutorial by checking the Playwright website, to see how it works in both Javascript and Python.


The test itself is pretty simple: go to the Playwright website, read the title bar, and verify the string. It can be viewed as a hello-world test case and there is not too many things to say about it. I'll also use it as a chance to explore how to debug test cases in VS code and other places.

### The hello-world

The test in Javascript is in async mode, which means test function needs the `async` keyword, and calling Playwright API needs `await`. There is no choice to write it in sync mode. The test case is as follows.

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

**Notes**

- :pushpin: Test case is saved in folder `tests`. The current folder is the default place where `Playwright` looks for test cases, but it can be set to a different place in configuration.
- :pushpin: The name pattern `*.spec.js` is needed to allow Playwright to pickup the test. By default, Playwright looks for test cases in file named in pattern `.*(test|spec)\.(js|ts|mjs)`. This pattern is also configurable.
- :pushpin: It uses `@playwright/test` to manage test running and does not use `playwright` package directly.
- :pushpin: The API in `playwright` is accessed via the `page` parameter (a test fixture in the terminology of Playwright) passed in to the test function.
- :pushpin: It uses `async` to define the test function, and `await`each API calls on the `page` object and `expect()`
- :pushpin: Assertion is done by calling the `expect()` function in the `@playwright/test` package. It holds the same set of assertion functions as `Jest` with some expansions specifically for web testing. Most of the assertion in test cases should be using this function as well.
{: .notice--info}

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

With these settings, we can simply press `F5` to start testing and add breakpoint to the test code to debug.

**Notes**
- :pushpin: The type is `node` because we still run the test using node.js
- :pushpin: The `program` line is deleted for we want to use `npx` to start testing, which in in the `runtimeExecutable`
- :pushpin: The `runtimeArgs` collects all parameters used to run the test, notice the `--headed` there which opens the browser in headed mode, allowing interactions to be visible, making it easier for debugging
- :pushpin: If the option is changed from `--headed` to `--debug`, it will start the Playwright Inspector and then we can debug/record script in that tool. If this is needed, we can create another configuration in the `configurations` list, make the change, and give it a different name. Both configurations will be avialble to choose in the debug side bar
- :pushpin: We can also use the keyword `debugger` to pause code execution and start debugging from that line
{: .notice--info}

### The project

Find the project so far in [this repo](https://github.com/wei-y/test-playwright-js).

```bash
git chekcout 0.02 -b 0.02
```
