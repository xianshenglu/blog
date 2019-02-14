---
title: Easier Way to Understand apply and call in JS
categories: js
tags:
  - apply
  - call
comments: true
date: 2018-12-04 20:18:18
---

The first time I know `apply` was when I met this code:

```js
Math.max.apply(null, [1, 2, 3, 4])
```

As the [mdn](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) shows, the syntax is:

> function.apply( thisArg , [argsArray] )

Actually, in case above, thisArg has no influence which means code below also works:

```js
Math.max.apply(undefined, [1, 2, 3, 4])
Math.max.apply(Math, [1, 2, 3, 4])
```

The only effect of `apply` in the code above is that it can pass the values in array to the function `max`. So, code above equal

```js
Math.max(1, 2, 3, 4)
```

Why would I mention this? Because we don't need this anymore because we already have `...` which works like:

```js
Math.max(...[1, 2, 3, 4])
```

The reason that we still need `apply` and `call` is the thisArg. They can help us call some powerful methods.

#### thisArg in apply and call

I guess you might have seen this code:

```js
Array.prototype.slice.call({ length: 2 })
function fn() {
  console.log(Array.prototype.slice.call(arguments))
}
fn(1, 2, 3, 4) //[1,2,3,4]
```

Today, we don't need this either because of `Array.from`. But I still want to talk about it for explanation. In the case above, `call` was used because we want to do something like:

```js
let obj = { length: 2 }
obj.slice() //Uncaught TypeError: obj.slice is not a function
```

It would cause error because slice was defined in `Array.prototype`. Only `Array` instance can call that method. **But actually in the [implementation](http://es5.github.io/#x15.4.4.10) of `slice`, it doesn't need to be called by `Array` instance** and there is a lot of methods like this. So, in this case, `call` or `apply` would let non `Array` instance call these methods which means

```js
Array.prototype.slice.call({ length: 2 })
//help you do something like this
let obj = { length: 2 }
obj.slice = Array.prototype.slice
obj.slice()
```

**And to help it easier to understand , you can remember it like:**

```js
method.call(thisArg, ...args)
//works like in most cases
thisArg.method = method
// or this way, if thisArg is a primitive value
Object.getPrototypeOf(thisArg).method = method
thisArg.method(...args)
//for apply
method.apply(thisArg, args)
//works like in most cases
thisArg.method = method
// or this way
Object.getPrototypeOf(thisArg).method = method
thisArg.method(...args)
```

Wasn't that easy ?

So, let get back to `Math.max.apply({}, [1, 2, 3, 4])`. You can remember it like:

```js
let thisArg = {}
thisArg.max = Math.max
thisArg.max(...[1, 2, 3, 4])
```

And more cases:

```js
Object.prototype.toString.call([]) //"[object Array]"
//help you do this
let thisArg = []
thisArg.toString = Object.prototype.toString
thisArg.toString() //"[object Array]"
//while
[].toString()//""
```

Or

```js
;[' sd ', 1, 3].map(Function.prototype.call, String.prototype.trim) //['sd','1','3']
//help you do
;[' sd ', 1, 3].map(function(...args) {
  return String.prototype.trim.call(...args)
})
//help you do
;[' sd ', 1, 3].map(function(...args) {
  let thisArg = args[0]
  thisArg.trim = String.prototype.trim
  // way above wouldn't work because thisArg is a Primitive value, so we use way below instead.
  Object.getPrototypeOf(thisArg).trim = String.prototype.trim
  return thisArg.trim(...args.slice(1))
})
```

#### More in apply

As `apply` can accept an array-like object. So, what would happen if coding like:

```js
Array.apply(null, { length: 2 })
```

Actually, it equals

```js
Array.apply(null, [undefined, undefined])
```

So, you can understand it like:

```js
let thisArg = {} //set null would get error in code below, also thisArg in above case is not important
thisArg.Array = Array
thisArg.Array(undefined, undefined)
```

Hope it's easier to understand `apply` and `call`.

[**Original Post**](https://github.com/xianshenglu/blog/issues/50)
