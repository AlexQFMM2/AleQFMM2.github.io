---
title: centos搭建hexo博客（三）
categories: blog
date: 2022-11-14 13:31:45
tags: hexo
---

# 番外、dockerfile的写法

###### 所需镜像自行拉取，示例：

拉取ubuntu镜像

```
docker pull ubuntu:latest
```

###### 从初始到带主题创建

```
FROM centos:centos7.5.1804
MAINTAINER BLOG

#安装汉字库并且导入
RUN yum install kde-l10n-Chinese -y
RUN yum install glibc-common -y
RUN localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
ENV LC_ALL zh_CN.UTF-8

#安装node环境，配置node环境变量
ADD node-v14.20.0-linux-x64.tar.xz /usr/local/
ENV NODE_HOME  /usr/local/node-v14.20.0-linux-x64
ENV PATH $PATH:$NODE_HOME/bin

#安装cnpm和hexo
RUN npm install -g cnpm --registry=https://registry.npmmirror.com
RUN cnpm install hexo-cli -g
RUN mkdir blog
WORKDIR blog
RUN hexo init
RUN cnpm install

#修改博客基本信息
COPY themes /blog/themes
RUN sed -i "s/language: en/language: zh-CN/g" /blog/_config.yml
RUN sed -i "s/theme: landscape/theme: next/g" /blog/_config.yml
RUN sed -i "s/title: Hexo/title: AlexHome/g" /blog/_config.yml
RUN sed -i "s/author: John Doe/author: AlexQFMM/g" /blog/_config.yml

#添加标签和类别
RUN hexo new page tags && hexo new page categories
RUN sed -i '3 a type: "tags"' /blog/source/tags/index.md
RUN sed -i '3 a type: "categories"' /blog/source/categories/index.md

#制作live2d
COPY live2d /blog
RUN npm uninstall hexo-helper-live2d
RUN npm install --save hexo-helper-live2d
RUN cat config.txt >> _config.yml
```

###### 仅环境

```
FROM centos:centos7.5.1804
MAINTAINER BLOG


RUN yum install kde-l10n-Chinese -y
RUN yum install glibc-common -y
RUN localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
ENV LC_ALL zh_CN.UTF-8


ADD node-v14.20.0-linux-x64.tar.xz /usr/local/
ENV NODE_HOME  /usr/local/node-v14.20.0-linux-x64
ENV PATH $PATH:$NODE_HOME/bin
RUN npm install -g cnpm --registry=https://registry.npmmirror.com
RUN cnpm install hexo-cli -g
RUN mkdir blog
WORKDIR blog
```

###### ubuntu仅环境

```
FROM ubuntu:latest
MAINTAINER BLOG

ADD node-v14.20.0-linux-x64.tar.xz /usr/local/
ENV NODE_HOME  /usr/local/node-v14.20.0-linux-x64
ENV PATH $PATH:$NODE_HOME/bin
RUN npm install -g cnpm --registry=https://registry.npmmirror.com
RUN cnpm install hexo-cli -g
RUN mkdir blog
WORKDIR blog
```

运行Dockerfile以及运行容器

```
docker build -t blog-test:v1.1 .

docker run -it -p 8080:4000 blog-test:v1.1 /bin/bash

docker run -d --name blog-main  -p 8080:4000 --privileged=true blog-test:v1.1 /usr/sbin/init(可以用systemctl)
```

###### 宿主机复制文件进容器方法

```
docker ps -a
复制容器名

获得长id
docker  inspect -f '{{.ID}}'  容器名

复制
docker cp 文件名 长id:目标路径
```

