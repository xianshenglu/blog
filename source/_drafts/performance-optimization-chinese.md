最近开始关注性能优化的问题，一边找资料，一边理思维，最终我的需求是这样的。

我希望找到一份

- 实时更新的性能优化列表
- 实时更新的性能优化原理分析
- 实时更新的流行性能分析插件或平台

重点都是实时更新，毕竟我不想

- 用已经淘汰的性能优化方式
- 每过一阵子又重新找资源总结

最后找到了[实时更新的性能优化列表](thedaviddias/Front-End-Performance-Checklist​github.com)以及[实时更新的性能优化原理分析和分析插件-谷歌开发者文档](https://developers.google.com/web/fundamentals/performance/why-performance-matters/​)

中英文夹杂，这里简要翻译 2019.2.16 原文的小标题（重点），最新内容和原理分析推荐看原文，内容较多。

### 概述

### 性能测量模型( RAIL)

### 加载性能

#### 概述

#### 起步

##### 性能检测平台

- Lighthouse (chrome dev tool 内置，所以开发阶段就可用)
- [PageSpeed Insights ](https://developers.google.com/speed/pagespeed/insights/)
- [WebPageTest](https://www.webpagetest.org/)
- [Pingdom Tools](https://tools.pingdom.com/)

##### 文字内容

- 开发文件和发布文件分开存放
- 最小化代码
- 压缩代码（去除不必要的空白符等）
- 移除不必要的库

##### 图像内容

- 移除不必要的图片
- 选择合适的图片类型
- 移除图片元信息（图片的拍摄地，名称之类）
- 缩放图片
  - 小元素用小图，大图需要的时候再加载或延迟加载
  - 裁切图片，裁掉没用的部分
- 在轻微降低图片质量的情况下，尽可能的压缩图片
- 单纯压缩图片

##### HTTP 请求

- 合并代码（HTTP 2.0 下，需要重新权衡 ）
- 合并图片（雪碧图）
- js 位置和行内 js
  - js 放在 `</body>` 之前
  - pre-render scripts 直接在需要的地方插入，避免再次像服务器请求一个新文件

##### HTTP 缓存

- 开启缓存
- 设置缓存头
  - `Cache-control` 字段分析
  - `Expires Caching` 字段分析

##### [Demo](https://developers.google.com/web/fundamentals/performance/get-started/wrapup-7)

#### 如何考虑加速工具

##### 关于性能的几个误区

- 用户体验可以用一个指标来捕获
- 用户体验可以用一个代表性用户来捕获
- 我自己觉得网站很快，所以，我的用户应该也是一样感觉

##### 理解实验数据和现场数据的优劣势

##### 有哪些不同的性能工具

- [Lighthouse](https://developers.google.com/web/tools/lighthouse/)
- [WebPageTest](https://www.webpagetest.org/)
- [TestMySite](https://testmysite.thinkwithgoogle.com/)
- [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/)
- [Speed Scorecard](https://www.thinkwithgoogle.com/feature/mobile/)
- [Impact Calculator](https://www.thinkwithgoogle.com/feature/mobile/)
- [Chrome Developer Tools](https://developers.google.com/web/tools/chrome-devtools/)

如果你是

- 想要提升用户体验的开发者/营销人员，用 Speed Scorecard，Impact Calculator，TestMySite
- 开发者，想要理解当前网站的性能状况，用 PageSpeed Insights
- 开发者，想要根据现代网页性能最佳实践理解和审视网站，用 Lighthouse
- 开发者，正在学习如何 debug/深入网站性能，用 Chrome Developer Tools，WebPageTest

#### 通过导航和资源计时评估实际场景下的加载性能

##### 帮助你理解浏览器中网络请求的简单 API

### 渲染性能

### 审视你的网站
