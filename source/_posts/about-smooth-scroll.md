---
title: About Smooth Scroll
tags:
  - smooth-scroll
  - scrollTo
  - requestAnimationFrame
  - setTimeout
categories: js
comments: true
date: 2018-06-09 17:47:41
---

## Preface

Recently, I need to make a smooth scroll. Of course, I think of `setTimeout`. However, I was thinking if there is a better way or API. So, here is the result of my research.

## Main

- [scrollTo][scrollto]

You can use it like:

```javascript
window.scrollTo({
  top: 1000,
  behavior: 'smooth'
});
```

It works in Chrome and Firefox. However, `IE` and `Edge` doesn't support. Code above will run like

```js
window.scrollTo(0, 0);
```

Similar API also exists like `scrollIntoView` or `scrollBy`. The compatibility is the key problem.

- [requestAnimationFrame][requestanimationframe]

I would suggest this API instead of `setTimeout`. However, the process of implementation is not so easy like an existing API. Well, a little more complicated than `setTimeout`. However, compared with `setTimeout`, `requestanimationframe` has higher performance. Want to know more? Check the reference and SO. Also, here is the demo:

```js
let animationFrameId = 0;
function smoothScoll(targetScrollTop) {
  let speed = 0.1;
  let startTime = 0;
  let timeLimit = 2000;
  window.cancelAnimationFrame(animationFrameId);
  animationFrameId = window.requestAnimationFrame(step);
  function step(timestamp) {
    startTime = startTime || timestamp;
    let interval = timestamp - startTime;
    let distance = targetScrollTop - getWinScrollTop();
    let stepSize = speed * distance;
    switch (Math.ceil(stepSize)) {
      case 0:
        stepSize = -1;
        break;
      case 1:
        stepSize = 1;
        break;
    }
    if (distance !== 0 && interval < timeLimit) {
      window.scrollTo(0, getWinScrollTop() + stepSize);
      animationFrameId = window.requestAnimationFrame(step);
    }
  }
  function getWinScrollTop() {
    return window.scrollY || window.pageYOffset;
  }
}
```

The latest version can be found in my github [smoothScroll][smoothscroll].

However, the compatibility is not good enough because of `IE<10` and `Opera Mini`.

- Other solution

Code for `setTimeout` is similar with `requestAnimationFrame` but I won't talk about it. Except that, jQuery has provided an API like:

```js
$("a[href='#top']").click(function() {
  $('html, body').animate({ scrollTop: 0 }, 'slow');
  return false;
});
```

## Ending

Anyway, use `requestAnimationFrame` if possible. And if not, use `setTimeout` as shim.

## Reference

[Why is requestAnimationFrame better than setInterval or setTimeout](https://stackoverflow.com/questions/38709923/why-is-requestanimationframe-better-than-setinterval-or-settimeout)

[scrollto]: https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo
[requestanimationframe]: https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame
[smoothscroll]: https://github.com/xianshenglu/smoothScroll
