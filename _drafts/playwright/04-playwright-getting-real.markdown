---
title:  "0x40: Playwright - Getting Real"
tag: playwright
---

Running only a hello-world is no fun. I'll try to create and run tests using `playwright` for an application with real functions.


### The application
There is a [Github repository](https://github.com/gothinkster/realworld) that implement a basic blog app using all kinds of frameworks. To keep things simple, I forked the fullstack implementation using `Django + Unpoly` and will use it as the testing target.

This application can be run following the instructions on the Github page and it is straightforward. The only requirement is to get Python 3 installed.

The feature and spec can be found at the [documentation of the real world example](https://realworld-docs.netlify.app/docs/intro). Basically, it allows users to create blogs, with ability to manage users and posts.

### Test cases
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

__Pseudo classes__ can be used in CSS selectors so that the `text`, `visible`, and `nth` selector engine can blend in as pseudo classes. Other pseudo classes are also added to enhance the ability of CSS selectors.
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

Different selectors can be chained in one string using the `>>` token. It can prefix a `*` in front of a selector in the string to resolve to the element of that selector in stead of the last selector.

#### Writing the first test case
First of all, let's remove the hello-world test case. It will not be used anymore. Based on the simple feature description of this app [here](https://realworld-docs.netlify.app/docs/implementation-creation/features), I'll start by testing the user signing up and logging in/out function first, which is the first step of any other actions.

_the first test using playwright directly_
#### More tests, and get organized
_add more tests, and group tests into different files_
### Configurations
_global setup, configurations, reports, etc._

### View test result

### Things learned
- selector
- test suite organization
- configurations in general
- test reports

### The project
Project under testing is [here](https://github.com/wei-y/realworld). Find the test project so far in [this repo](https://github.com/wei-y/test-playwright-js).

```bash
git chekcout 0.03 -b 0.03
```
