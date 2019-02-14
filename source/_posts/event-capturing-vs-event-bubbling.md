---
title: Event Capturing Vs Event Bubbling
tags:
  - event capturing
  - event bubbling
  - event delegate
  - image load error
categories: js
comments: true
date: 2018-05-23 15:40:02
---

## Preface

When I am trying to delegate the `error` event of `img`, I find that those events won't bubble up to window. So,does that mean I have to bind so many events as many as images I have?

## Main

After a while, I think of something about event capturing which maybe can solve this question. So, I tried code below:

```html
<img id="img" src="error.png" alt="error.png">
```

```javascript
window.addEventListener('error', windowErrorCb, { capture: true }, true)
function windowErrorCb(event) {
  let target = event.target
  let isImg = target.tagName.toLowerCase() === 'img'
  if (isImg) {
    imgErrorCb()
    return
  }
  function imgErrorCb() {
    let isImgErrorHandled = target.hasAttribute('data-src-error')
    if (!isImgErrorHandled) {
      target.setAttribute('data-src-error', 'handled')
      target.src = 'backup.png'
    }
  }
}
```

Code above works normally. If we change the `capture` and the last argument to `false`, it doesn't work.

In this case, we can find that event capturing seems have more applicable situations than event bubbling. Also it seems faster because I catch and handle the event earlier than event bubbling.
So, I got these questions:

- Why would we use event bubbling not event capturing as default except for compatibility?

- Does event capturing really faster than event bubbling as I thought?

- Does event capturing really have more applicable situations?

The answers can be listed below:

- Don't know yet.

- Yes, you can see the demo [here][delegating-capture-vs-bubble]

- Yes.

## Ending

After words above, I think we should use event capturing as default.

## Reference

[Event Capturing vs Event Bubbling](https://stackoverflow.com/questions/2661199/event-capturing-vs-event-bubbling/10335117)

[delegating-capture-vs-bubble][delegating-capture-vs-bubble]

[delegating-capture-vs-bubble]: https://jsperf.com/delegating-capture-vs-bubble/2
