---
title: Hexo系列(8)PicGo和GitHub搭建个人图床工具
tags: 
  - Hexo
  - 图床
categories: 
  - Hexo
toc: true
date: 2019-05-28 17:34:17
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528173343.jpg)

<!-- more -->

> 最近微博图床限制外链，博客的部分图片挂了。想着使用github作为图床，研究了下怎么弄，记录下来，以备查询。
方便程度：★★★★☆
配置难度：★★☆☆☆
适用环境：win + mac + linux
需要工具：GitHub 账号 + PicGo 客户端
稳定性：背靠 GitHub 和微软，比自建服务器都稳
隐私性：这算是唯一缺点，你的图片别人可以访问


### 1. PicGo介绍
这是一款图片上传的工具，目前支持微博图床，七牛图床，腾讯云，又拍云，GitHub等图床，未来将支持更多图床。

所以解决问题的思路就是，将本地的文件，或者剪切板上面的截图发送图床，然后生成在线图片的链接

### 2.下载并安装PicGo

#### 1.进入下载页面
[https://github.com/Molunerfinn/PicGo/releases](https://github.com/Molunerfinn/PicGo/releases)

#### 2.下载安装包
选择最新版本就行了，我下载时最新版是2.1.2
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528164711.jpg)

#### 3.安装PicGo
双击下载的安装包，一路默认就行，如果360报错，就允许执行，安装完成后，打开软件如下图所示：
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528165026.png)

### 3.创建GitHub图床
#### 1.需要注册/登陆GitHub账号
这个自行注册，不会的百度谷歌下

#### 2.创建Repository
随便命名，我的比较简单，直接是img

#### 3.创建 token 并复制保存
生成一个Token用于操作GitHub repository
Settings -> Developer settings -> Personal access tokens

1.点击右上角头像，settings，进入设置
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528170352.png)

2.在页面最下找到 Developer settings，点击进入
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528170636.png)

3.点击Generate new token
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528170920.png)

4.Note随便填，勾选复选框 repo ，接着到页面底部 Generate token 就完成了
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528171107.png)

5.然后会生成一串字符 token，这个 token 只出现一次，所以要复制保存一下
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528171329.png)


### 4.配置PicGo客户端
如下图配置：
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528165451.jpg)

说明：
- 仓库名 即你的仓库名
- 分支名 默认 master
- Token 就是刚刚复制的那一串字符
- 存储路径 这个可以填也可以不填，填了的话图片就上传到 git 中 picgo这个文件夹
- 域名 https://raw.githubusercontent.com/LicV587/img/master这个要改一下，改成你自己的，格式：https://raw.githubusercontent.com/[仓库名]/master

然后点击确定，在点击设为默认图床，就OK了。


### 5.图片压缩工具Imagine使用
因为github仓库有容量限制，并且为了访问博客时，更快的加载，我们需将博客图片压缩，然后在上传到GitHub图床

#### 1.压缩工具选择
常用的图片压缩工具有一些几种：
1. TinyPNG
TinyPNG是非常著名的图片压缩工具，压缩性能强大，提供在线版，每次最多上传20张图片，最大不超过5M，且要手动下载，比较麻烦。它有客户端版本，但是只要MAC版。

2.谷歌的Squoosh
大名鼎鼎谷歌家的，压缩性能比较强大，在Chrome中可以安装为离线APP

3.腾讯智图
这是腾讯家的图片压缩工具，有离线和在线版，比较老了

4.Imagine
这是个人开发者开发的，离线使用，跨平台，开源，使用简单，最后我选择了这个

#### 2.下载安装Imagine
1. 下载地址，选择最新版就行了
[https://github.com/meowtec/Imagine/releases](https://github.com/meowtec/Imagine/releases)


2. 下载完成后，双击安装包，一路默认，安装完成，打开软件如下：
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528172805.png)

#### 3.使用
将要压缩的图片拖拽到Imagine中，压缩完成后，再使用PicGo上传到图床，再复制Markdown链接到博客中


### 5.参考
- [PicGo + GitHub 搭建个人图床工具](https://blog.csdn.net/yefcion/article/details/88412025)
- [PicGo+GitHub图床，让Markdown飞起](https://blog.csdn.net/wugenqiang/article/details/88931897)
- [TinyPNG 图片无损压缩有什么本地可替代的方案吗？](https://www.v2ex.com/t/508416)
