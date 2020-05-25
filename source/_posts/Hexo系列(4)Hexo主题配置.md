---
title: Hexo系列(4)Hexo主题配置
tags:
  - Hexo
categories:
  - Hexo
toc: true
date: 2019-04-28 17:04:39
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528221339.jpg)

<!-- more -->

> 所有配置项的 属性冒号后面 均需要加上空格

### 安装Hexo主题
Hexo有很多优秀的主题，这里推荐使用[Next](https://github.com/theme-next)
在 **站点根目录** 中打开 git bash 窗口，使用如下命令下载 NexT 主题文件到 **themes 目录** 中
```
git clone git@github.com:iissnan/hexo-theme-next.git themes/next
```
- **注意**：clone完成后，将 **themes/next** 目录中的 .git文件夹、.gitignore文件删除，以将主题文件作为本地项目管理


### 站点配置
以下操作如无特殊说明，均在 **站点配置文件** 中

#### 设置Hexo主题
在 **站点配置文件** 中找到 theme 节点，启用Next主题，注意空格
```
theme: next	#配置成刚下载的next主题
```

#### 配置Hexo网站相关信息
找到site 节点，修改相关配置信息
```
# Site
title: 挨踢极客  #网站标题
subtitle: show me the code  #网站副标题
description: 人生苦短，我想编程  #描述,介绍网站的
keywords: java,mysql,springboot,docker,linux,前端 #网站的关键字
author: 挨踢极客 #博主姓名
language: zh-CN #语言  zh-CN 是简体中文
timezone: UTC #时区 可设置为 Asia/Shanghai
```

#### 设置Hexo站点永久链接
设置永久链接的目的是为了便于分享和SEO优化，找到 URL 节点
```
# URL
url: https://LicV587.github.io #网站的首页地址
root: / #网站的根目录设置
permalink: :year/:month/:day/:title.html # 这种设置的url表现为2018/10/18/hello-world.html 样式

# 默认链接样式可修改为 (permalink配置只能有一种,关闭某种开头加 #)
# permalink_defaults: :category/:title.html #用分类和博客title当做url地址
```

#### 设置hexo中的默认分类
找到 Category & Tag 节点
```
# Category & Tag
default_category: default  #这里设置一个默认分类名称,如果第3条永久链接设置为 permalink: category/:title/ 那么没有分类的博客就会被放在default这个分类下进行归档
```

#### 配置git信息
github 提供 pages 服务,这样我们就可以把博客静态资源发布到这个仓库,然后用域名来访问博客,所有人都可以通过浏览器来观看你的博客，找到 deploy 节点
```
deploy:
  type: git
  repo: git@github.com:LicV587/LicV587.github.io.git
  branch: master
```


### 主题配置
以下操作如无特殊说明，均在 **主题配置文件** 中，路径为：blog/themes/next/_config.yml

#### 配置Hexo站点的favicon
把准备好的favicon放在 blog/themes/next/source/images/ 下，然后找到 favicon 节点
```
favicon:
  small: /images/favicon-16x16-next.png  #把favicon名修改成你自己的
  medium: /images/favicon-32x32-next.png
```

#### 配置Hexo的菜单栏
默认菜单只开启了首页和归档。Hexo的所有图标均来自[fontawesome](https://fontawesome.com/v4.7.0/icons)。
在主题配置文件中开启相关的菜单栏，找到 menu 节点
```
menu: #菜单设置为 菜单名: /菜单目录 || 菜单图标名字
  home: / || home 
  about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  commonweal: /404/ || heartbeat
menu_settings:
  icons: true  #显示图标
  badges: true  #显示统计信息
```

#### 手动生成hexo菜单对应文件
新菜单开启后是没有对应文件的,所以要手动生成about,tags,categories和404页面,这里我们将404替换成腾讯的公益页面。
新建一个about页面：
```
cd blog
hexo new page about   #about就是普通的布局一般用来介绍站点信息和博主信息等
```
找到tags文件 blog/source/about/index.md 编辑它,在头部修改为
```
---
title: All tags
date: 2019-04-28 13:58:44 #时间随意
type: "tags" #类型一定要为tags
comments: false #提示找个页面不需要评论,后续评论插件那里会详细介绍
---
```
新建一个categories页面
```
cd blog
hexo new page categories
```
找到categories文件 blog/source/categories/index.md 编辑它,在头部修改为
```
title: 分类
date: 2019-04-28 13:58:44
type: "categories"
comments: false
---
```
404 页面是一个比较特殊的页面，用于访问不到指定的网页资源是进行提示。这里将404页面配置成 [腾讯公益404页面](https://www.qq.com/404/)。
在source/目录下新建一个404.html文件，注意页面头部不能缺少：
```
---
layout: false
title: "404"
---

<!DOCTYPE HTML>
<html>
<head>
    	<meta http-equiv="content-type" content="text/html;charset=utf-8;">
	<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
	<meta name="robots" content="all">
	<meta name="robots" content="index,follow">
</head>
<body>
<script type="text/javascript" src="//qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js" charset="utf-8" homePageUrl="https://blog.itjk.me" homePageName="回到我的主页"></script>
</body>
</html>
```
- **注意**：将 html 代码中的 homePageUrl 属性配置为你的博客地址

#### 配置Hexo本地搜索
安装插件,终端进入 cd blog 后执行：
```
npm install hexo-generator-searchdb --save
```
在 **主题配置文件** 中配置为：
```
local_search:
  enable: true
  trigger: auto
  top_n_per_article: 1
  unescape: false
```
在 **站点配置文件** 最后增加如下配置：
```
search:
  path: search.xml
  field: post
  format: html
  limit: 100
```

#### 设置hexo中的rss订阅
rss需要安装一个插件,终端进入 cd blog 后执行:
```
npm install hexo-generator-feed --save
```
刷新主页就可以看到rss

#### 配置Hexo站点的footer信息
底部footer可以显示hexo版权，theme版权，建站时间等个性化配置。
```
footer:
  since: 2015   #建站开始时间
  icon:
    name: user  #设置 建站初始时间和至今时间中间的图标 默认是一个'小人像'更改user为heart可以变成一个'心'
    color: "#808080" #更改图标的颜色 红色为 '#ff0000'
  powered:
    enable: true #是否开启hexo驱动
    version: true #是否开启hexo版本号
  theme:
    enable: true #是否开启theme驱动
    version: true #是否开启主题版本号
  custom_text: Hosted by <a target="_blank" rel="external nofollow" href="https://pages.coding.me"><b>Coding Pages</b></a> #这里的底部标识是为了添加coding page服务时的版权声明 打开注释就可以看到底部有一个 hosted by coding pages
```

#### 配置hexo中next主题样式选择
next一共提供了4种首页样式,按照自己喜好选择一个,切记选择一个其他主题后其他的主题之前一定要加上 #
```
# Schemes
#scheme: Muse		# 默认 Scheme，黑白主调，大量留白
#scheme: Mist		# Muse 的紧凑版本，整洁有序的单栏外观
#scheme: Pisces		# 双栏 Scheme，小家碧玉的清新
scheme: Gemini		# 新增 Scheme
```

#### 配置Hexo站点侧边栏宽度，头像信息，头像设置圆形及旋转等
```
sidebar:
  position: left  #选择pisces 或者 gemini 主题是 可以 选择侧边栏 位置 调整左边或者右边
  #position: right
  width: 300   # muse mist 主题 可以控制侧边栏的宽度 
  display: post   #如果是muse mist 主题 可以选择 侧边栏弹出动作  post 是 只在内容页弹出
  #display: always  #全局弹出
  #display: hide    #全局隐藏侧边栏 右下角有角标唤出
  #display: remove  #移除侧边栏
avatar:
  url: /images/avatar.gif  #设置头像资源的位置
  rounded: false  #开启圆形头像
  opacity: 1    #不透明的比例  0 就是完全透明
  rotated: false  #开启旋转
```

#### 配置Hexo站点的社交信息和友链
```
social: #和菜单设置格式一样  社交名字: 社交url || 社交图标 ,图标信息还是[fontawesome](https://fontawesome.com/v4.7.0/icons)
  GitHub: https://github.com/yourname || github
  E-Mail: mailto:yourname@gmail.com || envelope
  Google: https://plus.google.com/yourname || google
social_icons:
  enable: true  #显示社交图标
  icons_only: false #只显示图标的开关
#Blog rolls
links_icon: link  #友链的图标 参考上文
links_title: Links #友链的title  比如你可以更改为 友情链接
links_layout: block #友链摆放的样式,按块 一行一个
#links_layout: inline #按线摆放,一行很多个 切记 同时只能一种样式
links:
  Title: http://example.com/  #友链的地址
```

#### 配置Hexo站点文章摘要
```
scroll_to_more: true  #点击阅读全文后是否跳到<!--more-->标记处,设为false时点击阅读全文可以从头阅读
auto_excerpt: #是否自动截取摘要
  enable: false #设置为true则自动截取150字当做首页摘要
  length: 150   #自动截取的字数
```

#### 配置Hexo文章属性显示创建时间、修改时间、分类
```
post_meta:
  item_text: false  # 设为true 可以一行显示 文章的所有属性
  created_at: true  #显示创建时间
  updated_at:
    enabled: true   #显示修改的时间
    another_day: true #设true时 如果创建时间和修改时间一样则显示一个时间
  categories: true  #显示分类信息
```

#### hexo中的next增加页面阅读统计,字数统计,阅读时长
新增busuanzi阅读统计
```
busuanzi_count:
  enable: false  #设true 开启
  total_visitors: true  #总阅读人数 uv数
  total_visitors_icon: user  #阅读总人数的图标
  total_views: true  #总阅读次数 pv数
  total_views_icon: eye  #阅读总次数的图标
  post_views: true #开启内容阅读次数
  post_views_icon: eye #内容页阅读数的图标
```
新增字数统计,阅读时长,先安装插件
```
npm install hexo-symbols-count-time --save
```
**主题配置信息** 如下:
```
symbols_count_time:
  separated_meta: true  # false 会显示一行
  item_text_post: true  # 显示属性名称,设为false后只显示图标和统计数字,不显示属性的文字
  item_text_total: true #底部footer是否显示字数统计属性文字
  awl: 4 #计算字数的一个设置,没设置过
  wpm: 275 #一分钟阅读的字数
```
**站点配置** 新增如下:
```
symbols_count_time:
 #文章内是否显示
  symbols: true
  time: true
 # 网页底部是否显示
  total_symbols: true
  total_time: true
```

#### hexo中next主题给内容页里的代码块新增复制按钮
```
codeblock:
  copy_button:
  enable: true #增加复制按钮的开关
   show_result: true #点击复制完后是否显示 复制成功 结果提示
```

#### hexo中next主题文章主页配置微信公众号
```
# Wechat Subscriber
wechat_subscriber:
  enabled: true
  qcode: /images/wechat-qcode.jpg #微信公众号的二维码
  description: 这是我的公众号  #公众号描述文字
```

#### hexo中next主题配置微信,支付宝打赏
```
# Reward
reward_comment: 一毛也是爱~  #打赏描述
wechatpay: /images/wechat_pay.png #微信支付的二维码图片地址
alipay: /images/ali_pay.png  #支付宝的地址
#bitcoin: /images/bitcoin.png #这个是设置比特币的...
```

#### hexo中next主题新增 相关文章推荐
安装推荐文章的插件
```
npm install hexo-related-popular-posts --save
```
**主题配置信息** 如下:
```
related_posts:
  enable: true
  title: 相关文章推荐 # 属性的命名
  display_in_home: false #false代表首页不显示
  params:
    maxCount: 5 #最多5条
    #PPMixingRate: 0.0 #可以看github上 这个相关度介绍
    #isDate: true #是否显示 日期
    #isImage: false #是否显示配图
    isExcerpt: false #是否显示摘要
```

#### hexo中next主题的文章原创申明
```
post_copyright:
  enable: true #设置true就开启了
  license: <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/" rel="external nofollow" target="_blank">CC BY-NC-SA 4.0</a>
```

#### hexo中next主题更改code高亮样式
```
highlight_theme: normal #可选值为 normal | night | night eighties | night blue | night bright 这几个挑一个喜欢的
```

#### hexo中next主题配置网页body背景动态效果
4个效果同时只能开启一个
```
canvas_nest: false
three_waves: false
canvas_lines: false
canvas_sphere: false
```

#### 配置Hexo站点文章内页面图片点击放大
[fancybox](http://fancyapps.com/fancybox/3/) 一款优秀的弹出框JQuery插件。Github地址为：https://github.com/theme-next/theme-next-fancybox3 。
首先在themes/next目录下执行：
```
git clone https://github.com/theme-next/theme-next-fancybox3 source/lib/fancybox
```
在主题配置文件中开启fancybox
```
fancybox: true
```
- **注意**：clone完成后，删除 themes/next/source/lib/fancybox 目录下的 .git文件夹、.gitignore文件，以将主题文件作为本地项目管理

#### 配置fork me on github
点击[这里](<https://github.blog/2008-12-19-github-ribbons/>)挑选喜欢的样式，然后复制代码，粘贴到themes/next/layout/_layout.swig文件中
```
<div class="headband"></div>
```
下面，并把代码内href内的地址更换为你的github地址。

#### 修改文章底部标签样式
博客底部的标签样式默认为#tag，将其改为：
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528145934.jpg)
只需修改模版 /themes/next/layout/_macro/post.swig，搜索rel="tag">#，将 # 换成
```
<i class="fa fa-tag"></i>。
```

#### 版权声明
要想开启博客的版权功能，需要设置主题配置文件：
```
creative_commons:
  license: by-nc-sa
  sidebar: true
  post: true
```

### 参考文章
- [https://blog.csdn.net/marvinboy/article/details/83350437](https://blog.csdn.net/marvinboy/article/details/83350437)
- [https://www.cnblogs.com/wsmrzx/p/9439470.html](https://www.cnblogs.com/wsmrzx/p/9439470.html)
- [个人博客折腾之旅](https://x-d.xyz/2019/03/02/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%8A%98%E8%85%BE%E4%B9%8B%E6%97%85/)