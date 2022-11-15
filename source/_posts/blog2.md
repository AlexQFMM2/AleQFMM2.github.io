
---
title: centos搭建hexo博客（二）
categories: blog
date: 2022-11-10 15:35:45
tags: hexo
---




# centos搭建hexo博客（二）

## 一、评论区搭建

###### 注册Leancloud账号

```
https://console.leancloud.cn/login
```

![image-20221114192145068](image-20221114192145068.png)

注册完成后进入app控制面板->创建应用->创建开发版应用。

![image-20221114192202376](image-20221114192202376.png)

创建完之后所有应用->设置->安全中心->web安全域名。填入你的博客网址。

![image-20221114191933365](image-20221114191933365.png)

进入安全凭证，进入主题的_config.yml打开Valine，加入appid和appkey

![Quicker_20221114_192329](Quicker_20221114_192329.png)

在菜单栏添加留言版

```
hexo new page "guestbook"
```

进入网页，加入内容

```
---
title: 给我留言吧
date: 2022-02-18 20:04:25
type: "guestbook"
---

# 欢迎来到我的博客！
```

进入blog的_config.yml添加内容

```
menu:
  guestbook: /guestbook/ || fa fa-book
```

更改语言翻译（themes/next/languages）

```
menu:
  guestbook: 留言板
```



## 二、hexo的多机迁移

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

<!--more-->

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

## 三、hexo的后台搭建（qexo）

###### 1、申请MongoDB

​	[注册 MongoDB 账号 (opens new window)](https://www.mongodb.com/cloud/atlas/register)创建免费 MongoDB 数据库

![image-20221115094446726](image-20221115094446726.png)

区域**一定要选择 AWS / N. Virginia (us-east-1)** 

![image-20221115094519445](image-20221115094519445.png)

在 Clusters 页面点击 CONNECT，按步骤设置允许所有 IP 地址的连接），创建数据库用户，并记录数据库连接信息，密码即为你所设置的值

![image-20221115094540556](image-20221115094540556.png)

允许所有链接

![image-20221115094609823](image-20221115094609823.png)

复制shell脚本

![image-20221115094629012](image-20221115094629012.png)



###### 2、登录[New Project – Vercel](https://vercel.com/new/clone?repository-url=https://github.com/am-abudu/Qexo)进行创建

直接创建

![image-20221115094658221](image-20221115094658221.png)

​	创建项目后会报错。

![image-20221115094724722](image-20221115094724722.png)

进setting配置环境变量

![image-20221115094737649](image-20221115094737649.png)

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

![image-20221115094917875](mage-20221115094917875.png)

​	vercel密匙在vercel的setting中创建

![image-20221115094939000](image-20221115094939000.png)

​    Project ID，前往你的Qexo项目设置（最下方）

![image-20221115095012441](image-20221115095012441.png)

## 四、流水线自动部署

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

![image-20221115095130715](image-20221115095130715.png)

```
Title:
HEXO_DEPLOY_PRI
内容：
你的密匙
```

在项目仓库的setting--Deploy keys	

![image-20221115095213890](image-20221115095213890.png)

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
