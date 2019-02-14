---
title: X-UA-Compatible and ie=edge
categories: html
tags:
  - meta
  - http-equiv
  - X-UA-Compatible
  - content
  - ie=edge
comments: true
date: 2018-10-03 19:43:57
---

## Preface

平时会用 vue 写新项目，老项目就在原有基础上更新。对于 vue 这种框架，使用官方的脚手架通常就避免了很多问题，就像平时用模板创建新的单页一样。

然而有时总是会遇到些不按模板走的代码，虽然跑起来也没有问题，但是放到有些浏览器上就有 bug 了，这个时候对既有模板的理解和掌握就很重要了。

## Main

当我用 html 模板创建一个新单页时，拿到的页面是这样的，vue 也是类似，至少三个 `meta` 标签基本都是一样的：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>

<body>
</body>

</html>
```

`viewport` 是在兼容移动端时才了解的内容，费了不少功夫。而 `X-UA-Compatible` 则是在遇到非常规代码的时候才想起来的。有一回改个老项目，用了 `transform`，在 ie11 上测试，没有用，而且在它的工具栏里样式表里根本看不到我写的代码，这个时候我就好奇了，这是 ie11 啊，怎么会不支持 `transform` 呢？然后我瞄了一下开发者工具，大概是这样的：

![截图20181003201618.png](../images/截图20181003201618.png)

然后我就好奇了，为什么会是 ie7 模式呢？我明明装的是 ie11 浏览器啊，然后脑袋一闪，好像明白了什么，看了看 `html` ，果然没有:

```html
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
```

加上之后，再刷新，网页就正常了。

在这件事之前，我知道这个东西，但是因为是用的模板，一直没有遇到过这个问题，而且通常来说，我会以为，我既然是在 ie11 里打开的，默认你也没有理由用 ie11- 的文档模式去渲染啊，结果就错了。后来看到 SO 上高票答主大概是这么解释 ie 的行为的：

_ie 会用它认为最好的方式去渲染页面，如果没有上面那行代码的话_

此外，ie11 已经开始废弃上面那个了，如果不兼容 ie 的话，其实上面的代码也可以不用写了，不过目前为止 html 模板和 vue 的模板都还是默认支持的。而上面的那行代码实际意思呢，就是：

_Edge：始终以最新的文档模式来渲染页面。忽略文档类型声明。对于 IE8，始终保持以 IE8 标准模式渲染页面。对于 IE9，则以 IE9 标准模式渲染页面。_

当然 ie 还可以等于其他值，不过其他值大多都是老版本，目前而言，都没有必要去纠结了，譬如：

- "IE=edge"
- "IE=11"
- "IE=EmulateIE11"
- "IE=10"
- "IE=EmulateIE10"
- "IE=9"
- "IE=EmulateIE9
- ...

## Ending

## Reference

- [What does <meta http-equiv=“X-UA-Compatible” content=“IE=edge”> do?](https://stackoverflow.com/questions/6771258/what-does-meta-http-equiv-x-ua-compatible-content-ie-edge-do)
