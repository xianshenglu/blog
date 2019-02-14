---
title: Notes for Developing Web Page
categories: summary
tags:
comments: true
date: 2019-02-01 19:17:24
---

Here is the stuff.

### Still Consider IE?

If you still need to be compatible with IE browser, the below tag might be needed to render page using EdgeHTML instead of uncertain render engine.

```html
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
```

### Use Custom Font on the Web?

[fontmin](https://github.com/ecomfe/fontmin/) will help you. For more information, check [issues/72](https://github.com/xianshenglu/blog/issues/72).

### Consider Scroll Bar

Before we can directly set scroll bar width by `::-webkit-scrollbar` **for major browsers on PC**, we always need to take care of the toggle of scroll bar because it might affect the layout except in Mac. For example,

- It is common that we need to use `box-sizing: border-box;` to include the scroll bar.
- Sometimes we even need to get the scroll bar width in js.

Anyway, make sure that you have already considered scroll bar before production. For example, the style, the effect of layout, etc.

### Mobile Web Page

For the mobile web page, we need to do something else and I write a summary [here](https://github.com/xianshenglu/blog/issues/69).

[**Source**](https://github.com/xianshenglu/blog/issues/68)

## Reference
