---
title: WheelEvent Compatibility and Delta Handler
categories: js
tags:
  - WheelEvent
  - delta
  - wheel
  - DOMMouseScroll
  - mousewheel
comments: true
date: 2019-01-27 19:47:39
---

I have seen many times people talking about the compatibility problems of wheelEvent, something about `wheel`, `DOMMouseScroll`, `mousewheel` etc. But now, it seems we don't need to care about those compatibility problems anymore.

As [mdn](https://developer.mozilla.org/en-US/docs/Web/Events/wheel#Browser_compatibility) shows, **IE9 support `wheel` event in `addEventListener` and `DOMMouseScroll` is only needed for Firefox 17-**. And for other major browsers, they all support `wheel` event.

But, there are other problems still need to take care.

### `delta`

`event.delta` returns different numbers in the major browsers. For instance, if you run the code below at your console in some page,

```js
document.body.addEventListener('wheel', function(event) {
  console.log(event.deltaY)
})
```

then moving your mouse wheel, you will see the `deltaY` numbers.

| browsers/win7 | deltaY |
| ------------- | ------ |
| Chrome 70     | 100    |
| Firefox 64    | 3      |
| IE 11         | 88.19  |

Well, you can find a solution to unify the `deltaY`. But I think the easier way is to give up unifying and only do the accumulation job. For example, try the code below:

```js
let wheelSize = 0
document.body.addEventListener('wheel', function(event) {
  wheelSize += event.deltaY > 0 ? 0.025 : -0.025
  console.log(wheelSize, event.deltaY)
})
```

In this way, we will get the unified `wheelSize`.

### More Situations Need to Watch Out

When used in projects, there are more things we need to watch out. For example,

- We might have `wheelSize` limit.
- We might want to prevent calculating `wheelSize` when the page has been scrolled to the top or the bottom.
- ...

And the code would become more complicated. For instance,

```js
let targetNode = document.body
let curWheelSize = 1
let minWheelSize = 1
let maxWheelSize = 10
let minScrollTop = 0
let maxScrollTop =
  targetNode.parentNode.scrollHeight - targetNode.parentNode.offsetHeight
targetNode.addEventListener('wheel', wheelCb)
function wheelCb(event) {
  const isDeltaYPositive = event.deltaY > 0
  let scrollTop = targetNode.parentNode.scrollTop
  if (
    (scrollTop <= minScrollTop && !isDeltaYPositive) ||
    (scrollTop >= maxScrollTop && isDeltaYPositive)
  ) {
    return
  }
  let stepSize = isDeltaYPositive ? 0.025 : -0.025
  let targetWheelSize = curWheelSize + stepSize
  targetWheelSize = Math.max(targetWheelSize, minWheelSize)
  targetWheelSize = Math.min(targetWheelSize, maxWheelSize)
  curWheelSize = targetWheelSize
}
```

[**Original Post**](https://github.com/xianshenglu/blog/issues/58)

## Reference
