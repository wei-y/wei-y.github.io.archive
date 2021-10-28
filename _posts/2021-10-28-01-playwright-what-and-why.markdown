---
layout: post
title:  "0x01: Playwright - what and why"
date:   2021-10-28 11:47:36 +1030
tag: playwright
---
I was doing web app automation test for a few years, and used libaries from Selenium to Webdriver.io. Recently I just found out that I've missed [Playwright](https://playwright.dev/) which looks like an all-in-one solution to automation.
<!--more-->

An ideal automation testing tool, in my opinion, should have the following features:
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

<br>

By reading the documents of `Playwright`, it looks like all of these are supported. Beyond them, there are other interesting features as well, like browser controlling, authentication, cross-platform, distributed running, etc. I would like to have a try of this framework and record my process here step by step. Hopefully, it would prove my first impression of `Playwright` is correct.

The features of `Playwright` that I am interested are:
  - `Mocha` style test management with hooks;
  - Expanded assertions from `jest`;
  - Many helping tools for recording, debugging and analyzing;
  - Flexible selector system;
  - Built-in screenshot and video support;
  - Easy to apply page object pattern;
  - Automation beyond DOM, i.e. browser and network, etc.

<br>

I'll try to cover all of these features and maybe other part as well during my trial of this tool.

[Go Back](/)