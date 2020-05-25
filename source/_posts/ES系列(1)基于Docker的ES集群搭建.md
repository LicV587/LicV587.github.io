---
title: ES系列(1)基于Docker的ES集群搭建
tags:
  - Elasticsearch
  - Docker
categories:
  - Elasticsearch
toc: true
date: 2019-10-22 17:01:45
---

![](https://i.loli.net/2020/05/25/E1Lgx4XMIQHGser.jpg)

<!-- more -->

## 前言
最近项目需要用到ES，以前没搞过，就自己研究折腾了下。经过一段时间的摸索，有了些许心得，写个博客记录下，以备查询。

### 版本说明
此处以 docker 安装 ES 双节点集群为例。
elatsicsearch:6.8.3 与springboot-2.1.7版本下的spring-data-elatsicsearch-3.1.10匹配。

## 安装准备
### 环境要求
1. centos7
2. docker
3. jdk8
4. ES6.8.3
5. IK中文分词插件

### JVM设置
调高JVM线程数限制数量，防止因线程数不够而报错
```bash
# 使用vim编辑系统配置文件
vim /etc/sysctl.conf
# 在最后添加以下内容并保存退出
vm.max_map_count=262144
# 启用配置
sysctl -p
```

### 端口设置
- 9200: master节点http访问端口
- 9300: master节点jar包访问端口
- 9201: slave节点http访问端口
- 9301: slave节点jar包访问端口

```bash
# 开放端口
firewall-cmd --zone=public --add-port=9200/tcp -permanent
firewall-cmd --zone=public --add-port=9300/tcp -permanent
firewall-cmd --zone=public --add-port=9201/tcp -permanent
firewall-cmd --zone=public --add-port=9301/tcp -permanent
# 重启防火墙
firewall-cmd --reload
```

### 外部挂载 数据及配置 文件创建
```bash
mkdir -p /home/docker/es
cd /home/docker/es
mkdir master-config
mkdir master-data
mkdir master-log
mkdir slave-config
mkdir slave-data
mkdir slave-log
```

### 赋予权限
6.x版本需要开启777权限，为文件夹下的所有子文件&子文件夹赋予权限
```bash
chmod -R 777 /home/docker/es
```

## ES 配置文件编写 
### master节点配置文件
位于 **/home/docker/es/master-config/es.yml**，将以下内容写入配置文件：
```bash
# master-config
cluster.name: ydool
node.name: master
network.bind_host: 0.0.0.0
network.publish_host: 192.168.1.56
http.port: 9200
transport.tcp.port: 9300
http.cors.enabled: true
http.cors.allow-origin: "*"
node.master: true 
node.data: true  
discovery.zen.ping.unicast.hosts: ["192.168.1.56:9300","192.168.1.56:9301"]
discovery.zen.minimum_master_nodes: 1
```

### slave节点配置文件
位于 **/home/docker/es/slave-config/es.yml**，将以下内容写入配置文件：
```bash
# slave-config
cluster.name: ydool
node.name: slave
network.bind_host: 0.0.0.0
network.publish_host: 192.168.1.56
http.port: 9201
transport.tcp.port: 9301
http.cors.enabled: true
http.cors.allow-origin: "*"
node.master: true 
node.data: true  
discovery.zen.ping.unicast.hosts: ["192.168.1.56:9300","192.168.1.56:9301"]
discovery.zen.minimum_master_nodes: 1
```

## 安装运行 ES 集群
### 拉取镜像
```bash
docker pull elasticsearch:6.8.3
```

### 启动 ES 集群
#### 启动master节点
```bash
docker run -d --name es-master --restart=always -p 9200:9200 -p 9300:9300 -v /home/docker/es/master-config/es.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /home/docker/es/master-data:/usr/share/elasticsearch/data -v /home/docker/es/master-log:/usr/share/elasticsearch/logs elasticsearch:6.8.3
```

#### 启动slave节点
```bash
docker run -d --name es-slave --restart=always -p 9201:9201 -p 9301:9301 -v /home/docker/es/slave-config/es.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /home/docker/es/slave-data:/usr/share/elasticsearch/data -v /home/docker/es/slave-log:/usr/share/elasticsearch/logs elasticsearch:6.8.3
```
可附加 **-e ES_JAVA_OPTS="-Xms256m -Xmx256m"** 参数来调整JVM大小，默认是2G

### 访问ES
访问 192.168.1.56:9200，出现版本信息，则集群启动成功.
使用chrome插件 Elastisearch-Head 可以可视化操作.

## 安装 IK 中文分词插件
### 进入镜像
```bash
docker exec -it es-master /bin/bash
```

### 安装插件
插件版本需要与ES版本对应，可访问下面的地址查看: **https://github.com/medcl/elasticsearch-analysis-ik/releases** 
```bash
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.8.3/elasticsearch-analysis-ik-6.8.3.zip
```

### 退出并重启镜像
```bash
exit
docker restart es-master
```

### slave节点执行同样操作