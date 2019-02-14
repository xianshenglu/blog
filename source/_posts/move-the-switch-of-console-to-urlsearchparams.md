---
title: Move the Switch of Console to URLSearchParams
categories: debug
tags:
  - console.log
  - URLSearchParams
comments: true
date: 2018-10-05 09:31:50
---

## Preface

It is very common for us to use `console.log` When we are in a development environment. So, how do you handle this when you are preparing to deploy it to server?

In the past, I chose to delete those debug logs. However, I guess nobody likes doing that and in some place, I really want to keep that.

## Main

One day, I find a better way to handle this. Before the main js code, I add code below:

```js
let urlSearchParams = new URLSearchParams(window.location.search)
let isDebug = urlSearchParams.get('debug') === '1'
if (!isDebug) {
  console.log = function() {}
}
```

## Ending

## Reference
