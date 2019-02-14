---
title: Understand Format Context in CSS2.1
categories: css
comments: true
date: 2018-05-21 19:30:05
tags:
---

##Preface

According to CSS2.1 [specs][specs] and [errata][errata],

> Boxes in the normal flow belong to a formatting context, which in CSS 2 may be table, block or inline, but not both simultaneously. In future levels of CSS, other types of formatting context will be introduced.

> Block-level boxes participate in a block formatting context. Inline-level boxes participate in an inline formatting context. Table formatting contexts are described in the chapter on tables.

Also,we can find the definition of BFC(Block Formatting Context) and IFC(Inline Formatting Context). However, it really takes me a lot of time to understand what is `participate in a BFC or IFC` or `establish a new BFC or IFC`.

##Explanation

When the specs says that participate in a

[specs]: https://www.w3.org/TR/2011/REC-CSS2-20110607
[errata]: http://www.w3.org/Style/css2-updates/REC-CSS2-20110607-errata.html
