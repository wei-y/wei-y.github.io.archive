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
Generaully, page object defines each page as a class, and add locators as its attributes that can be used in page actions. First of all, we needs to define a class for each page. In the previous article, our test cases used 4 pages, so 4 classes are needed.

```javascript
class HomePage {}
class RegisterPage {}
class LoginPage {}
class SettingsPage {}
```

We can add all elements we are going to be interacting with to each page directly, but observing the structure of pages, we can find out that they share a similar structure. All pages have a main nav on the top, only the main content is different. We can define a base class for all of them so that they can share the common elements in one place.

Within `Playwright`, `page.locator()` returns a promise, which will be resolved at accessing time. Utilizing this fact, elements locators can be simply defined in class constructor. We will also keep a reference to the page itself for convenience.

Another trick here is that I defined the collection of elements of both unsigned and logged in page in the main nav bar in the base class, so that we don't have to define two different bases.


```javascript
class BasePage {
    constructor(page) {
        this.page = page;
        // nav bar. Notice that the profile is a function with user name as argument
        this.signUp = page.locator("css=nav .navbar-nav >> text=Sign up");
        this.signIn = page.locator("css=nav .navbar-nav >> text=Sign in");
        this.settings = page.locator("css=nav .nav-item >> text=Settings");
        this.home = page.locator("css=nav .nav-item >> text=Home");
        this.userProfile = userName => page.locator(`css=nav .nav-item >> text=${userName}`);
    }
}

class HomePage extends BasePage {}
class RegisterPage extends BasePage {}
class LoginPage extends BasePage {}
class SettingsPage extends BasePage {}

```

Now we don't have to repeat the same code for the main nav in each page, only need to add what is necessary to each page. Adding the remaining elements listed in the previous article, the code would look like the following (only the register page is demonstrated, all others are omitted and can be found in the project repo).

```javascript
class BasePage { 
    //...the definition above...
}

class RegisterPage extends BasePage {
    constructor(page) {
        super(page);
        this.name = page.locator("id=id_name");
        this.email = page.locator("id=id_email");
        this.password = page.locator("id=id_password");
        this.submit = page.locator("button[type=submit]");
    }
}
```

Now that the page has all elements we would access, we can add the behavior particularly of this page to the class as well, which is registering a new user. We did not define each field as a seperate parameter of the functionbut used an object containing all fields. In the case that the register form changes, we don't have to change the function interface, only add new properties to the single parameter. Notice that each interaction is asyncronous and should be called with `await`.

```javascript
class BasePage { /*...omitted...*/ }
class RegisterPage extends BasePage {
    constructor(page) { /* ...omitted... */ }
    await registerUser(attrs) {
        await this.name.type(attrs.name);
        await this.email.type(attrs.email);
        await this.password.type(attrs.password);
        await this.submit.click();
    }
}
```

We can keep adding elements and behaviors to other pages. As the concept is already demonstrated, I will not list everything here, instead they can be found in the project repo in Github.


### Components

page component: why and pattern
event hook: wait on loading, and more
one more step: service layer, why, how
refactor and more test cases