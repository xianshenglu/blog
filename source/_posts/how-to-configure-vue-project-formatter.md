---
title: How to Configure Vue Project Formatter
categories: js
tags:
  - vue
  - codeStyle
  - ESLint
  - prettier
  - babel
comments: true
date: 2019-05-26 16:33:24
---

As far as I know, serval formatters would run with serval rules when I want to format .vue file in the vscode editor. I have been confused for a long time about these formatters and their specific rules, also the crossed situations. Sometimes it makes me crazy. For example,

- For formatters, there are `ESLint`, `prettier`, `vscode internal formatter` and some other formatters which I don't know.
- For rules, there are `.eslintrc.js`, `.prettierrc.js`, `user settings` and other plugins like `eslint-plugin-html`, `eslint-plugin-vue` etc.
- For developers, you can't expect that they have identical user settings.

### What I Want?

After thinking, I figure out what I want.

> How can I let different developers **commit** with the identical styles?

To be more specific, I want

- [ ] Slightly dependent on developer user settings, use config files instead of plugins as possible as we can.
- [x] Syntax-highlighting
- [x] All the format and lint should work in a consistent way(specific rules).
- [x] Format and lint .js and `script` in .vue.
- [x] Format and lint .css, .less, .scss and `style` in .vue.
- [x] Format and lint .html and `template` in .vue.
- [ ] Format template _public/index.html_ or ignore.
- [ ] Format and lint before commit
- [ ] Works on vscode at least, better if works on more editors.

Okay, let's do it one by one.

### Syntax-highlighting

I found two ways:

1. add config below to _settings.json_ which let vscode highlight it as html.

```js
"files.associations": { "*.vue": "html" },
```

2. install [Vetur][vetur] plugin.

I would recommend [Vetur][vetur] because [Vue VSCode Snippets](https://marketplace.visualstudio.com/items?itemName=sdras.vue-vscode-snippets) and other plugins may depend on it.

### Work in a consistent way(specific rules)

- For .js or `script` in .vue, we have ESLint with the help of plugins and prettier.
- For .css, .less, .scss and `style` in .vue, we have stylelint with the help of plugins and prettier.
- For html and `template` in .vue, we have htmllint with the help of plugins and prettier.

### Format and lint .js and `script` in .vue.

As [ESLint](https://github.com/eslint/eslint#what-about-experimental-features) said,

> ESLint's parser only officially supports the latest final ECMAScript standard.

> In other cases (including if rules need to warn on more or fewer cases due to new syntax, rather than just not crashing), we recommend you use other parsers and/or rule plugins. If you are using Babel, you can use the babel-eslint parser and eslint-plugin-babel to use any option available in Babel.

> Once a language feature has been adopted into the ECMAScript standard (stage 4 according to the TC39 process), we will accept issues and pull requests related to the new feature, subject to our contributing guidelines.

In my case, there is `stage2` in my _.babelrc_ presets. So, I have to use `babel-eslint` as parser.

### History problem:

- [ ] How to handle history format and validation?

### Related config or plugins:

When and why do we need these?

- [ ] [`"parser": "babel-eslint"`](https://github.com/babel/babel-eslint)
- [ ] `extends` like

```js
;[
  'plugin:vue/essential',
  'eslint:recommended',
  'plugin:prettier/recommended' //may not
]
```

- [ ] plugins like `eslint-plugin-prettier`, `eslint-config-prettier`, `@vue/cli-plugin-eslint`, `@vue/cli-plugin-babel`
- [ ] [prettier](https://github.com/prettier/prettier)
- [ ] [vetur](https://github.com/vuejs/vetur)
- [ ] [eslint-plugin-vue](https://eslint.vuejs.org/rules/)

## Reference

- http://www.alloyteam.com/2017/08/13065/

[**Source**](https://github.com/xianshenglu/blog/issues/93)

## Reference

[vetur]: (https://marketplace.visualstudio.com/items?itemName=octref.vetur)
