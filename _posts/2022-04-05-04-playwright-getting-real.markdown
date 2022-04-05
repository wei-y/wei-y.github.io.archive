---
title:  "0x40: Playwright - Getting Real"
tag: playwright
---

Running only a hello-world is no fun. I'll try to create and run tests using `playwright` for an application with real functions.


### The application
There is a [Github repository](https://github.com/gothinkster/realworld) that implement a basic blog app using all kinds of frameworks. To keep things simple, I forked the fullstack implementation using `Django + Unpoly` and will use it as the testing target.

This application can be run following the instructions on the Github page and it is straightforward. The only requirement is to get Python 3 installed.

The feature and spec can be found at the [documentation of the real world example](https://realworld-docs.netlify.app/docs/intro). Basically, it allows users to create blogs, with ability to manage users and posts.

To start the application, we need to have Python 3 installed, and then clone the repo and follow the instructions. 

First of all, clone the repo and set it to local mode. Change code in `./manage.py`, `./config/asgi.py`, and `./config/wsgi.py`
```python
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'realworld.config.settings.local')
```

And then run the following command to setup the environment.

```shell
# go to the folder
cd ./realworld
# create and activate the virtual environment
python3 -m venv env
source ./env/bin/activate
# install dependencies
pip install -r requirements/local.txt
# initial setup
python manage.py migrate
```

Now run the server, and the application is accessible at http://127.0.0.1:8000/.
```shell
# if you are not in the folder and the environment is not activated, run these
cd ./realworld
python3 -m venv env
source ./env/bin/activate
# start the server
python manage.py runserver
```

### Test case
#### Select elements in pages
Now we have a functional application running, we can start testing it using `Playwright`. But before starting, we should know how to locate elements on the page, which is slightly different in every frameworks. I found that the selectors in `Playwright` is quite powerful and concise. The details can be found in [its document](https://playwright.dev/docs/selectors), I'll only list selectorsin a table to summarize things.

__Selector engines__
| Type | Shadow DOM | Summary |
| ---- | ---------- | ------- |
| Text | yes | `text=substring`, `text="exact"`, `text=/regex/`. Find element by text substring, exact match, or regex. Has equivalent pseudo classes. |
| Visibility | N/A | `visible=true|false`, Find element by visibility. It has equivalent pseudo class |
| Attributes | yes | `<attr>=value`. Only support `id`, `data-test-id`, `data-testid`, `data-test`. |
| n-th element | ?? | `nth=index`. Select the nth-matching element with 0-based index. Support negative index to count backward |
| CSS | yes | `css=selector`. Standard CSS selectors of browser, can use pseudo classes in combination |
| XPath | no | `xpath=selector`. Standard XPath selector used in `Document.evaluate()` |
| React | N/A | `_react=reactSelector` |
| Vue | N/A | `_vue=vueSelector` |

__Pseudo classes__ can be used in CSS selectors so that the `text`, `visible`, and `nth` selector engine can blend in as pseudo classes when using CSS selector engine. Other pseudo classes are also added to enhance the ability of CSS selectors.
| Pseudo class | Summary |
| ------------ | ------- |
| `:has-text("substring")` | All elements containing the substring |
| `:text("substring")` | Smallest element containing the substring. Equivalent to `text=substring` |
| `:text-is("exact")` | Equivalent to `text="exact"` |
| `:text-matches("regex")` | Equivalent to `text=/regex/` |
| `:visible` | Equivalent to `visible=true` |
| `:has('selector')` | Element contains the specified element |
| `:right-of("selector")` | Element right to the speified element |
| `:left-of("selector")` | Element left to the speified element |
| `:above("selector")` | Element above the speified element |
| `:below("selector")` | Element below the speified element |
| `:near("selector")` | Element near the speified element withing 50 pixels |
| `:nth-match("selector", index)` | The nth-matching element. Index is 1-based |
| `:light` | Opt-out shadow DOM |

Different selectors can be chained in one string using the `>>` token. It can prefix a `*` in front of a selector in the string to resolve to the element of that selector in stead of the last selector. This article only demonstrates a simple case of these selectors. The explanation of selectors in detail is [here](https://playwright.dev/docs/selectors)

All of these selectors can be used in the function `page.locator()` to find the item. Once an element is located, we can interact with the element, e.g. `locator.click()`, `locator.type()`, `locator.dragdrop()`, etc. A full list can be found [here](https://playwright.dev/docs/api/class-locator). 

The `locator()` function returns an element **OR** an array of elements matching the selector. It is user's task to identify if it is one or multiple elements. In the case user wants only one element, but there are multiple matching, the selector should be changed to be more specific.

#### Verifying expectations
As mentioned in previous article, `Playwright` uses the Jest `expect` library for assertions. If you are familiar with `Jest` or `Chai`, there would be no difficult using this library.

Also, `Playwright` expands the library with a bunch of web related assertions, which is convenient in testing web applications. The library runs assertion repeatedly until the expectation is met or timeout. The timeout value can be passed in as a parameter, or configured in the configuration file, which will be covered later.

An example asserting visibility of an element:
```javascript
await expect(page.locator('text=Check this text')).toBeVisible();
```

Details of assertions are [here](https://playwright.dev/docs/test-assertions)

#### Writing the first test case
First of all, let's remove the hello-world test case. It will not be used anymore. Based on the simple feature description of this app [here](https://realworld-docs.netlify.app/docs/implementation-creation/features), I'll start by testing the user signing up and logging in/out function first, which is the first step of any other actions. I know it could be better to split these into separate tests to keep everything concise and independent, but to simplify things, I'll use one test case to cover all three actions. The procedure would be:

- go to the application URL
- click sign up and register a new user
  - click the `Sign up` button in the nav
  - fill the form with name, email, and password
  - click `Sign up` button in the form
- verify that user has been logged in
  - check that the user name button is displayed in nav
  - check that the `My article` tab is displayed
- log out
  - click the `Settings` button in the nav
  - click `Or click here to logout` button
- verify that user has been logged out
  - check that the user name button disappeared from the nav
  - check that the `My article` tab is not displayed
- login again
  - click the `Sign in` button
  - enter email and password
  - click the `Sign in` button
- verify that user has been logged in (same as after signing up)

There are a few pages involved here, and we are going to interact with elements on them:
- Home page: when user is not logged in, it displays `Sign in` and `Sign up` buttons without `My articles` tab; when user is logged in, it displays user profile link with user name, and also show the `My articles` button. These can be used to verify user is logged in or not;
- Register form: with user name, email, password, and submit button
- Login page: with user email, password, and submit button
- Settings page: user information and the log out button.

In the following table I'll list the elements involved in each step, the selector I used, and the reason I choose the selector.

| Page | Element | Selector | Description |
| ---- | ------- | -------- | ----------- |
| Home | Buttons in nav: <br> - `Sign up` <br> - `Sign in` <br> - `Settings` | - `css=nav .navbar-nav >> text=Sign up` <br> - `css=nav .navbar-nav >> text=Sign in` <br> - `css=nav .navbar-nav >> text=Settings` | First, use CSS to limit the selector to the `<nav>`, in case there is an article that has a title of the same text as these buttons. Then we use text selector to find the buttons. It is following the [best practice](https://playwright.dev/docs/selectors#prioritize-user-facing-attributes) suggestted by `Playwright` using user facing attributes (the text of the button here). The two selectors are chained together by `>>` sign. |
| | User profile button | `css=nav .nav-item >> text=${userName}` | It is the same concept above, only now the text dynamically equals to user name. A string template is used instead of a simple string |
| | `My articles` tab in main | `css=main .nav-item >> text=My Articles` | Similar to the `Sign up`, it is limited to the `.nav-item` in `<main>` and then selected by text |
| Sign up | Fields: <br> - user name, <br> - email, <br> - password, | - `id=id_name` <br> - `id=id_email` <br> - `id=id_password` | Also following the [best practice](https://playwright.dev/docs/selectors#define-explicit-contract), preferring using ID selector engine |
| | Submit button | `button[type=submit]` | Using CSS here because it uniquely identifies the element, and this particular selector is not likely to change |
| Settings | Logout button | `text=Or click here to logout` | This text is unique enough to be used directly |
| Sign in | Fields: <br> - email <br> - password | - `id=id_username` <br> - `id=id_password` | This page is pretty much the same as the sign up page, so the same reason choosing selectors |
| | Submit button | `button[type=submit]` | Same as in sign up page |

Now go back to each points to test we listed previously, to sign up a new user and check result, we could write code as the following. Other tasks are not listed here but are included in the project repo.

```javascript
const { test, expect } = require("@playwright/test");

test.describe("user management", () => {
  test("should sign up", async ({ page }) => {
    // open the application
    await page.goto("http://127.0.0.1:8000/");

    // click sign up, fill information, and submit
    await page.locator("css=nav .navbar-nav >> text=Sign up").click();
    await page.locator("id=id_name").type("Test User");
    await page.locator("id=id_email").type("test@user.com");
    await page.locator("id=id_password").type("testPassword");
    await page.locator("button[type=submit]").click();

    // verify user is signed in by checking existence of profile button and my articles tab
    await expect(page.locator(`css=nav .nav-item >> text=Test User`)).toBeVisible();
    await expect(page.locator("css=main .nav-item >> text=My Articles")).toBeVisible();
  }
}
```

#### Faking data
The test above works...only for once, because the user name and email are hard coded in the test case and running it again the application will complain about duplicated user. Of course we could manually delete the user in the database or even reset the DB before starting, but it seems to be a headover not directly contributes to testing automation.

What we want to do here is to use the `faker` library to generate fake data for us, so that it's unlikely to have conflict. In theory there still could be conflicts, but based on my experience it works well enough to keep the automation smooth.

To install the library, we simpliy use `npm`.
```shell
npm install @faker-js/faker
```

Once it is installed, we can use it in our script replacing the hard coded user data with generated fake data. Notice that the selector of user profile button in nav is replaced by a string template now.
```javascript
const { test, expect } = require("@playwright/test");
const { faker } = require("@faker-js/faker");


test.describe("user management", () => {
  test("should sign up", async ({ page }) => {
    // generate fake user details
    const userName = faker.name.findName();
    const userEmail = faker.internet.email();
    const userPassword = faker.datatype.string();

    // open the application
    await page.goto("http://127.0.0.1:8000/");

    // click sign up, fill information, and submit
    await page.locator("css=nav .navbar-nav >> text=Sign up").click();
    await page.locator("id=id_name").type(userName);
    await page.locator("id=id_email").type(userEmail);
    await page.locator("id=id_password").type(userPassword);
    await page.locator("button[type=submit]").click();

    // verify user is signed in by checking existence of profile button and my articles tab
    await expect(page.locator(`css=nav .nav-item >> text=${userName}`)).toBeVisible();
    await expect(page.locator("css=main .nav-item >> text=My Articles")).toBeVisible();
  }
}
```

Now our test can be run happily again and again without conflicts.

### View test result
With the test running now, a following question would be where can I see the test result. By default without any configurations, `Playwright` uses the "list" reporter, which prints a line for each test cases of its status.

`Playwright` provides a few built-in test reports that can be used, also, some third-party or customized reports can also be configured and used, such as Allure report. It also allow generating multiple reports in different formats. It would be useful in some scenarios, for example, unit test report for CI, and HTML report for human.

The built-in reports of `Playwright` are:
- list: the default reporter, pring a line for each test cases;
- line: print a line as the progress bar of execution, and list all failures after test run;
- dot: print a dot for each successful test case, and an "F" for each failed ones;
- HTML: an HTML report for human to read;
- JSON: export test result as JSON data;
- JUnit: JUnit test report in XML format.

To use a reporter other than "list", it can be specified in command line running the test, or in the configuration file.

The following command line runs the test and generates the HTML report, and another command serves the report for viewing (it's not a static HTML file)

```shell
# run test and generate HTML report
npx playwright test --reporter html
# serve the report
npx node-static playwright-report
```

### Things learned
- :pushpin: **Selectors of `Playwright` are powerful** 
There are a bunch of different selector engines and they are able to be chained together providing powerful ways locating elements. Note that the `locator()` function returns a Promise which is not resolved until used in action (which is usually awaited) or awaited directly.
- :pushpin: **`Playwright` uses `expect` of `Jest` as assertion library and expanded it**
The assertion library is straightforward and easy to use, especially with the expanded web related assertions. Assertions return Promise as well, so they needs to be awaited as well.
- :pushpin: **Use `faker` to generate fake data**
It would be a good idea to generate random data for testing, for one, it reduces conflicts, for another reason not mentioned, it slightly expands the test case by the random data generated.
- :pushpin: **Different flavors of reports**
Similar to other modern test frameworks, `Playwright` supports many reporters, and they are easy to be used in test.

### The project
- Application under testing is [here](https://github.com/wei-y/realworld). 
- Find this test project so far in [this repo](https://github.com/wei-y/test-playwright-js).

```bash
git chekcout 0.03 -b 0.03
```
