---
abbrlink: ''
categories:
- blog
date: '2022-11-14 13:31:45'
tags:
- hexo
title: centos搭建hexo博客（番外）
updated: '2022-11-14 16:44:46'
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

docker build -t ubuntu-blog:v1.1 .

docker images 

REPOSITORY    TAG              IMAGE ID       CREATED         SIZE
ubuntu-blog   v1.1             b3ce33692487   12 days ago     230MB
blog-test     v1.1             a7e9b03b1ae2   2 weeks ago     747MB

docker run -it --name blog-main -p 8080:4000 blog-test:v1.1 /bin/bash

docker run -d --name blog-main  -p 8080:4000 --privileged=true blog-test:v1.1 /usr/sbin/init(可以用systemctl)
```

查看hexo搭建是否完成
```
[root@920635e9bdec blog]# ll
total 568
-rw-r--r--.   1 root root      0 11月  9 01:23 _admin-config.yml
-rw-r--r--.   1 root root      0 11月  9 01:23 _config.landscape.yml
-rw-r--r--.   1 root root    179 11月  9 01:23 config.txt
-rw-r--r--.   1 root root   2894 11月 18 05:47 _config.yml
-rw-r--r--.   1 root root 253080 11月 23 04:41 db.json
drwxr-xr-x.   3 root root     83 11月  9 01:23 live2d-widget-model-miku
drwxr-xr-x. 856 root root  57344 11月 14 11:16 node_modules
-rw-r--r--.   1 root root    719 11月 14 11:16 package.json
-rw-r--r--.   1 root root 228167 11月  9 01:23 package-lock.json
drwxr-xr-x.  13 root root    173 11月 18 05:44 public
drwxr-xr-x.   2 root root     52 11月  9 01:23 scaffolds
drwxr-xr-x.   7 root root     80 11月 18 06:06 source
drwxr-xr-x.   4 root root     51 11月  9 01:23 themes
```
查看hexo环境
```
[root@920635e9bdec blog]# hexo -v
INFO  Validating config
hexo: 5.4.2
hexo-cli: 4.3.0
os: linux 3.10.0-1160.76.1.el7.x86_64 CentOS Linux 7 (Core)
node: 14.20.0
v8: 8.4.371.23-node.87
uv: 1.42.0
zlib: 1.2.11
brotli: 1.0.9
ares: 1.18.1
modules: 83
nghttp2: 1.42.0
napi: 8
llhttp: 2.1.5
openssl: 1.1.1q
cldr: 40.0
icu: 70.1
tz: 2021a3
unicode: 14.0
```

查看cnpm环境
```
[root@920635e9bdec blog]# cnpm -v
cnpm@8.4.0 (/usr/local/node-v14.20.0-linux-x64/lib/node_modules/cnpm/lib/parse_argv.js)
npm@8.19.3 (/usr/local/node-v14.20.0-linux-x64/lib/node_modules/cnpm/node_modules/npm/index.js)
node@14.20.0 (/usr/local/node-v14.20.0-linux-x64/bin/node)
npminstall@6.5.2 (/usr/local/node-v14.20.0-linux-x64/lib/node_modules/cnpm/node_modules/npminstall/lib/index.js)
prefix=/usr/local/node-v14.20.0-linux-x64 
linux x64 3.10.0-1160.76.1.el7.x86_64 
registry=https://registry.npmmirror.com
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
