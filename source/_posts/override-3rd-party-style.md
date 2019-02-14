---
title: Override 3rd Party Style
categories: css
tags:
  - override.css
comments: true
date: 2018-10-05 16:26:56
---

## Preface

It is very common to see 3rd party libs in our projects, especially some UI frames. Meanwhile, here comes the problems.

How can we override partial 3rd party style and avoid our style being overridden by 3rd party.

Well, that's two question.

## Main

### Override Partial 3rd Party Style

- _lib-override.css_

I think the first thing is to prepare a single file like _lib-override.css_ to do this job whatever choice we make.

- override ways

It's obvious that `!important` or `#id` can help us do this job. However, I wouldn't suggest you to do that. I would choose to use class selector with namespace to solve this kind of problem. For example,

3rd party code is:

```css
.el-button--primary {
  color: #fff;
}
```

Then you can write:

```less
.app {
  .el-button--primary {
    color: #fff;
  }
}
```

Code above also works to avoid our style being overridden by 3rd party. In some cases, if one class is not enough you may have to add more class as namespace.

## Ending

## Reference
