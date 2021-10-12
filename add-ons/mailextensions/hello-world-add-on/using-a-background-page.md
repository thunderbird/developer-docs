---
description: Extending the simple example extension to use a backgroud page.
---

# Using a Background Page

In this third section of the Hello World Example, we will introduce the concept of the WebExtension background page.

## Introduction

In the first two sections of the Hello World Example, we used well-defined UI hooks to load HTML pages when the user opened one of our popups. Those HTML pages support the same technologies as normal web pages (HTML, CSS, JavaScript, Web APIs) and additionally WebExtension APIs to interact with Thunderbird. 

In contrast to the fact that those HTML pages are only loaded after the user has initiated an action, the background page - if defined - is loaded automatically when the add-on is loaded during Thunderbird start or after the add-on has been manually enabled or installed. The background page is automatically destroyed when the add-on is shutting down.

### Background Scripts vs Background Page

The background page is a standard HTML page, but it is never shown to the user. Its main purpose is to load one or more JavaScript files. As [described in the MailExtension guide](../#background-page), there are two ways to do that:

1. Actually defining a background HTML page, that uses `script` tags to load the JavaScript files.
2. Just defining the to-be-loaded JavaScript files and let Thunderbird create a background page on-the-fly.

The author of this example prefers the first option, as it allows declaring the loaded JavaScript file (in the script tag) as `type="module"`, which enables support for [using ES6 modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules). 
