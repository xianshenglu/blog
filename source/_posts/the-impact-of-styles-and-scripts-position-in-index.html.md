---
title: The Impact of Styles and Scripts Position in index.html
categories: browser
tags:
  - DOM
  - Chrome
comments: true
date: 2020-03-17 21:42:02
---

## Preface

For a long time, I was thinking if the _.css_ files would block DOM parsing and rendering. It seems I got different results sometimes. So, I decided to test and summarize the behaviors carefully with chrome.

Let's begin.

### Put _.css_ Files in the `<head>`

Normally, we would put the _.css_ files in the head and _.js_ files at the bottom of `<body>`. For example,

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <link rel="stylesheet" href="./static/header.css" />
    <link rel="stylesheet" href="./static/main.css" />
  </head>
  <body>
    <div>MyApp</div>
    <header>
      Header
    </header>
    <main>
      Main
    </main>
    <footer>Footer</footer>
    <script src="./static/app.js"></script>
  </body>
</html>
```

Say we blocked the _.css_ files request by fiddler. And here are the questions,

- Would _header.css_ block DOM parsing and rendering?
- Would _header.css_ block _main.css_, _app.js_ and other resources fetching?

And here are the results.

![](../images/1584456970837.gif)

- _header.css_ won't block DOM parsing.
- _header.css_, _main.css_ all blocked rendering which means **the page would be always blank until all the _.css_ files request finished.**
- _header.css_ won't block _main.css_, _app.js_ and other resources fetching. Though the codes in the resources won't be executed in advance!

### Put _.css_ Files in the `<body>`

It is not very common to see this usage. However, test is still needed if we want to know more. Consider the following code,

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

Say I still block all the _.css_ files with fiddler. What would the results be?

![](../images/1584452497349.gif)

As you can see,

- _header.css_ **blocked DOM parsing**, hence blocked render.
- _header.css_ didn't block other files fetching and _app.js_ is still not executed in advance!
- After _header.css_ was loaded, the DOM parsing keeps working. Then blocked by _main.css_ again.
- After _main.css_ was loaded, _app.js_ is executed!

Compared with the first example, we can find the difference is that

- _.css_ files in the `<body>` would block DOM parsing while _.css_ files in the `<head>` wouldn't.

Hence, if we put the styles in the `<head>`, we can make use of the time of styles downloading and parsing
to parse DOM. That's why developers always put the styles in the `<head>`.

_Actually, there are some cases that we might need to put the styles in the body. For example, to parse and render page part by part._

### Put _.js_ Files in the `<head>` or `<body>`

Actually, it works like styles in `<body>` where we put it in the `<head>` or `<body>`. The script without `async` and `deferred` would block DOM parsing and rendering. Actually, it is specified in W3C specifications.

> For classic scripts, if the async attribute is present, then the classic script will be fetched in parallel to parsing and evaluated as soon as it is available (potentially before parsing completes).
> If the async attribute is not present but the defer attribute is present, then the classic script will be fetched in parallel and evaluated when the page has finished parsing.
> If neither attribute is present, then the script is fetched and evaluated immediately, blocking parsing until these are both complete.

That's why it is common to see recommendations about putting the scripts at the bottom of `<body>`.

## End

The above tests are tested in windows7 Chrome 80.0.3987.106.

[**Source**](https://github.com/xianshenglu/blog/issues/103)
