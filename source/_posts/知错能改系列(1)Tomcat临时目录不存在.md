---
title: 知错能改系列(1)Tomcat临时目录不存在
tags:
  - Java
  - Bug
categories:
  - Java
  - Bug
toc: true
date: 2020-05-25 14:52:45
---

![](https://i.loli.net/2020/05/25/qDh6WGEaL2v3Mkz.jpg)

<!-- more -->

## 问题场景
最近项目中的上传文件功能报错，以前使用正常，也没有修改代码

## 报错日志
```bash
Failed to parse multipart servlet request; nested exception is java.io.IOException: The temporary upload location [/tmp/tomcat.492329695199971531.8088/work/Tomcat/localhost/ROOT] is not valid
```

## 产生原因
在linux系统中，springboot应用服务再启动（java -jar 命令启动服务）的时候，会在操作系统的/tmp目录下生成一个tomcat*的文件目录，上传的文件先要转换成临时文件保存在这个文件夹下面。

由于临时/tmp目录下的文件，在长时间（10天）没有使用的情况下，就会被系统机制自动删除掉。所以如果系统长时间没有使用到临时文件夹，就可能导致上面这个问题。

## 解决办法
### 临时解决办法(可临时避免出现该问题，一段时间后该问题还会暴露)
1. 重启项目，就会重新生成一个临时目录

2. 新建一个同名文件夹
    ```bash
    mkdir -p /tmp/tomcat.492329695199971531.8088/work/Tomcat/localhost/ROOT
    ```

### 永久解决办法
1. application.yml 重新配置一个文件目录,然后重启项目
    ```bash
    # 存放Tomcat的日志、Dump等文件的临时文件夹，默认为系统的tmp文件夹
    server:
      tomcat:
        basedir: /data/apps/temp
    ```
2. 在application.yml文件中设置multipart location ，并重启项目
    ```bash
    spring:
      servlet:
        multipart:
          location: /data/apps/temp
    ```

3. 添加启动参数，并重启
    ```bash
    -java.tmp.dir=/data/apps/temp/
    ```

4. 配置类配置临时文件存储目录
    ```java
    @Bean
    MultipartConfigElement multipartConfigElement() {
        MultipartConfigFactory factory = new MultipartConfigFactory();
        factory.setLocation(tmepPath);
        return factory.createMultipartConfig();
    }
    ```
