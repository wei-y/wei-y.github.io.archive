---
title:  "0x10: Playwright - What and Why"
tag: playwright
---
For some reason I've missed [Playwright](https://playwright.dev/) which looks like an all-in-one solution to automation. I'll start a project from installing to test the product in pipeline, and write down all the steps I used.


In my opinion, an ideal automation framework should have the following features:
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

By reading the documents of `Playwright`, it seems all of these are supported, and also something beyond: browser controlling, authentication, cross-platform, distributed running, etc.

The features of `Playwright` I am interested in are:
  - `Mocha` style test management with hooks;
  - Expanded assertions from `jest`;
  - Many helping tools for recording, debugging and analyzing;
  - Flexible selector system;
  - Built-in screenshot and video support;
  - Easy to apply page object pattern;
  - Automation beyond DOM, i.e. browser and network, etc.

`Playwright` is a libray [supporting multiple languages](https://playwright.dev/docs/languages). I'll be focusing on Javascript in this project.