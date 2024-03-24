---
author: Timo Bickmann
pubDatetime: 2024-03-24T12:51:33.000+01:00
modDatetime:
title: Write a Vanilla JS SPA Router
slug: write-a-vanilla-js-router
featured: false
draft: false
tags:
  - javascript
description: How to create a simple Vanilla JS router for a Single Page Application (SPA).
---

This guide explains how to create a simple Vanilla JS router for a Single Page Application (SPA), based on the `router.js` provided in the project.

## Table of contents

## Overview

A router in a SPA is responsible for handling navigation without the need to reload the entire page. It listens to changes in the URL and renders the corresponding content dynamically.

## Step 1: Install http server

To run the SPA, you need a local server. You can use the `live-server` package from npm to serve the files. Setup your index.html as the entry point for the SPA.

```json
// package.json
"scripts": {
    "start": "live-server --entry-file=index.html"
  }
```

## Step 2: Setup Page Modules

First, create JavaScript files for each page in your SPA, like `home.js`, `contact.js`, etc. Each file should export a function that returns the HTML content for that page.

```javascript
// pages/home.js
export function home() {
  return `<h1>Home Page</h1><p>Welcome to the home page.</p>`;
}

// pages/contact.js
export function contact() {
  return `<h1>Contact Us</h1><p>Contact us at example@example.com.</p>`;
}
```

## Step 3: Define Routes

In your `router.js`, import the page modules and map pathnames to their respective functions using an object.

```javascript
import { home } from "./pages/home.js";
import { contact } from "./pages/contact.js";
import { about } from "./pages/about.js";
import { notFound } from "./pages/notFound.js";

const routes = {
  "/": home(),
  "/about": about(),
  "/contact": contact(),
};
```

## Step 4: Handle Navigation

Implement a function to handle navigation. This function should check the current URL and render the corresponding page content.

```javascript
export function renderCurrentPage(pathname) {
  if (pathname === "/index.html") {
    document.querySelector("main").innerHTML = home();
    return;
  }
  if (routes[pathname] === undefined) {
    document.querySelector("main").innerHTML = notFound(pathname);
    return;
  }
  document.querySelector("main").innerHTML = routes[pathname];
}
```

## Step 5: HTML Markup

In your `index.html`, add a root div and layout your app inside the `app.js`.

```html
<body>
  <div id="root"></div>
</body>
```

```javascript
// app.js
document.querySelector("#root").innerHTML = `
${nav()}
<main></main>`;
```

## Step 6: Navigation Links

Create navigation links in your `nav.js` file and attach event listeners to them. Use the `history.pushState` method to update the URL without reloading the page.

```javascript
// nav.js
import { renderCurrentPage } from "./router.js";

export function nav() {
  return`
<nav>
  <li>
    <a href="/">Home</a>
  </li>
  <li>
    <a href="/about">About</a>
  </li>
  <li>
    <a href="/contact">Contact</a>
  </li>
  <li>
    <a href="/not-existing-page">Not existing Page</a>
  </li>
</nav>
`;
}

function onNavigate(pathname) {
  window.history.pushState({}, pathname, window.location.origin + pathname);
  renderCurrentPage(pathname);
}

export function attachNavEvents() {
  const navElem = document.querySelector("nav");
  const navElemLinkElems = navElem.querySelectorAll("a");

  navElemLinkElems.forEach((linkElem) => {
    linkElem.addEventListener("click", (event) => {
      event.preventDefault();
      onNavigate(linkElem.getAttribute("href"));
    });
  });
}
```

## Step 7: Bind to Browser Events

Use browser events to detect navigation changes and call `renderCurrentPage` accordingly. Bind to the `popstate` event for browser navigation (back/forward) and call your function to attach navigation links.

```javascript
// app.js
attachNavEvents();

window.onpopstate = () => {
  renderCurrentPage(window.location.pathname);
};
```

## Step 8: Initialize Router

When the page loads, call `renderCurrentPage` with the current URL to render the initial page.

```javascript
document.addEventListener("DOMContentLoaded", () => {
  renderCurrentPage(window.location.pathname);
});
```

## Conclusion

Following these steps, you can build a SPA with Vanilla JavaScript, handling navigation dynamically without full page reloads, improving the user experience. For the full code, check out my [GitHub repository](https://github.com/timobickmann/vanilla-js-spa).
