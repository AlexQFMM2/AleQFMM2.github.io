---
title: centos搭建hexo博客（一）
categories: blog
date: 2022-11-06 13:31:45
tags: hexo
---



# centos搭建hexo博客（一）

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

### 2、Docker的安装

###### 更新yum包（root）

```
yum update
```

###### 设置yum源

```
yum install -y yum-utils device-mapper-persistent-data lvm2
```

###### 使用阿里云访问，添加docker的yum源

```
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

###### 查看docker版本

```
yum list docker-ce --showduplicates | sort -r
```

###### 配置镜像加速器

```
进入阿里云控制台 ( https://cr.console.aliyun.com/#/accelerator )根据下面操作来
```

###### 安装docker

```
yum install docker-ce
```

###### 添加开机启动

```
systemctl start docker

systemctl enable docker

docker version
```

### 	3、图形化界面安装（可不做）

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

### 	4、node包安装

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

### 	5、hexo搭建

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

###     6、git安装

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



