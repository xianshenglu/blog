---
title: Better Way to Use orientationchange Event on Mobile
categories: js
tags:
  - orientationchange
  - mobile
comments: true
date: 2018-10-04 12:39:53
---

## Preface

When I was using `orientationchange` event, I met a few bugs. So, I take it down.

## Main

#### compatibility problem

When I was testing my code on my android, it was ok. However, it doesn't work on my boss's iPhone6. So, i have to change the code.

The origin code was like:

```html
  <div class="box" id="box">
```

```css
html,
body {
  width: 100%;
  height: 100%;
}

.box {
  width: 100%;
  height: 100%;
  background: pink;
}

.box.landscape {
  background: lightblue;
}
```

```js
let box = document.getElementById('box')
window.addEventListener('orientationchange', orientationChangeCb)
function orientationChangeCb(event) {
  let isLand = isLandscape()
  if (isLand) {
    box.classList.add('landscape')
  } else {
    box.classList.remove('landscape')
  }
}
function isLandscape() {
  if ('orientation' in window) {
    return Math.abs(window.orientation) === 90
  } else {
    return window.innerWidth > window.innerHeight
  }
}
```

To be compatible with iPhone6, I use `resize` event instead.

However, the better way I think is :

```js
let eventForOrientationChange =
  'onorientationchange' in window ? 'orientationchange' : 'resize'
if (isMobile()) {
  window.addEventListener(eventForOrientationChange, orientationChangeCb)
}
```

#### isMobile ?

Because `onorientationchange` is a mobile event, so if you try to run code below on your computer with chrome, you will get:

```js
window.onorientationchange //undefined
'onorientationchange' in window //false
```

It seems a little weird but it's true until chrome 69. That's why I add `isMobile()` before I use `window.addEventListener`. In that case, we don't have to listen for the `resize` event on desktop.

#### window.orientation or screen.orientation

According to mdn, [window.orientation][window.orientation] has been **Deprecated**. However, similar API [screen.orientation][screen.orientation] has a big problem for compatibility. Safari and QQ doesn't support. As of 2018.10.4, global support in [caniuse][caniuse] is only 72.5%.

#### css only

If you just want to update style, you don't have to use code above. CSS media queries
support code like:

```css
@media (min-height: 680px), screen and (orientation: portrait) {
  /* ...; */
}
@media (min-height: 680px), screen and (orientation: landscape) {
  /* ...; */
}
```

## Ending

## Reference

- [detect viewport orientation, if orientation is portrait display alert message advising user of instructions](https://stackoverflow.com/questions/4917664/detect-viewport-orientation-if-orientation-is-portrait-display-alert-message-ad)

[window.orientation]: https://developer.mozilla.org/en-US/docs/Web/API/Window/orientation#Specifications
[screen.orientation]: https://developer.mozilla.org/en-US/docs/Web/API/Screen/orientation#Browser_compatibility
[caniuse]: https://caniuse.com/#search=orientation
