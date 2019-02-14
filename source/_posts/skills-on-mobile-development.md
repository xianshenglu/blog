---
title: Skills on Mobile Development
tags:
  - mobile
  - webpack
categories: debug
comments: true
date: 2018-07-01 10:18:27
---

## Main

### Access localhost on Mobile

As you know, If I am working with url like `http://localhost:8080` It doesn't work on mobile. So, I have to try another way. After some research, I find something related:

- `localhost` is a hostname which points to `127.0.0.1` by default and also can be changed.

Normally, **we are using the same WIFI on our computer and mobile.** So, in my case,

- I use command `ipconfig` and got the ip `192.168.x.x`.
- I add `--host 192.168.x.x` in my _package.json_ scripts like this:

```js
{
  "name": "webpack-boilerplate",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "build": "npm run lint && webpack --config webpack.config.js -p",
    "dev": "npm run lint && webpack-dev-server --config webpack.dev.config.js --inline --host 192.168.x.x",
    "lint": "./node_modules/.bin/eslint js"
  },
}
```

- If you aren't using _package.json_, you can directly replace `localhost` to `192.168.x.x` in the url.
- Then I open the url `http://192.168.x.x:8080` on my computer and mobile. Both work!

## Ending

## Reference

- [移动端前端开发调试](http://yujiangshui.com/multidevice-frontend-debug/)
