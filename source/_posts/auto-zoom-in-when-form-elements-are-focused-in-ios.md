---
title: Auto Zoom in When Form Element Gains Focus in IOS
tags:
  - form
  - IOS
  - focus
categories: html
comments: true
date: 2018-08-18 14:49:09
---

## Preface

When I was testing my webpage on IOS, I find that the page would be zoomed out automatically if there is some form elements and some element gains focus. Sometimes it will be really weird.

## Main

After searching I found this [How do you disable viewport zooming on Mobile Safari?](https://stackoverflow.com/questions/4389932/how-do-you-disable-viewport-zooming-on-mobile-safari) and I add `user-scalable=0` in my `meta[name=viewport]`. It works.

However, user can still zoom the webpage though it won't zoom out automatically when form element gains focused.

## Reference

[How do you disable viewport zooming on Mobile Safari?](https://stackoverflow.com/questions/4389932/how-do-you-disable-viewport-zooming-on-mobile-safari)
