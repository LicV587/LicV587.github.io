---
title: Hexo系列(5)Hexo添加评论系统
tags:
  - Hexo
categories:
  - Hexo
toc: true
date: 2019-04-28 17:46:36
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528221439.jpg)

<!-- more -->

> Hexo next主题支持多种评论系统，各有利弊，这里推荐使用Valine，一款基于LeanCloud的快速、简洁且高效的无后端评论系统。

> Valine 介绍：[https://valine.js.org/](https://valine.js.org/)

### 一、注册LeanCloud
#### 1. 注册一个账号
注册地址：[https://tab.leancloud.cn/dashboard/login.html#/signup](https://tab.leancloud.cn/dashboard/login.html#/signup)

#### 2. 创建应用
创建应用
![createapp.png](https://i.loli.net/2019/04/29/5cc649074a753.png)
填写应用信息
![appinfo.png](https://i.loli.net/2019/04/29/5cc6498b7f7f9.png)
在设置里面找到AppId和AppKey，添加到主题配置文件对应的位置
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528150557.jpg)

### 二、Hexo 开启 valine 评论系统
#### 1. 配置 Valine
打开 主题配置文件，路径为：/themes/next/_config.yml 文件，搜索 valine，开启 valine，设置 enable 为 true，如下：
```
valine:
  enable: true # 开启评论
  appid:  ~~~  # 刚申请 leancloud 的 appid
  appkey:  ~~~ # 刚生情 leancloud 的 appkey
  notify: false # 是否邮件推送 详情看 https://github.com/xCss/Valine/wiki
  verify: false # 
  placeholder: Just go go # 评论框里的placeholder信息
  avatar: mm # gravatar style
  guest_info: nick,mail,link # custom comment header
  pageSize: 10 # 一页显示的评论条数
  visitor: false # 
```