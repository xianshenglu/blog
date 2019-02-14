---
title: Problems Met When Using canvas
categories: html
tags:
  - canvas
comments: true
date: 2018-10-05 19:21:32
---

## Preface

Kind of weird when using `canvas`, but it's true. Let's take a look.

## Main

### canvas would lost accurate data when opacity isn't equal to 255

sample code:

```html
 <canvas id="canvas"></canvas>
```

```js
let canvas = document.getElementById('canvas')
let cxt = canvas.getContext('2d')
let imageData = cxt.getImageData(0, 0, 1, 1)
let data = imageData.data

data[0] = 200
data[1] = 100
data[2] = 50
data[3] = 25
console.log(imageData.data) //[200, 100, 50, 25]
cxt.putImageData(imageData, 0, 0)
let debugImgData = cxt.getImageData(0, 0, 1, 1)
console.log(debugImgData.data) //[204, 102, 51, 25]
```

`imageData.data` wouldn't be equal to `debugImgData.data` because opacity isn't equal to 255. If we set opacity to 255. They would be the same.

```js
data[3] = 255
```

### imageData.data is readonly

You can modify `data` but you can't reassign it like `imageData.data=[1,22,3,5]`. It will fail **silently**.

### copy imageData

Because `imageData.data` is readonly. So, you can't copy it like code below:

```js
let newImgData = cxt.createImageData(debugImgData)
newImgData.data = debugImgData.data
console.log('newImgData', newImgData.data) //[0,0,0,0]
```

According to mdn, return value of [createImageData][createimagedata] is

_A new ImageData object with the specified width and height. The new object is filled with transparent black pixels._

which is `[0,0,0,0,,,,]`. And assignment of `newImgData.data = debugImgData.data` fail silently. So, if you want to copy `imageData` the right way is:

```js
let newImgData = cxt.createImageData(debugImgData)
newImgData.data.set(debugImgData.data)
console.log('newImgData', newImgData.data) //[200, 100, 50, 255]
```

That is because `ImageData.data` is a `Uint8ClampedArray`. So we can find the API of
`Uint8ClampedArray.prototype.set()` which stores multiple values in the typed array, reading input values from a specified array.

## Ending

## Reference

- [HTML Canvas putImageData with transparency causes incorrect RGB to be saved](https://stackoverflow.com/questions/36588722/html-canvas-putimagedata-with-transparency-causes-incorrect-rgb-to-be-saved#)

[createimagedata]: https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/createImageData
