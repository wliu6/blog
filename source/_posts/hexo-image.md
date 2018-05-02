---
title: hexo 使用图片
date: 2018-04-29 18:14:11
categories: blog
tags: hexo
copyright: true
---

> 使用hexo编辑bolg，需要使用markdown主要语言来编写。mark是一种标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。
> 在使用github时，repo简介说明文件——README.md文件，便是使用markdown编辑的。我在初次使用hexo编辑blog时，插入图片遇到一些问题，解决问题后，便书于此文。

<!--more-->

---

<br/>


# Markdown 使用图片


markdown插入图片代码:
```
![tmpimage](/path/to/you/image_asset/image)
```

效果如下:
![img0](/resourse/hexo/image/0.jpg)


在某些markdown编辑器(例如: MWeb)上，可以对图片进行更多操作。


修改图片宽度，只需要在图片描述后添加"-w"+"宽度"，即可修改宽度。
```
# 图片宽度已经被修改为66，高度会等比缩放
![tmpimage-w66](/path/to/you/image_asset/image)
```


修改图片水平方向的队列布局方式，字需要在图片描述后添加"-l"或"-r"或"-c"，即可修改队列布局方式。
```
# 居中显示
![tmpimage-c](/path/to/you/image_asset/image)
```
布局修饰与渲染效果对应关系如下表：

布局修饰|图片渲染效果
--------|---------
-l|靠左显示
-c|居中显示
-r|靠右显示

Note：
`1.以上语法糖可一起使用;`
`2.但github等git repo网站为编辑文档使用markdown plugin并不支持这些操作图片的语法糖，hexo使用的markdown插件也不支持操作图片的语法糖;`




<br/>


# hexo 使用图片

使用hexo搭建的博客，在编辑博客是插入图片却发现只能使用图片资源url。由于使用github page部署的静态站点，导致source下的图片无法直接当做文件资源通过url引用。但是又不想维护自己的图片资源server，安装hexo-asset-image插件便可以解决这个问题。
``` bash
# 该插件可以直接引用相对路径资源
$ npm install hexo-asset-image --save
```

在对于这些无法使用修饰图片的语法糖的markdown解释器，也可以直接通过img标签来修改图片的渲染效果。

修改图片宽高，使用height、width即可
```
<img src="/path/to/you/image_asset/image" height="330" width="495">
```
<img src="/resourse/hexo/image/2.jpg" width="200">


同理修改图片布局，使用style即可
```
<img src="/path/to/you/image_asset/image" style="margin-left:0px" height="70" >
```
<img src="/resourse/hexo/image/1.jpg" style="margin-left:0px" width="320">


<br/>
<br/>