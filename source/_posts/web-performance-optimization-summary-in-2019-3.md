---
title: Web Performance Optimization Summary in 2019.3
categories: Web Performance Optimization
tags:
  - browser
  - LightHouse
  - performance
comments: true
date: 2019-03-25 21:02:15
---

## Where to Learn Web Performance Optimization(WPO)

About one month ago, I started to learn and summarize the knowledge about web performance optimization. However, beyond my expectations, there is too many things need to know. After thinking, I chose [developers.google.com][developers.google.com] to start to learn. And other resources are listed [**here**](https://github.com/xianshenglu/blog/issues/70) with this article.

The reasons that I chose [developers.google.com][developers.google.com] can be listed below:

- Articles will update when some methods or workarounds is outdated. This is extremely important for me to check it out when I need to update my knowledge about WPO. Also, it lets me know that I am learning something which can be tested and applied right now. For me, a blog is not better than a real-time updated document.
- All the knowledge about WPO was listed systematically which is easy to understand, remember and use.

## Summary About WPO

There are too many things for me to remember or test according to the doc. So, I am trying to summarize and abstract to make it simple to deduce and remember. Well, let's start with the questions.

### What Is the Target of WPO

To make the page load as quickly as possible, also in a progressive way if possible.

### How Can We Do That

Generally, we can analyze from the following aspects.

<!-- #### Loading Performance -->

- Use as much **cache** as possible.

  - For a better control of cache in the front end, we may need to learn something about `PWA` or `Service Worker`.
  - Or we might need some knowledge about offline storage like `Indexed DB`, `Web SQL`, `localStorage` and so on.

- For those content that we can't use the cache, we need to take other measures.

  - Send the content **as closely as possible** which

    - may need the help of `CDN`.

  - Send the content **simultaneously as many as possible** which

    - need the help of `http/2.0` to breakthrough the 6 parallel TCP connections limit in http/1.1 and request the resources simultaneously instead of one by one.

  - Send **as little content as possible** which

    - needs `code compression`, `GZIP`, `code split`, `tree shake`, `image compression` and remove or replace the bigger resources with smaller ones if possible.

  - Load the resource **in a more efficient way** which

    - needs to put `script` at the bottom of `body`

    - or use the `async` according to the situation

<!-- #### Rendering Performance -->

- **Render the page in a more efficient way** which requires more efficient practice in our daily code. For examples,

  - Use `requestAnimationFrame` to change the UI instead of `setTimeout` or `setInterval`.

  - Implement lazy load with `Intersection Observer` instead of calculating the position of each target element by `getBoundingClientRect`.

  - Stick to compositor-only properties like `transform` and `opacity` to avoid re-layout and re-paint.

  - Reduce the complexity of selectors.

  - ....

### How Can We Prove That Our Optimization Is Work

We need some tools like below to collect the result each time we did an optimization.

- [Lighthouse](https://developers.google.com/web/tools/lighthouse/)
- [WebPageTest](https://www.webpagetest.org/easy)
- [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/)
- ......

### Strictly

Strictly speaking, if we want to do WPO seriously for a long time, we still need to do something like:

- Set a performance budget
- Use `performance` or google analysis tool to get the performance data in the real scene.
- ...

## Last Words

Here is just a summary about WPO. More accurate information is in [developers.google.com][developers.google.com].

[**Source**](https://github.com/xianshenglu/blog/issues/70)

## Reference

[developers.google.com]: https://developers.google.com/web/fundamentals/performance/why-performance-matters/
