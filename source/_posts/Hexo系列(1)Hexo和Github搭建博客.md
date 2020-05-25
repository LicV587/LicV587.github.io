---
title: Hexo系列(1)Hexo和Github搭建博客
tags:
  - Hexo
categories:
  - Hexo
toc: true
date: 2019-04-28 13:57:07
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528205023.jpg)

<!-- more -->

搭建本博客涉及到以下技术或工具，请知悉。
1. [Github Pages](https://pages.github.com/)——是Github提供的一种免费的静态网页托管服务，可以用来托管博客、项目官网等静态网页。支持 Jekyll、Hugo、Hexo等 编译静态资源。
2. [Hexo](https://hexo.io/zh-cn/)——是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
3. 本博客的所有操作均基于windows，其他平台类似。


### 一、Hexo 环境配置

#### 1、安装Git
[Git](https://git-scm.com/) 是一个分布式版本控制系统，用于项目的版本控制管理。对于Git的使用，可以学习一下[廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。

下载地址：[https://git-scm.com/download/win](https://git-scm.com/download/win)
下载完成后，双击安装包，一直选择默认就可，安装好后，在命令行输入以下命令查看git版本号：
```
git --version
```
如果正确显示版本号，则安装成功。
对于windows用户，由于众所周知的原因从上面的链接下载git for windows最好挂上一个代理，否则下载速度十分缓慢。也可以参考[这个页面](https://github.com/waylau/git-for-win)，收录了存储于百度云的下载地址。

#### 2、安装Node.js
[NodeJS](https://nodejs.org/) 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 Hexo是基于Node.JS驱动的一款博客框架。

下载地址：[http://nodejs.org/](http://nodejs.org/)
选择 LTS 长期支持版就可，下载后，双击安装包，勾选Add to PATH选项，其他的均默认就可，，安装好后，在命令行输入以下命令查看Node.js版本号：
```
node -v
```
如果正确显示版本号，则安装成功。


#### 3、 安装Hexo
上述环境搭建成功之后，我们就可以使用npm安装Hexo。在命令行输入已下命令：
```
$ npm install -g hexo-cli
```
命令行输入以下命令查看Hexo框架的版本号：
```
hexo -v
```
如果正确显示版本号，则安装成功。
注意：由于众所周知的原因，国内使用npm的官方镜像源速度会很慢，甚至连接不上。建议使用国内的 [淘宝镜像源https://npm.taobao.org/](https://npm.taobao.org/)
```
# 设置npm源为淘宝镜像
npm config set registry http://registry.npm.taobao.org/
# 还原为官方镜像源
npm config set registry https://registry.npmjs.org/
```
以后就可以使用cnpm命令替换npm命令。


### 二、配置博客本地仓库
安装 Hexo 完成后，打开命令行，cd 到一个适当的目录，如：c:\workspace\blog，执行下列命令，Hexo 将会在该文件夹中新建所需要的文件。
```
hexo init 
npm install
```

blog 目录即为本地博客存放的文件夹。
创建完成后，此文件夹的目录结构如下：
```
├── _config.yml # 网站的配置信息，您可以在此配置大部分的参数。 
├── package.json
├── scaffolds # 模版文件夹
├── source  # 资源文件夹，除 _posts 文件，其他以下划线_开头的文件或者文件夹不会被编译打包到public文件夹
|   ├── _drafts # 草稿文件
|   └── _posts # 文章Markdowm文件 
└── themes  # 主题文件夹
```

本地预览博客效果：
运行 hexo s 命令，其中s是server的缩写，在浏览器输入http://localhost:4000 预览博客效果。默认使用的主题是landscape。你可以更换你喜欢的主题。

至此，你本地的博客就已经成功搭建了，下面就是将其部署到Github Page上了。

### 三、 配置Github Pages 仓库

#### 1. 注册Github
首先如果你还没有 Github 账号，需要先[注册](https://github.com/join?source=header-home)一个。
注册完成之后，在官方的引导页面点击Start a project或者下面的new repository新建一个仓库。需要注意的是，Github 仅能使用一个同名仓库的代码托管一个静态站点。所以，你创建的用来作为Github page的仓库的名称必须为：用户名.github.io。
然后打开仓库，创建一个index.html文件。
在浏览器访问http://你的用户名.github.io 就可以看到刚创建的index.html网页效果。

#### 2. 配置SSH key
要使用git工具将本地博客部署到GithubPage上，需要配置一下SSH key。git bash中输入以下命令：
```
# 全局配置本地账户
git config --global user.name "用户名"
git config --global user.email "邮箱地址"
# 生成密钥
ssh-keygen -t rsa -C '上面的邮箱'
```
按照提示完成密钥的生成。通过查看本地~/.ssh/id_rsa.pub 文件内容，获取到你的 SSH key。
首次使用，还需要确认并添加主机到本地SSH可信列表。git bash执行以下命令：
```
ssh -T git@github.com 
```
若返回 Hi xxx! You've successfully authenticated, but GitHub does not provide shell access. 内容，则证明添加成功。
最后，我们还需要在github上添加刚刚生成的SSH key。在github的设置中，创建一个新的SSH key，将前面在本地id_rsa.pub文件中生成的key填到SSH key中。

#### 3. 部署到Github pages
Hexo支持 Git/Heroku/Rsync/OpenShift/FTPSync 等部署方式，选择git来部署的话，需要安装hexo-deployer-git插件。

第一步，在本地安装部署插件[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)，执行以下命令行
```
npm install hexo-deployer-git --save
```
第二步，打开本地项目根目录下的_config.yml配置文件，在文件末尾，填上如下配置。
```
deploy:
  type: git
  repo: git@github.com:用户名/用户名.github.io.git
  branch: master
```
上述配置项中，type是部署方式，repo是要部署到的目的仓库，branch是分支。
然后执行以下命令，就可以部署上传了。以下 g 是 generate 缩写，用于生成本地静态资源，d是deploy的缩写，用于部署到远程仓库：
```
hexo g -d
```
部署前最好能先执行一下 hexo clean 命令，清除缓存文件 (db.json) 和已生成的静态文件 (public)。在某些情况（尤其是更换主题后），如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令。
稍等一会，在浏览器访问网址： https://用户名.github.io 就会看到你的博客啦！！
详细的Hexo常用命令可参考[Hexo官方文档](https://hexo.io/zh-cn/docs/)。


### 四、参考文档：
1. [超详细Hexo+Github Page搭建技术博客教程【持续更新】](https://juejin.im/post/5c4730c9f265da61616efeec)
2. [Hexo系列(一) 搭建博客网站](https://www.cnblogs.com/wsmrzx/p/9439284.html)
3. [个人博客折腾之旅](https://x-d.xyz/2019/03/02/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%8A%98%E8%85%BE%E4%B9%8B%E6%97%85/)