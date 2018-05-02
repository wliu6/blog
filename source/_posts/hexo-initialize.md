---
title: hexo搭建博客
date: 2018-05-02 08:49:21
tags:
categories:
copyright: true
---

> 本文讲述如何使用“hexo” + “GitHub Page”搭建自己的博客。
![img0](/resourse/hexo/initialize/0.jpg)

<!--more-->

---


# 准备工作

## Dependencies
1. node
2. git（使用Mac、Ubuntu等自带git的操作系统不用处理）
3. GitHub

## Optional
1. register domain


<!-- 文章主体开始 -->

# 创建GitHub page仓库
使用“hexo” + “GitHub Page”搭建博客，首先需要一个GitHub Page repo。
![img1](/resourse/hexo/initialize/1.png)![img2](/resourse/hexo/initialize/2.png)


# Blog Compiling

## 全局安装hexo
```
$ sudo npm install -g hexo
```

## 初始化hexo工程
Node和Git都安装好后，首先创建一个文件夹，用于存放hexo的工程文件，然后进入该文件夹里初始化Hexo工程。由于在Mac下使用npm安装hexo，会自动关联安装hexo-cli，可以直接使用命令行工具进行初始化：
```
$ hexo init
```
文件夹下目录结构入下
![img3](/resourse/hexo/initialize/3.png)

## hexo命令
```
# 常用命令
$ hexo help # 查看帮助
$ hexo new "blog_name" # 新建文章
$ hexo new page "page_name" # 新建页面
$ hexo generate # 生成静态页面至public目录
$ hexo server # 开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
$ hexo deploy # 部署到GitHub
$ hexo version # 查看Hexo的版本

# 命令缩写
hexo n >> hexo new
hexo g >> hexo generate
hexo s >> hexo server
hexo d >> hexo deploy

# 命令组合
$ hexo s -g #生成并本地预览
$ hexo d -g #生成并上传
```
## _config.yml
_config.yml是博客样式的配置文件，文件中的注释已经说明每个参数的用途，再次不做缀述，实在不懂的可去 [hexo](https://hexo.io/docs/configuration.html) 官网查看configuration文档。编辑 _config.yml 的时候需要注意一下两点：
1. **冒号后面必须有一个空格，否则hexo可能会出问题。**
2. **每次编辑完_config.yml之后，记得校验一下YAML文件格式，可以使用脚本或者YAML online。如果YAML文件格式报错，会导致hexo命令执行时报错。**

## 编写blog
进入hexo工程根目录，创建一篇新博文：
```
$ hexo new "blog_name"
```
hexo-cli会echo博文所在物理路径：
![img4](/resourse/hexo/initialize/4.png)
接下来编辑的博客就可以了（编辑博客使用markdown语法），可以直接使用EMACS或者Vim，当然也可以用你常用的编辑器。
```
$ emacs ~/work/hexo/source/_posts/hexo-initialize.md
```

## 部署bolg
### 配置SSH Key
**通过GitHub进行部署需要使用ssh方案（根据hexo官方文档，hexo deploy并不支持GitHub的https方案）。**

检查本机已生成过ssh密钥
```
$ cd ~/. ssh 
```

如果有，直接copy
```
$ cat ~/.ssh/id_rsa.pub |pbcopy
```
如果没有，生成SSH Key
```
ssh-keygen -t rsa -C "#email#"
```
再进行copy，然后将复制ssh公钥配置到你的GitHub。
![img5](/resourse/hexo/initialize/5.png)![img6](/resourse/hexo/initialize/6.png)![img7](/resourse/hexo/initialize/7.png)

检测是否SSH Key是否配置成功
```
$ ssh -T git@github.com
```
如提示
`Are you sure you want to continue connecting (yes/no)?`
输入yes，配置成功输出：
`Hi liuxianan! You've successfully authenticated, but GitHub does not provide shell access.`
失败则会挂起当前执行进程，`Clt`+`c`退出即可。

### config hexo deployment
由于我们使用“hexo”+“GitHub Page”搭建blog，需要依赖于git进行部署，部署之前主要配置git依赖，编辑 _config.yml 的 deploy 配置：
```
deploy:
  type: git
  repository: git@github.com:wliu6/wliu6.github.io.git
  branch: master
```
如果想部署到其他云服务器可以参考：[deployment](https://hexo.io/docs/deployment.html)

部署命令：
```
$ hexo d -g
```



