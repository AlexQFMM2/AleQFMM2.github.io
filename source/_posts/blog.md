```
---
title: centos搭建hexo博客
categories: blog
date: 2022-11-06 13:31:45
tags: hexo
---
```



# centos搭建hexo博客

## 一、环境搭建

### 	1、网络配置

###### 更改网卡

```
 vi /etc/sysconfig/network-scripts/ifcfg-ens32 
```

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPADDR=192.168.10.100
NETMASK=255.255.255.0
GATEWAY=192.168.10.2
DNS=8.8.8.8
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens32
UUID=a7fcd28f-d8f2-4b72-83c0-6e60d26e257d
DEVICE=ens32
ONBOOT=yes
```

<!--more-->

###### 重启服务

```
systemctl restart network
```

###### 关闭防火墙

```
systemctl stop firewalld

systemctl disable firewalld
```

### 	2、图形化界面安装（可不做）

###### 升级yum包

```
yum upgrade -y
```

###### 安装图形化界面

```
yum groupinstall "X Window System" && yum groupinstall -y "GNOME Desktop"
```

###### 转到图形化

```
init 5
```

### 	3、node包安装

###### 官网（推荐下载14以上版本）

```
https://nodejs.org/en/download/
```

###### 	导入，解压，改名

```
tar -xvf node-v14.20.0-linux-x64.tar.xz -C /usr/local/src/

mv -v node-v14.20.0-linux-x64 node
```

###### 	设置环境变量

```
vi /etc/profile

末尾加
export NODE_HOME=/usr/local/src/node/
export PATH=$NODE_HOME/bin:$PATH

source /etc/profile
node -v
```

######     安装cnpm

```
npm install -g cnpm --registry=https://registry.npmmirror.com

cnpm -v
```

### 	4、hexo搭建

###### hexo环境搭建

```
cnpm install hexo-cli -g   

mkdir blog
cd blog/

 hexo init
 hexo -v
 cnpm install

 hexo s
```

结果：
INFO  Validating config
INFO  Start processing
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.

http:虚拟机ip:4000（主机）

复制超链接进浏览器即可预览博客

###     5、git安装

```
yum install -y git
```

###### 取消git代理（TCP connection reset by peer再不行就科学上网）

```
git config --global --unset http.proxy
git config --global --unset https.proxy
```



## 二、hexo博客的自定义

```
hexo s  运行预览博客页面

修改配置文件为  vi _config.yml
```

### 1、主题变更

###### 下载

```
git clone https://github.com/theme-next/hexo-theme-next.git themes/next
```

###### 变更（blog目录下）

原

```
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: landscape
```

改

```
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next
```

### 2、语言以及其他显示信息

###### 变更（blog目录下）

原

```
# Site
title: Hexo
subtitle: ''
description: ''
keywords:
author: John Doe
language: en
timezone: ''
```

改

```
# Site
title: AlexHome
subtitle: ''
description: ''
keywords:
author: Alex
language: zh-CN
timezone: ''
```

### 3、分栏

###### 变更（blog/themes/next目录下）

原

```
# Schemes
scheme: Muse
#scheme: Mist
#scheme: Pisces
#scheme: Gemini
```

改

```
# Schemes
scheme: Pisces
#scheme: Mist
#scheme: Pisces
#scheme: Gemini
```

### 4、更改背景

###### 将需要做背景的图片放入blog/themes/next/source/images/路径下

```
cp background.jpg blog/themes/next/source/images/
```

###### 末尾新增（blog/themes/next/source/css/_schemes/Pisces（主题样式名）/index.styl）

```
body {

    background:url(/images/background.jpg);

    background-repeat: no-repeat;

    background-attachment:fixed; //不重复

    background-size: cover;      //填充

    background-position:50% 50%;

}
```

###### 侧边栏透明度（/blog/themes/next/source/css/_schemes/Pisces/ _sidebar.styl）

原

```
.sidebar {
  background: var(--content-bg-color);
  box-shadow: none;
  margin-top: 100%;
  position: static;
  width: $sidebar-desktop;
```

```
.sidebar-inner {
  background: var(--content-bg-color);
  border-radius: $border-radius;
  box-shadow: $box-shadow;
  box-sizing: border-box;
  color: var(--text-color);
```

改

```
.sidebar {
  background: rgba(255,255,255,0.3);
  box-shadow: none;
  margin-top: 100%;
  position: static;
  width: $sidebar-desktop;
```

```
.sidebar-inner {
  background: rgba(255,255,255,0.3);
  border-radius: $border-radius;
  box-shadow: $box-shadow;
  box-sizing: border-box;
  color: var(--text-color);
"_sidebar.styl" 123L, 2082C
```

###### 头部透明（/blog/themes/next/source/css/_schemes/Pisces/ _layout.styl）

原

```
.header-inner {
  background: var(--content-bg-color);
  border-radius: $border-radius-inner;
  box-shadow: $box-shadow-inner;
  overflow: hidden;
  padding: 0;
  position: absolute;
  top: 0;
  width: $sidebar-desktop;
```

改

```
.header-inner {
  background: rgba(255,255,255,0.3);
  border-radius: $border-radius-inner;
  box-shadow: $box-shadow-inner;
  overflow: hidden;
  padding: 0;
  position: absolute;
  top: 0;
  width: $sidebar-desktop;
```

###### 主题框透明（/blog/themes/next/source/css/_schemes/Pisces/ _layout.styl）

原

```
.content-wrap {
  background: var(--content-bg-color);
  border-radius: $border-radius-inner;
  box-shadow: $box-shadow-inner;
  box-sizing: border-box;
  padding: $content-desktop-padding;
  width: $content-wrap;
```

改

```
.content-wrap {
  background: rgba(255,255,255,0.3);
  border-radius: $border-radius-inner;
  box-shadow: $box-shadow-inner;
  box-sizing: border-box;
  padding: $content-desktop-padding;
  width: $content-wrap;
```

### 5、添加标签（分类）页

###### 创建标签页（blog目录下）

```
hexo new page tags
```

###### 修改tags页面

```
cd /source/tags

vi index.md

title: tags
date: 2022-11-02 15:56:30
type: "tags"
```

###### 添加tag（/blog/themes/next）

```
menu:
  home: / || fa fa-home
  #about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  #categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat
```

###### ps：分类页同理，把所有的tags改为categories即可

### 6、添加头像

###### /blog/themes/next目录下

```
avatar:
  # Replace the default image and set the url here.
  url: #/images/avatar.gif
  # If true, the avatar will be dispalyed in circle.
  rounded: false
  # If true, the avatar will be rotated with the cursor.
  rotated: false
```

改

```
avatar:
  # Replace the default image and set the url here.
  url: /images/Avatar.jpg
  # If true, the avatar will be dispalyed in circle.
  rounded: true
  # If true, the avatar will be rotated with the cursor.
  rotated: true
```

### 7、live2d制作

###### 更新依赖（blog目录下）

```
npm uninstall hexo-helper-live2d

npm install --save hexo-helper-live2d
```

###### 导入live2d包（blog目录下）

```
cp -rvf live2d-widget-model-miku blog/
```

###### 编写配置（blog目录下）

```
live2d:
  enable: true
  pluginModelPath: assets/
  model:
    use: live2d-widget-model-miku
  display:
    position: left
    width: 200
    height: 400
  mobile:
    show: true
```

## 三、博客的编写

###### 生成命令

```
hexo new 博客标题
```

###### 生成目录

```
blog/source/_posts
```

###### 收缩全文

```
在需要收起的行下面添加

<!--more-->
```

## 四、博客的上传

```
github创建仓库，与用户名相同（小写）
```

###### 创建git密匙

```
ssh-keygen -t rsa -C "your_email@youremail.com"

cat ~/.ssh/id_rsa.pub
```

###### 复制下来点击头像进入setting----SSH创建新ssh

###### 执行

```
ssh -T git@github.com
```

###### 打开项目，复制git的地址

###### 修改站点信息（blog目录下）

```
deploy:
  type: 'git'
  repo: 'git@github.com:AlexQFMM2/AleQFMM2.github.io.git'
  branch: master
```

###### 登录

```
git config --global user.name "yourname"
git config --global user.email "youremail"
```

###### 安装上传插件

```
cnpm install hexo-deployer-git --save

hexo g -d  上传
```

###### 进入项目库setting--pages修改访问源即可访问你的博客网页



## 番外、dockerfile的写法

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

docker run -it blog-test:v1.1 /bin/bash

docker run -d --name blog-main --privileged=true blog-test:v1.1 /usr/sbin/init(可以用systemctl)
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

## 五、hexo的多机迁移

```
push 推
pull 拉
```

首先在github上增加分支，命名为hexo，并设为默认

在主机本地任意目录clone下载

```
git clone xxx
```

接下来在克隆到本地的目录中，把除了.git 文件夹外的所有文件都删掉，如果你之前克隆过[theme](https://www.zhihu.com/search?q=theme&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A489124966})中的主题文件，那么应该把主题文件中的`.git`文件夹删掉

 把之前我们写的博客源文件全部复制过来，除了`.deploy_git`。这里应该说一句，复制过来的源文件应该有一个`.gitignore`，用来忽略一些不需要的文件，如果没有的话，自己新建一个，在里面写上如下，表示这些类型文件不需要git：

```text
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

然后

```
git add .
git commit -m "add branch"
git push 
```

###### 更换电脑

###### 安装git

```
yum install -y git 
```

###### 设置邮箱和密匙

```text
git config --global user.name "yourgithubname"
git config --global user.email "yourgithubemail"
```

设置ssh key

```text
ssh-keygen -t rsa -C "youremail"

cat ~/.ssh/id_rsa.pub
#生成后填到github
```

将刚刚上传的克隆下来，然后进入克隆到的文件夹：

```text
cd xxx.github.io
cnpm install
cnpm install hexo-deployer-git --save
```

```
hexo g -d
```

不要忘了，每次写完最好都把源文件上传一下

```text
git add .
git commit –m "xxxx"
git push 
```

如果是在已经编辑过的电脑上，已经有[clone](https://www.zhihu.com/search?q=clone&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A489124966})文件夹了，那么，每次只要和远端同步一下就行了

```text
git pull
```

## 六、hexo的后台搭建（qexo）

###### 1、申请MongoDB

​	[注册 MongoDB 账号 (opens new window)](https://www.mongodb.com/cloud/atlas/register)创建免费 MongoDB 数据库，区域**一定要选择 AWS / N. Virginia (us-east-1)** 在 Clusters 页面点击 CONNECT，按步骤设置允许所有 IP 地址的连接），创建数据库用户，并记录数据库连接信息，密码即为你所设置的值

###### 2、登录[New Project – Vercel](https://vercel.com/new/clone?repository-url=https://github.com/am-abudu/Qexo)进行创建

​	创建项目后会报错。进setting配置环境变量

| 名称         | 意义                                            | 示例                                    |
| ------------ | ----------------------------------------------- | --------------------------------------- |
| DOMAINS      | 你所允许通信的安全域名 注意双引号而且是英文半角 | [".vercel.app"]                         |
| MONGODB_HOST | MongoDB 数据库连接地址                          | mongodb+srv://cluster0.xxxx.mongodb.net |
| MONGODB_PORT | MongoDB 数据库通信端口 默认应填写 27017         | 27017                                   |
| MONGODB_USER | MongoDB 数据库用户名                            | abudu                                   |
| MONGODB_DB   | MongoDB 数据库名                                | Cluster0                                |
| MONGODB_PASS | MongoDB 数据库密码                              | JWo0xxxxxxxx                            |

###### 3、初始化

​	github密匙在github-头像-setting中创建

​	vercel密匙在vercel的setting中创建

​    Project ID，前往你的Qexo项目设置（最下方）

## 七、流水线自动部署

######     ubtuntu容器安装git

```
apt-get update
apt-get upgrade
apt install git
```

​    部署hexo（详情请看多机迁徙）

###### 1、在ubuntu中创建密匙

```
ssh-keygen -f github-deploy-key -C "HEXO CD"

cat /root/.ssh/id_rsa
```

密匙

```
-----BEGIN OPENSSH PRIVATE KEY-----
*******
-----END OPENSSH PRIVATE KEY-----
```

在项目仓库的setting--Secrets--Actions--New  repository sercet

```
Title:
HEXO_DEPLOY_PRI
内容：
你的密匙
```

在项目仓库的setting--Deploy keys	

```
Title:
github-deploy-key.pub
内容
你链接git的公匙（提示重复，就去头像--setting中删除对应公匙）
```

###### 2、创建流水线

项目仓库--Action--new workflow选一个空白来写

```
name: HEXO CI
 
on:
  push:
    branches:
    - hexo  	 //源代码分支
 
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14.x]
 
    steps:
      - uses: actions/checkout@v1
 
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}
 
      - name: Configuration environment
        env:
          HEXO_DEPLOY_PRI: ${{secrets.HEXO_DEPLOY_PRI}}
        run: |
          mkdir -p ~/.ssh/
          echo "$HEXO_DEPLOY_PRI" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name "AlexQFMM2"    //id
          git config --global user.email "2161582897@qq.com"   //邮箱
      - name: Install dependencies
        run: |
          npm i -g hexo-cli
          npm i
      - name: Deploy hexo
        run: |
          hexo clean && hexo generate && hexo deploy
```

确保仓库**_config.yml**中存在

```
deploy:
  type: 'git'
  repo: 'git@github.com:AlexQFMM2/AleQFMM2.github.io.git'
  branch: master
```

试着推送吧
