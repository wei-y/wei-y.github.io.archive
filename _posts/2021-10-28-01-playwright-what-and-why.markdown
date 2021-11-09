---
title:  "0x10: Playwright - What and Why"
tag: playwright
---
I was doing web app automation test for a few years, and used libaries from Selenium to Webdriver.io. Recently I just found out that I've missed [Playwright](https://playwright.dev/) which looks like an all-in-one solution to automation. I'll start a project from installing to test the product in pipeline, and write down all the steps I used.


Before I start, I want to explain the ideal automation testing tool in my opinion. It should have the following features:
  - support Page Object pattern, or very easy to build it;
  - powerful element selectors, not only raw CSS and XPATH;
  - test framework support, e.g. Mocha style;
  - plenty assertion methods;
  - static and rich style test reports;
  - running tests in parallel;
  - screenshot capturing, comparison, and video recording;
  - test failure retry;
  - easy to debug;
  - and maybe more...

By reading the documents of `Playwright`, it looks like all of these are supported. Beyond them, there are other interesting features as well, like browser controlling, authentication, cross-platform, distributed running, etc.

The features of `Playwright` that I am interested are:
  - `Mocha` style test management with hooks;
  - Expanded assertions from `jest`;
  - Many helping tools for recording, debugging and analyzing;
  - Flexible selector system;
  - Built-in screenshot and video support;
  - Easy to apply page object pattern;
  - Automation beyond DOM, i.e. browser and network, etc.

`Playwright` is a libray [supporting multiple languages](https://playwright.dev/docs/languages). I'll focus on Javascript in this project.