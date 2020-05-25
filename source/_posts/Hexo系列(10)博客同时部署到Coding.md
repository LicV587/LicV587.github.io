---
title: Hexo系列(10)博客同时部署到Coding
tags:
  - Hexo
categories:
  - Hexo
toc: true
date: 2019-07-01 23:32:31
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701233152.jpg)

<!-- more -->

### 前言
因为某些原因，国内访问部署在Github的Hexo博客有些慢，而且有时候还访问不到，不太稳定。并且，因为百度无法索引到github，所以，百度搜索不到博客。就考虑把博客同时部署到coding。折腾了一番，终于成功了。


### 配置coding token
#### 博客的部署流程图
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701222756.jpg)

#### 生成coding的token
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701223130.jpg)

#### 复制token
因为这里的token只会显示一次，所以你必须要将token复制下来，保存到你认为安全的地方，以便复用
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701223143.jpg)

#### travis ci 配置coding token
登录travis，找到博客项目，点击进入博客的详情页，再点击settings，将刚刚复制的coding token配置进去。这里的CO_TOKEN就是配置的coding token。
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701223158.jpg)

### 修改.travis.yml
进入Hexo博客根目录，找到.travis.yml文件，修改它。具体内容如下：

```
language: node_js  #构建环境
node_js:
- stable  #选择当前稳定版本
branches:
  only:
  - dev   #这里替换你要监听的分支
cache:
  directories:
  - node_modules  #把node_modules文件夹放入缓存，节约每次构建的时间
before_install:
- npm install hexo-cli -g #安装Hexo脚手架工具
install:
- npm install  #安装package.json中的依赖
script:
- hexo clean
- hexo generate
after_success: #script阶段成功后执行，构建失败不会执行
  - git clone https://${GH_REF} .deploy_git  # GH_REF是最下面配置的仓库地址
  - cd .deploy_git
  - git checkout master
  - cd ../
  - mv .deploy_git/.git/ ./public/   # 这一步之前的操作是为了保留master分支的提交记录，不然每次git init的话只有1条commit
  - cd ./public
  - git init
  - git config user.name "lic"
  - git config user.email "acelichao@163.com"
  - git add .
  - git commit -m "Travis CI Auto Build at `date +"%Y-%m-%d %H:%M:%S"`"
  # Github Pages
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master 
  # Coding Pages
  - git push --force --quiet "https://acelichao:${CO_TOKEN}@${CO_REF}" master:master 

env:
 global:
   # Github Pages
   - GH_REF: github.com/LicV587/LicV587.github.io.git
   # Coding Pages
   - CO_REF: git.dev.tencent.com/acelichao/acelichao.coding.me.git
```

注意事项：
1. CO_REF 项目git地址必须要是https的，并且要将 **https://** 去掉
2. git push 语句中，比github语句多了一个用户名，就是 **acelichao:** 这块，这是你的coding用户名

### coding pages博客仓库配置
#### 新建coding pages仓库
博客仓库的名称必须是 xxx.coding.me，这里的xxx就是你的coding用户名。并且我们选择使用空的readme.md文档初始化仓库，因为必须初始化仓库后才能开启coding pages。待仓库配置完成后，可以删除readme.md文档。
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701225633.png)

#### 开启仓库的pages功能
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701225651.png)

#### 配置pages
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701225703.png)
注意事项：
1. 我们这里开启强制HTTPS访问
2. 并且使用自定义域名访问博客，你只需要在绑定域名处输入域名，点击绑定就行了

### 域名DNS解析配置
在上一步中，我们开启了https访问，并设置了自定义域名，但还无法正常访问，我们还要在DNS解析处设置，以支持个人域名同时绑定coding与github。

github的自定义域名解析式根据博客中的**CNAME**文件判断。
coding的自定义域名解析则是根据上面的配置判断的。

我用的是dnspod，因为腾讯云已经收购了dnspod，所以在腾讯云后台解析也是一样。

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190701230723.png)

如上图所示，我们分别设置2个CNAME解析记录指向github与coding，其中，指向coding的是默认类型，国外访问时则指向github。

注意：
1. 相同主机记录的CNAME解析类型不能相同
2. 在上面我们在coding 配置中启用https访问时，我们必须要先将指向github的解析暂时停止，只开启指向coding的解析。这是因为，coding的https证书是向Let's Encrypt申请的，它会验证我们的域名解析，如果这时开启了github的解析，怎么无法判断到底指向那个域名，导致报错。当证书状态显示正常后，则是证书申请成功，此时可以再次开启github解析了。

### 成功
至此，整个Hexo博客的coding部署就成功了。可以尽情的写博客，然后提交到github仓库，travis会帮我们自动编译，并发布到github和coding仓库。
现在再次访问博客，速度真是如火箭般快速。


