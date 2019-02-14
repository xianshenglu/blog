---
title: Browsers Disabled Audio AutoPlay
categories: js
tags:
  - audio
  - autoplay
comments: true
date: 2018-11-26 21:40:32
---

If you have used `audio` or `video`, I guess you probably know `autoplay` attribute. Sometimes, PM wants to use that. However, the browsers doesn't want that.

When I was using `autoplay` on 2018.10, I find that safari and chrome disabled `autoplay` when user haven't interacted with the page, especially on safari mobile. It's annoying.

So, if you want to use `autoplay` it may fail. So, some guys choose to play the audio when user click or touch the page. But I think the better way is to detect whether the user's browser support `autoplay`. And the way is:

```js
audioEl
  .play()
  .then(res => {
    //not disabled
  })
  .catch(er => {
    //disabled
  })
```

However, [ie11 and edge before 2018.1 returns `undefined` when `audio.play()`](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/11998448/). So, if you care about that, you may try:

```js
let audioState = audioEl.play()
if (typeof audioState !== 'undefined') {
  audioState
    .then(res => {
      //not disabled
    })
    .catch(er => {
      //disabled
    })
}
//other logic? what are you gonna do?
```

[**Original Post**](https://github.com/xianshenglu/blog/issues/18)

## Reference

- [Autoplay Best Practices with Video.js](https://blog.videojs.com/autoplay-best-practices-with-video-js/)
