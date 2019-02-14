---
title: Label Can't Be Focused In Firefox With Element.focus()
tags:
- label
- focus
- firefox
- contentEditable
- bug
categories: html
comments: true
date: 2018-06-10 11:13:18
---

## Preface

As the title says, let's see the demo below:

## Main

```html
<label id="cont" contentEditable>
   label should be focused
</label>
<button id="focus">make label focus</button>
```

```css
#cont {
  display: block;
  background: pink;
  width: 150px;
  height: 40px;
}
```

```javascript
$('#focus')[0].onclick = () => {
  $('#cont')[0].focus()
}
$('#cont')[0].onfocus = event => {
  console.log(event.type)
}

function $(selector) {
  return Array.from(document.querySelectorAll(selector))
}
```

Suppose that you are using chrome, after loaded please press `F12` and open the console and then click the button. `label#cont` should be focused after clicking the button.So, there will be word `focus` at the console.

However, firefox wouldn't do that until you click the label. Anyway, I have posted a [bug][bug]. Let's see what will happen.

[bug]: https://bugzilla.mozilla.org/show_bug.cgi?id=1468049
