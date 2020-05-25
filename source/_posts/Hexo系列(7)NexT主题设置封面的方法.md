---
title: Hexo系列(7)NexT主题设置封面的方法
tags:
  - Hexo
categories:
  - Hexo
toc: true
date: 2019-05-28 16:30:46
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528162957.jpg)

<!-- more -->

> 默认情况下NexT主题的首页，每篇文章都会渲染出来，希望首页可以尽量缩短，每个文章只显示摘要，如果有图片，就显示张封面。

### 1.Next主题设置摘要
在主题配置文件 themes/next/_config.yml，将auto_excerpt.enable设置成true，length属性表示摘要的字数限制。
```
auto_excerpt:
  enable: true
  length: 150
```

### 2.文章设置封面
在 <!-- more -->上面放一张图片就可以了
```
---
title: Hexo系列(7) NexT主题设置封面的方法
tags:
  - Hexo
categories:
  - Hexo
toc: true
date: 2019-05-28 16:22:59
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528162957.jpg)

文章摘要

<!-- more -->

文章正文
```

### 3.参考
- [Hexo NexT主题设置封面的方法](https://wdd.js.org/hexo-next-theme-set-cover.html)
- [NexT主题的优化定制修改指南](https://juejin.im/post/5a71ab9f518825735300ee6c#heading-39)