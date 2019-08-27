---
title: Traps in reduce
categories: js
tags:
  - reduce
comments: true
date: 2019-08-27 10:14:26
---

### Optional `initialValue`?

Sometimes, we can see code using `reduce` without `initialValue`. For example,

```js
const array = [1, 2, 3];
array.reduce((re, v) => re + v);
```

According to [mdn](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce#Parameters), it is valid and seems simpler. However, is it a good practice?

### Cons Brought by No `initialValue`

In the real world, `array` could be `[]`. In this case,

```js
const array = [];
array.reduce((re, v) => re + v);
```

You will got error in production!

> Uncaught TypeError: Reduce of empty array with no initial value

Also, imagine that if you have met this code in your work ? What the hell does this `reduce` do?

- Join the `string`
- Or cumulative summation
- Or other situations?

You would never know until you have read the context.

As you can see, these are the most obvious cons brought by no `initialValue`.

- Potential Error
- Unclear semantics

### Benefits Brought with `initialValue`

Instead, if using `reduce` with `initialValue`. For example, in the above case, we can write like this

```js
const array = [];
array.reduce((re, v) => re + v, 0);
```

- We would never meet the error!
- It is obvious that `reduce` is using to do the cumulative summation job.

**Safer** and **Clearer!**

**[Issue](https://github.com/xianshenglu/blog/issues/97)**

**[Source](https://github.com/xianshenglu/blog/blob/master/source/_posts/traps-in-reduce.md)**

## Reference
