---
title:  "0x50: Playwright - Page Object Pattern"
tag: playwright
---

With the knowledge of the last article, it is all right to continue expanding tests applying the same pattern. However, everything needs to be repeated in each pages and test cases making it redundant to write tests. Page object is there to help resolve these issues.


### Issues without abstracting

There are a few issues if all test cases are defined from scratch, by naming each locator of each element again and again. In details, a few issues are listed as follows

- It needs to locate elements all the time in test cases, making it not only verbose, but also hard to adpapt to changes in the application. 
- It repeats actions as well. The login action used in the last article would be used again and again in test cases, repeating it makes it redundant.
- Many pages share similar structure. It has to be repeated in each page if there is not a way to define common components.

Page Object pattern is there to resolve these issues. Generally it provides a way to organize pages into objects and reuse elements, components, and logics. Applying page object pattern can improve the robustness of test cases against application changes.

### Defining page objects
Generally, page object defines each page as a class, and add locators as its attributes that can be used in page actions. First of all, we needs to define a class for each page. In the previous article, our test cases used 4 pages, so 4 classes are needed.

First of all, we create a new folder called `pages` to save the page definitions. For the moment we save everything in a file called `app.pages.js`.

```javascript
// app.pages.js
class HomePage {}
class RegisterPage {}
class LoginPage {}
class SettingsPage {}
```

We can add all elements we are going to be interacting with to each page directly, but observing the structure of pages, we can find out that they share a similar structure. All pages have a main nav on the top, only the main content is different. We can define a base class for all of them so that they can share the common elements in one place.

Within `Playwright`, `page.locator()` returns a promise, which will be resolved at accessing time. Utilizing this fact, elements locators can be simply defined in class constructor as attributes. We will also keep a reference to the page itself for convenience.

Another trick here is that I defined the collection of elements of both unsigned and logged in page in the main nav bar in the base class, so that we don't have to define two different bases.

I used the `$` in variable names to identify locators.


```javascript
class BasePage {
    constructor(page) {
        // reference to the page
        this.page = page;

        // nav bar.
        // Notice that the profile is a function with user name as argument
        this.$signUp = page.locator("css=nav .navbar-nav >> text=Sign up");
        this.$signIn = page.locator("css=nav .navbar-nav >> text=Sign in");
        this.$settings = page.locator("css=nav .nav-item >> text=Settings");
        this.$home = page.locator("css=nav .nav-item >> text=Home");
        this.$userProfile = userName => page.locator(`css=nav .nav-item >> text=${userName}`);
    }
}

// all other pages extends the base
class HomePage extends BasePage {}
class RegisterPage extends BasePage {}
class LoginPage extends BasePage {}
class SettingsPage extends BasePage {}

```

Now we don't have to repeat the same code for the main nav in each page, only need to add what is necessary. Adding the remaining elements listed in the previous article, the code would look like the following (only the register page is demonstrated, all others are omitted and can be found in the project repo).

```javascript
class BasePage { /*...omitted... */ }

class RegisterPage extends BasePage {
    constructor(page) {
        super(page);
        this.$name = page.locator("id=id_name");
        this.$email = page.locator("id=id_email");
        this.$password = page.locator("id=id_password");
        this.$submit = page.locator("button[type=submit]");
    }
}
```

Now that the page has all elements we would access, we can add the behavior particularly of the page to the class as well, which is registering a new user in the case of the `RegisterPage`. We did not define each registrering fields as a seperate parameter of the function but used an object containing all fields. In the case that the register form changes, we don't have to change the function interface, only add new properties to the parameter. Notice that each interaction is asyncronous and should be called with `await`.

```javascript
class BasePage { /*...omitted...*/ }

class RegisterPage extends BasePage {
    constructor(page) { /* ...omitted... */ }

    async registerUser(attrs) {
        await this.$name.type(attrs.name);
        await this.$email.type(attrs.email);
        await this.$password.type(attrs.password);
        await this.$submit.click();
    }
}
```

We can keep adding elements and behaviors to other pages. As the concept is already demonstrated, I will not list everything here, instead they can be found in the project repo in Github.


### Components
So far we have centralized element locators and page behaviour so that they can be reused without repeating. There is one more place we can refactor to make it more flexible. Actually, in the code above, there is already signs of redundancy implying further refactoring.

If we look closely at the `BasePage` class, we can see that all locators there shares a similar structure that all of them locates elements in the nav bar. Since the nav bar is only used in `BasePage` class, it is not necessary to abstract it to its own class, but suppose there is a group of elements being used in multiple pages, it might worth centralize the definition of that group into a component. We will define the nav bar as a component for demonstration, althout it is only used in one place.

Defining a page component is similar to defining a page object. The difference is that everything is located relatively to a locator which is the container of the component. We also save references to the page and the component container.

I also added a few small actions to the nav bar. In the case that the nav bar design changed, we can keep the interface accessing the nav bar stable (These actions are moved to the `BasePage` for convenience).

```javascript
class MainNavBar {
    constructor(page, locator) {
        // reference to the page and the component element
        this.page = page;
        this.$origin = locator;

        // locate elements in the component
        this.$signUp = this.$origin.locator("text=Sign up");
        this.$signIn = this.$origin.locator("text=Sign in");
        this.$settings = this.$origin.locator("text=Settings");
        this.$home = this.$origin.locator("text=Home");
        this.$userProfile = userName => this.$origin.locator(`text=${userName}`);
    }

    async toHome() { await this.$home.click(); }
    async toSignUp() { await this.$signUp.click(); }
    async toSignIn() { await this.$signIn.click(); }
    async toSettings() { await this.$settings.click(); }
    async toProfile(user) { await (this.$userProfile(user)).click(); }
}

class BasePage {
    constructor(page) {
        this.page = page;

        // we find the container of the nav bar and make a component object from it
        const $navBar = page.locator("css=nav .navbar-nav")
        this.mainNavBar = new MainNavBar(page, $navBar);
    }    
}

/* ... other code omitted... */

```

### Refactoring test cases
Now we have all the building blocks for rewriting test cases in page object pattern. The following code is refactored and it is more descriptive than befor which was full of locators drowning the real logic.

```javascript
const { test, expect } = require("@playwright/test");
const { faker } = require("@faker-js/faker");


test.describe("user management", () => {
  test("should sign up", async ({ page }) => {
    // generate fake user details
    const name = faker.name.findName();
    const email = faker.internet.email();
    const password = faker.datatype.string();

    // open the application and make a page object
    await page.goto("http://127.0.0.1:8000/");
    const homePage = new HomePage(page);

    // go to home register page
    await homePage.mainNavBar.toSignUp();
    const registerPage = new RegisterPage(page);
    
    // register a new user
    await registerPage.registerUser({name, email, password})

    // check result
    await expect(homePage.mainNavBar.$userProfile(name)).toBeVisible();
    // $myArticles is not defined in this post, 
    // add it yourself, or find it in the project repo in Github
    await expect(homePage.$myArticles).toBeVisible(); 
  })
})
```

### Things learned
- :pushpin: **Apply page object to build structral test cases**
Applying page object pattern can reduce code repetition and improve robustness. It abstract locators, behaviours, and components by organizing pages into classes.
- :pushpin: **Use page base class to keep general elements in one place**
In most apps, many pages share a similar layout structure. The common parts can be defined in a base class and each page extends it with their unique elements.
- :pushpin: **Organize reusable elements group into components**
If a group of elements are used in multiple places and serves as a component, they should be grouped as a component class.

### The project
- Application under testing is [here](https://github.com/wei-y/realworld). 
- Find this test project so far in [this repo](https://github.com/wei-y/test-playwright-js). Notice that the code in the repo is slightly different with the demonstration in this article.

```bash
git chekcout 0.04 -b 0.04
```
