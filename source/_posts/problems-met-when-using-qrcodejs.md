---
title: 使用 qrcodejs 生成二维码的几个问题
categories: js
tags:
  - qrcodejs
  - wechat
  - MutationObserver
comments: true
date: 2018-10-03 14:03:57
---

## Preface

产品希望我这边下载页面加个二维码，可以扫描下载 APP，并且希望二维码中有公司的 logo，很合理的需求，不过实现的时候依旧遇到了几个问题，在此记录下。

## Main

二维码的实现逻辑我当然没有这个时间去研究，直接用的 [qrcodejs][qrcodejs]。官方给的 demo 是最简单的版本，各种各样的需求都是在 issues 里找到的提示，似乎这个库已经很久没有人去维护了，虽然 star 是很多。

#### 官网示例（改编）

```html
<div id="qrcode" class="qrcode"></div>
```

```css
.qrcode {
  width: 150px;
  height: 150px;
  border: 2px solid green;
  margin-top: 15px;
}
```

```js
let qrcodeEl = document.getElementById('qrcode')
let qrcode = new QRCode(qrcodeEl, {
  text: 'https://avatars1.githubusercontent.com/u/23273077',
  width: 128,
  height: 128,
  colorDark: '#000000',
  colorLight: '#ffffff',
  correctLevel: QRCode.CorrectLevel.H
})
```

效果如图：

![截图20181003145142.png](../images/截图20181003145142.png)

#### 尺寸控制

我给官网的示例加上了边框，二维码的尺寸和 js 里的尺寸不一样，这个缺点立马就暴露出来了。

我很可能是想生成的二维码填满传入的 `qrcode` 元素的，但是这里的 `width` 不支持 100%，更不支持 `vw` 这种尺寸单位了。当然，我可以用 `qrcode.offsetWidth` 来解决这个问题，但是如果 `qrcode` 的尺寸后期会动态修改的话，那不还是会有问题么。

经 SO 的提示，发现了一个好方案，

```css
.qrcode {
  width: 150px;
  height: 150px;
  border: 2px solid green;
  margin-top: 15px;
}
.qrcode canvas + img {
  width: 100%;
  height: 100%;
}
```

这样就可以了，不过仍然有个不足，**就是二维码有失真。经测试，只有传入的尺寸和 `qrcode` 的尺寸一样时，才不会失真，所以传入的尺寸还是需要动态计算。**不过，可以试试 svg 的 qrcode 库，svg 不会出现这个问题。

#### 加 logo 的二维码

[qrcodejs][qrcodejs] 并没有提供这个 API，issues 里有人给了 demo，其实就是在原有元素上覆盖一个 logo 就可以了，虽然遮盖了原有二维码的一部分，但是实测并不影响扫描。不过我没有进行大规模测试，**可能会有一定的错误率。**

```html
<div id="qrcode" class="qrcode">
  <img
    src="https://avatars1.githubusercontent.com/u/23273077"
    class="qrcode__logo"
  />
</div>
```

```css
.qrcode {
  width: 150px;
  height: 150px;
  border: 2px solid green;
  margin-top: 15px;
  position: relative;
}

.qrcode canvas + img {
  width: 100%;
  height: 100%;
}
.qrcode__logo {
  width: 50px;
  height: 50px;
  border-radius: 10%;
  border: 1px solid #fff;
  position: absolute;
  margin: auto;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
}
```

效果如图：

![截图20181003164515.png](../images/截图20181003164515.png)

#### 检测二维码的生成

某些情况下，我需要重用二维码，在这种情况下，我发现，二维码的生成是异步的，譬如：

```js
let qrcodeEl = document.getElementById('qrcode')
let qrcode = new QRCode(qrcodeEl, {
  text: 'https://avatars1.githubusercontent.com/u/23273077',
  width: 200,
  height: 200,
  colorDark: '#000000',
  colorLight: '#ffffff',
  correctLevel: QRCode.CorrectLevel.H
})
let qrcodeImg = document.querySelectorAll('.qrcode canvas+img')
console.log('qrcodeImg.src', qrcodeImg.src)
setTimeout(function() {
  console.log('qrcodeImg.src', qrcodeImg.src)
}, 1000)
```

第一个日志就是空白的，第二个才有 base64。搞笑的是，[qrcodejs][qrcodejs] 也没有给出回调或者通知告诉用户什么时候生成完毕。

这个问题也是在 issues 里找到的提示，关键点在于 [MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)。

这个 API 很少在项目中用，因为不兼容性 ie11-，但是有时在几千行代码里 debug 时会用，尤其是我怀疑中间有代码改了某个元素的属性，确又找不到证据或者找不到哪段代码时，会用这个来监测下。在这里的用法如下：

```js
let qrcodeEl = document.getElementById('qrcode')
let qrcode = new QRCode(qrcodeEl, {
  text: 'https://avatars1.githubusercontent.com/u/23273077',
  width: 200,
  height: 200,
  colorDark: '#000000',
  colorLight: '#ffffff',
  correctLevel: QRCode.CorrectLevel.H
})
let qrcodeImg = document.querySelector('.qrcode canvas+img')
listenQrcodeSrc()
function listenQrcodeSrc() {
  var observeConfig = { attributes: true }
  var observeCb = function(mutationsList, observer) {
    mutationsList.forEach(function(mutation) {
      if (
        mutation.type.toLowerCase() === 'attributes' &&
        mutation.attributeName.toLowerCase() === 'src'
      ) {
        console.log('qrcodeImg src done!', mutation.target.src)
        observer.disconnect()
      }
    })
  }
  if (typeof MutationObserver !== 'undefined') {
    var observer = new MutationObserver(observeCb)
    observer.observe(qrcodeImg, observeConfig)
  }
}
```

#### 微信中二维码要放在 `img` 中，不能放在 `background-image`

2019.1.31 ，微信 7.0.0 亲测

#### 微信中多个二维码在一起识别错误

这个问题，我也遇到了，根据网友的提示，微信是截屏识别的，所以会出现这种问题。我测试的结果是，左右两个，永远识别的右边的那个。网上有好几种方案：

- 调透明度和层级

最初尝试过，结果发现失败，等到成功的时候，透明度已经小于 0.5 了，视觉差异太明显，所以放弃了这个方案。

- 替换二维码

最终采取的是这个，这个也有问题，就是用户会看到二维码变化的过程，除非你把多个二维码做得很像。

假设，我们要显示两个二维码，所谓替换二维码，其实也就是在多个 `img.src` 属性里切换，可以把实际的二维码保存在 `data-real-src` 属性里，然后在用户 `touchstart` 事件中，替换另一个 `img` 的 `src` 为当前按下的这个，然后在 `touchend` 事件中再改回来，因为原来的地址都保存在 `data-real-src` 属性里。

这里就用到了前面提到的检测 `src` 属性来判断 `qrcode` 生成完毕，否则一开始直接把 `src` 属性赋给 `data-real-src` 属性，就是空白。

示例代码（这里代码跟前面脱节了，dom 是另外的结构，仅作为示例代码）：

```js
//* pubMethods 是类似 jq 的一些 API 的汇总对象
var qrcodeImgs = pubMethods.$('.download__qrcode-box canvas+img')
listenQrcodeSrc()
var downloadBox = pubMethods.$('.download')[0]
downloadBox.addEventListener('touchstart', changeQrcodeSrcToOne)
downloadBox.addEventListener('touchend', changeQrcodeSrcBack)
downloadBox.addEventListener('touchcancel', changeQrcodeSrcBack)

function listenQrcodeSrc() {
  var observeConfig = { attributes: true }
  var observeCb = function(mutationsList, observer) {
    mutationsList.forEach(function(mutation) {
      if (
        mutation.type.toLowerCase() === 'attributes' &&
        mutation.attributeName.toLowerCase() === 'src'
      ) {
        mutation.target.setAttribute('data-real-src', mutation.target.src)
        observer.disconnect()
      }
    })
  }
  qrcodeImgs.forEach(function(ele) {
    if (typeof MutationObserver !== 'undefined') {
      var observer = new MutationObserver(observeCb)
      observer.observe(ele, observeConfig)
    }
  })
}
function changeQrcodeSrcToOne(event) {
  var target = event.target
  var getQrcodeBox = pubMethods.closest(
    target,
    '.download__qrcode-box',
    downloadBox
  )
  if (getQrcodeBox) {
    var targetImg = qrcodeImgs.filter(function(ele) {
      return getQrcodeBox.contains(ele)
    })[0]
    qrcodeImgs.forEach(function(ele) {
      ele.src = targetImg.getAttribute('data-real-src')
    })
  }
}
function changeQrcodeSrcBack(event) {
  qrcodeImgs.forEach(function(ele) {
    ele.src = ele.getAttribute('data-real-src')
  })
}
```

## Ending

## Reference

- [Make qr code full width](https://stackoverflow.com/questions/34546241/make-qr-code-full-width)

- [微信中有两个挨着二维码长按识别的问题?](https://www.zhihu.com/question/35246592)

[qrcodejs]: https://github.com/davidshimjs/qrcodejs
