---
title: CSSOM Blocked DOM Parsing and Script Executing
categories: browser
tags:
  - CSSOM
  - DOM
comments: true
date: 2020-03-17 21:42:02
---

## Preface

For a long time, I was thinking if the _.css_ files would block DOM parsing and rendering. Recently, I got time to test the behaviors in the browser.

## Main

I tested it in Chrome 80.0.3987.106. And here is the test code:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div>MyApp</div>
    <link rel="stylesheet" href="./static/header.css" />
    <header>
      Header
    </header>
    <link rel="stylesheet" href="./static/main.css" />
    <main>
      Main
    </main>
    <footer>Footer</footer>
    <script src="./static/app.js"></script>
  </body>
</html>
```

I blocked all .css files with fiddler. The result can be seen in the below gif:

![](../images/1584452497349.gif)

As you can see,

- _header.css_ blocked DOM parsing.
- _header.css_ didn't block _app.js_ fetching. But _app.js_ is not executed!
- After _header.css_ was loaded, the DOM parsing keeps working. Then blocked by _main.css_.
- After _main.css_ was loaded, _app.js_ is executed!

So, we can say, the .css files would block DOM parsing but it doesn't matter that the resources are being fetched at the same time. Though the fetched files won't work.

And the conclusion is still right when I test with .js files. For example, with code below

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <script src="./static/app.js"></script>
    <div>MyApp</div>
    <link rel="stylesheet" href="./static/header.css" />
    <header>
      Header
    </header>
    <link rel="stylesheet" href="./static/main.css" />
    <main>
      Main
    </main>
    <footer>Footer</footer>
  </body>
</html>
```

Here I blocked _app.js_.

Hence the DOM parsing is blocked. However, the .css files are still got fetched though they don't work until the _app.js_ is executed.

[**Source**](https://github.com/xianshenglu/blog/issues/103)
