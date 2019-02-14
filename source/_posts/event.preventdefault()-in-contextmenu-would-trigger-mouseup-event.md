---
title: event.preventDefault() in contextmenu Would Trigger mouseup event
categories: js
tags:
  - mouseup
  - contextmenu
  - preventDefault
comments: true
date: 2018-12-27 21:16:15
---

### Question

How many logs would you have when you click and release right mouse button?

#### Demo1

```js
document.oncontextmenu = function() {
  console.error(event.type, Date.now())
}
document.onmouseup = function() {
  console.error(event.type, Date.now())
}
```

#### Demo2

```js
document.oncontextmenu = function() {
  event.preventDefault()
  console.error(event.type, Date.now())
}
document.onmouseup = function() {
  console.error(event.type, Date.now())
}
```

The answer is 1,2. The question is

- Does that matter?
- And why?

### Matters

Normally, click and release right button would only trigger `contextmenu` instead of `mouseup`.

```js
document.oncontextmenu = function() {
  console.error(event.type, Date.now())
}
document.onmouseup = function() {
  console.error(event.type, Date.now())
}
```

It does make senses in most cases that when we were using `onmouseup` means that we only want to listen to the `mouseup` of left button.

However, if you used `event.preventDefault()` in `contextmenu`, `mouseup` event would be triggered when you press and release the right button.

```js
document.oncontextmenu = function() {
  event.preventDefault()
  console.error(event.type, Date.now())
}
document.onmouseup = function() {
  console.error(event.type, Date.now())
}
```

In most cases, this is not expected as it changes the behavior of `mouseup` listener because of changes in `contextmenu` listener. To be honest, this awful **unless we add button detection in `mouseup` listener every time we want to do something when user only press and release left button.**

```js
document.oncontextmenu = function() {
  event.preventDefault()
  console.error(event.type, Date.now())
}
document.onmouseup = function() {
  if (event.button !== 0) {
    return
  }
  console.error(event.type, Date.now())
}
```

### Why

according to mdn [mouseup](https://developer.mozilla.org/en-US/docs/Web/Events/mouseup), default action of `mouseup` is

> Default Action : Invoke a context menu (in combination with the right mouse button, if supported)

```js
document.oncontextmenu = function() {
  console.error(event.type, Date.now())
}
document.onmouseup = function() {
  event.preventDefault()
  console.error(event.type, Date.now())
}
```

However, at least Chrome 70 didn't prevent triggering `contextmenu` event. But it might explain something.

- Because default action of `rightClick` was to invoke a context menu. So, `mouseup` won't get triggered.
- And `event.preventDefault()` in `contextmenu` listener did the actual prevent job. So, `mouseup`
  get triggered.

Anyway, it's still hard to understand.

[**Original Post**](https://github.com/xianshenglu/blog/issues/64)

## Reference
