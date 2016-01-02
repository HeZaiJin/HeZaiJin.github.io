title: 利用Github pages创建个人博客
date: 2016-01-02 21:29:46
tags: note
# 利用Github pages创建个人博客

---

**Github** 为每位用户提供了私人200M空间的***[Github pages][1]***。而我们可以利用它来创建自己的私人博客。
## Step 1:
创建一个特殊的repository .
例如，我的github用户名是HeZaijin , 那么对应的要创建一个HeZaiJin.github.io的repository .
## Step 2:
安装软件:Git  node.js 等。
安装软件不必赘述。请自行Google .
## Step 3: 
install hexo 工具。Hexo是一个博客管理工具，利用它，可以轻松的设计出一个漂亮的博客。
Git shell （Git for windows软件，推荐使用，漂亮的交互，省心的命令窗口）。
输入以下命令：
```
npm install hexo-cli -g
npm install hexo --save

#如果提示proxy有问题，可以输入以下命令
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
安装hexo 后，接下来就是搭建hexo 和管理repository了。
## Step 4:
clone HeZaiJin.github.io仓库到一个指定的文件夹<Github> 
```
hexo init <Github>
cd <Github>
npm install 
```
搭建完成。注意，最好现在checkout一个code分支，用于管理配置文件和资源文件。master分支用于保留后面生成的public文件夹下的所有文件 。
## Step 5:
如何配置和创建自己的blog，请自行研究：***[Hexo文档][2]***，***[学习使用Markdown][3]***.现在我们切到code分支，简单的编辑一下_config.yml 文件：
```
# Site
title: HeZaiJin
subtitle:
description: 今何在
author: HeZaiJin
```
然后保存，在命令行下输入**hexo s**:
```
hexo s 
```
创建本地静态网页，运行后可以访问http://localhost:4000 去查看刚刚修改的效果

```
hexo g
```
创建public文件夹,把public 生成的文件（它的子文件）推送到master分支，然后输入网址http://HeZaiJin.github.io 就可以查看自己的博客了。
     


 [1]: https://pages.github.com
 [2]: https://hexo.io/zh-cn/
 [3]: http://wsgzao.github.io/post/markdown/ 
 
  
---
