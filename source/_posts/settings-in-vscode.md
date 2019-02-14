---
title: settings in vscode
tags: --vscode
categories: software
comments: true
date: 2018-09-23 10:09:06
---

## 配置

1. vscode 支持特定语言的设置，比如，为不同的语言设置不同的配置：

```js
{
  "[html]": {
    "editor.tabSize": 4
  },
  "[css]": {
    "editor.tabSize": 4
  },
  "[javascript]": {
    "editor.insertSpaces": true,
    "editor.tabSize": 2
  },
  "[typescript]": {
    "editor.tabSize": 2
  }
}
```

2. 个别插件的配置比较特殊，需要配置后才能正常使用，譬如 csscomb, eslint, Easy LESS 等，注意查看文档。

3. 我的配置文件，借助 Settings Sync VSCode 插件，不定期修改会同步到 https://gist.github.com/xianshenglu

## 相关插件

#### HTML：

Auto Rename Tag 修改 HTML 标签时，自动修改匹配的标签

Open in Browser

HTML CSS Class Completion: CSS class 提示

HTMLHint: HTML 格式提示

#### CSS：

Can I Use: HTML5、CSS3、SVG 的浏览器兼容性检查

Color Highlight: 颜色值在代码中高亮显示

Color Picker: 拾色器

csscomb: css 排序，格式化，支持 .vue，**但对于嵌入式样式缩进有问题**

HTML CSS Support: css 提示，支持 .vue

#### JS

ESLint: ESLint 语法校验，自动格式化，修改等

Version Lens package.json: 文件显示模块当前版本和最新版本

View Node Package: 快速打开选中模块的主页和代码仓库

JavaScript (ES6) code snippets: ES6 语法代码段

Version Lens: 在 package.json 里显示包最新版本号

#### HTML/CSS/JS

Emmet: html,css,js 提示

#### 框架

vetur: 目前比较好的 Vue 语法高亮

VueHelper: Vue2 代码段（包括 Vue2 api、vue-router2、vuex2）

vue-peek: vue 文件内查找速览声明

vue-format: vue 格式化插件

#### 非语言类

Bracket Pair Colorizer: 不同层级括号上色

Code Spelling Checker: 单词拼写检查

Output Colorizer: 彩色输出信息

Partial Diff: 对比两段代码或文件

Path Autocomplete: 路径补全

Path Intellisense: 路径提示

Project Manager: 快速切换项目

Settings Sync VSCode: 设置同步到 Gist
