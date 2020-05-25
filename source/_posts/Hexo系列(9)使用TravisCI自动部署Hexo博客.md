---
title: Hexo系列(9)使用TravisCI自动部署Hexo博客
tags:
  - Hexo
  - CI/CD
categories:
  - Hexo
toc: true
date: 2019-05-29 11:31:09
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528225537.jpg)

<!-- more -->

前面我们已经搭建好了一个人Hexo博客，但是还有一个问题，我们必须在本地写作，然后在编译，最后在推送到GitHub仓库。这其中，编译这一步非常耗时，也很不方便。我们需要写作，git push，之后的步骤都由机器自动执行。这就需要Travis CI出马了。

### 持续集成简介
持续集成(Continuous Integration)是一种软件开发实践。在持续集成中，团队成员频繁集成他们的工作成果，一般每人每天至少集成一次，也可以多次。每次集成会经过自动构建（包括自动测试）的检验，以尽快发现集成错误

### Travis CI简介
[Travis CI](https://docs.travis-ci.com/api) 是目前新兴的开源持续集成构建工具，它与jenkins，GO的明显区别在于采用yaml格式，同时它是在线服务，不像jenkins需要搭建本地环境。目前大多数的Github开源项目都已经移入到Travis CI的构建队列中，据说Travis CI每天运行超过4000次完整构建。

Travis CI分为2个版本：
- 免费版([https://travis-ci.org/](https://travis-ci.org/))
- 收费版([https://travis-ci.com/](https://travis-ci.com/))

这里我们只需要关注免费就可以了，Travis CI与Github集成的程度非常高，对于Github上的开源项目，可以免费在Travis上构建。

### 自动部署流程


#### Travis 构建 Hexo 的步骤
1. 首先在Github的博客仓库新建一个**dev**分支，然后把博客的整个源代码托管到这个分支
2. 每当我们在本地修改博客或者写好博文，将修改push到**dev**分支
3. Travis上可以对这个项目的**dev**分支设置钩子，每当检测到push操作的时候，就去**dev**分支clone代码到Travis
4. Travis执行构建脚本
5. Travis把构建结果push到博客仓库的master分支

根据上述的自动部署流程，我们唯一需要做的是push我们的博文到**dev**分支，其他的事情交给Travis

#### 如何将博客源代码提交到 dev 分支下
1. 首先在本地博客根目录下，执行git init将博客文件夹初始化为一个本地git仓库
2. 然后配置用户名和邮箱
```
git config user.name “github用户名”
git config user.email “github邮箱”
```
3. 本地新建一个**dev**分支，并切换到该分支下
```bash
git checkout -b dev
```
4. 提交本地文件
```
git add .
git commit -m “dev init”
```
5. 关联远程仓库
```
git remote add origin https://github.com/LicV587/LicV587.github.io.git
```
6. 将本地仓库**dev**分支提交到远程仓库的**dev**，注意这里会在远程仓库新建一个**dev**分支，并与本地的**dev**分支关联起来，以后本地的**dev**分支都会提交到对应的远程仓库**dev**分支
```
git push origin dev:dev
```

至此，我们就将博客源代码提交到GitHub了


### 配置Travis
#### 注册登录Travis
[Travis CI](https://travis-ci.org/)不需要单独注册，直接使用Github账号登录即可。点击左边的Sync account，会同步你的Github账号，然后右边选择你要Activate(激活)的仓库
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190529105126.jpg)

#### 生成GitHub Token
我们需要给予Travis CI对我们仓库进行相关操作的权限，所以我们需要生成相应的Token添加到Travis CI中。

首先，生成Github的Token，进入Github的设置中，如图：
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190529110041.jpg)

先给Token取一个名字，然后设置一些权限，其中红框内的权限是必须的，其他可以按需添加，如图：
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190529110226.jpg)

生成后的Token只会显示一次，所有需要复制保存 Token
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528171329.png)

#### 配置Travis CI
点击进入勾选的仓库，进入设置页面，如图：
图1
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190529110756.png)
图2
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190529110937.jpg)
这里设置构建的行为，。然后配置环境变量，起个名字，如：GH_TOKEN，并将刚才复制保存的Token填入，并将Display value in build log设置为OFF，关闭变量显示，避免Token泄露。

### 编写.travis.yml文件
在博客源文件根目录下新建 .travis.yml 文件，然后再文件中写入如下内容：
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
  - git config user.name "你的GitHub用户名"
  - git config user.email "你的GitHub邮箱"
  - git add .
  - git commit -m "Travis CI Auto Build at `date +"%Y-%m-%d %H:%M:%S"`"
  # Github Pages
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master 

env:
 global:
   # Github Pages
   - GH_REF: github.com/LicV587/LicV587.github.io.git
```

以后只要将博客提交到**dev**分支，travis就会帮你自动构建并部署。看到下面这张图提示 **passed** 就表示成功了
![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190529112101.jpg)

至此，Hexo博客集成Travis CI就完成，以后，只需写博客+git push就行了，其他的Travis CI都会自动帮你完成，持续集成的魅力就是这么大

### 参考文档
- [使用Travis CL自动部署Hexo](https://x-d.xyz/2019/04/02/%E4%BD%BF%E7%94%A8Travis-CI%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2Hexo/)
- [基于 Hexo 的全自动博客构建部署系统](https://kchen.cc/2016/11/12/hexo-instructions/#Travis-CI-%E6%8C%81%E7%BB%AD%E9%9B%86%E6%88%90)
- [使用travis-ci自动部署Hexo到github和coding](https://juejin.im/post/5afe61f5f265da0b8d422a3e)
- [使用Travis CI自动部署Hexo到GitHub](https://dmego.me/2017/10/13/deylpoy-hexo-with-TravisCI.html)
- [如何快速搭建一个有域名且持续集成的hexo博客(2.0版)](https://juejin.im/post/596e39916fb9a06baf2ed273)