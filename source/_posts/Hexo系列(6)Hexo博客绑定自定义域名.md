---
title: Hexo系列(6)Hexo博客绑定自定义域名
tags:
  - Hexo
categories:
  - Hexo
toc: true
date: 2019-04-29 10:10:59
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528160925.jpg)

<!-- more -->

> 如果你感觉直接使用 **用户名.github.io** 的域名作为你的博客链接不够专业，不够程序员的话，那么就购买一个域名解析绑定到你的博客

### 一、 购买域名注册
购买域名可以到域名服务商去购买，如国内的 [阿里云](https://wanwang.aliyun.com/)、[腾讯云](https://dnspod.cloud.tencent.com/)、国外的[Godaddy](https://sg.godaddy.com/zh/)等。
这里推荐 Godaddy ,快速无限备案，没有限制，附上域名注册指南：[https://www.zhudc.com/website/1669](https://www.zhudc.com/website/1669)

### 二、 配置域名解析
购买好了域名，我们还要让域名解析到博客的地址。首先，我们去到刚刚刚刚购买域名的服务商那里，进入域名管理的控制台，一般来说，域名服务商会同时提供域名注册和域名解析的服务，在我们购买好域名后，域名服务商会给我们配置上域名解析的服务。但是他们提供的域名解析服务可能收费或者是解析效果不好，这里推荐几个优秀的免费域名解析服务商，国内的有[dnspod](https://www.dnspod.cn/)、[cloudxns](http://www.cloudxns.net/)，国外的有[cloudflare](https://www.cloudflare.com/)。我的域名是从godaddy上购买的，使用的是dnspod的域名解析服务。下面以godaddy域名+dnspod解析来讲解具体操作步骤。

#### 1. 进入godaddy 账户中心，点击DNS，如图：
![](https://ws1.sinaimg.cn/large/005QTX3hgy1g2j8qa2p4nj31gq0mqjsw.jpg)

#### 2. 进入DNS管理后，找到域名服务器，点击更改，如图：
![](https://ws1.sinaimg.cn/large/005QTX3hgy1g2j8u30zqnj31h80ox0us.jpg)

#### 3. 选择自定义域名，添加dnspod的DNS，保存，如图：
![](https://ws1.sinaimg.cn/large/005QTX3hgy1g2j8yek6ksj31ci0oitad.jpg)

这样，就将我们的域名解析从godaddy转到了dnspod。

#### 4. 进入到dnspod，配置我们需要解析的域名，如图：
![](https://ws1.sinaimg.cn/large/005QTX3hgy1g2j9h3gbmdj316v0l0q7p.jpg)

#### 5. 然后点击刚刚添加的域名，进入解析管理，添加一条CNAME记录指向用户名.github.io 博客地址，如图：
![](https://ws1.sinaimg.cn/large/005QTX3hgy1g2j9kfk1sqj31600ch403.jpg)


### 三、在github 项目中设置我们的域名
#### 1. 在GitHub 项目中，点击settings，如图：
![](https://ws1.sinaimg.cn/large/005QTX3hgy1g2j9pd6oezj312m0jn40y.jpg)

#### 2. 找到Github Pages节点，在Custom domain处填入自定义域名，再开启强制HTTPS访问
![](https://ws1.sinaimg.cn/large/005QTX3hgy1g2j9pm96zaj311y0mmtae.jpg)


### 四、在Hexo 博客中设置自定义域名
#### 1. 在博客 站点配置文件 中，找到 URL 节点，设置自定义域名
```
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://blog.itjk.me	# 自定义域名
root: /
permalink: :year/:month/:day/:title.html
# permalink_defaults: :category/:title.html
```

#### 2. git bash 进入博客根目录，执行下列命令：
```
cd source
touch CNAME
```
然后打开 新建立的 **CNAME** 文件，在文件中填入自定义域名，如下：
```
blog.itjk.me
```

#### 3. 将更改后的项目执行以下命令行
```
hexo clean
hexo g -d
```
就将博客推送到了github仓库，然后访问 blog.itjk.me，就能看到博客了