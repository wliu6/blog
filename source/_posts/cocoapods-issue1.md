---
title: CocoaPods Error —— [!] RPC failed;
date: 2017-11-21 16:15:43
tags: iOS
categories: development
copyright: true
---

> 使用 pod install 时报错`RPC failed; curl 18 transfer closed with outstanding read data remaining `，于此记录下问题。


<!--more-->

---

<br/>
# 使用 CocoaPods 报错场景

使用 CocoaPods 安装依赖
```
$ pod install --verbose
```

报错信息如下：
```
error: RPC failed; curl 18 transfer closed with outstanding read data remaining 
fatal: The remote end hung up unexpectedly 
fatal: early EOF 
fatal: index-pack failed
```

<br/>
# 解决问题
分析一下报错信息，RPC因该是Remote Position Control的缩写，curl是常用的cmd下载工具，猜测应该是install过程的下载repo子过程出错了。CocoaPods的repo下载基于git，暂且猜测是git clone过程出问题了。

由于install是用了verbose option，从输出中可以拿到正在下载的repo下载的本地路径：
```
-> Installing xxxSDK (3.4.2)
 > Git download
 > Git download
     $ /usr/bin/git clone https://github.com/xxx/xxx.git /var/folders/f7/hx8jzq4d6yd60n4lvsjkqwm80000gn/T/d20180716-82840-xyqioz
     --template= --single-branch --depth 1 --branch v3.4.2
     Cloning into '/var/folders/f7/hx8jzq4d6yd60n4lvsjkqwm80000gn/T/d20180716-82840-xyqioz'...
```

```
$ cd /var/folders/f7/hx8jzq4d6yd60n4lvsjkqwm80000gn/T/d20180716-82840-xyqioz
$ while : ;do du -sh; sleep 1; done;
```
发现最终xxxSDK下载到96M就卡主不动了，nice，问题原因确定了，的确是在git clone过程出的问题，那么error phrase —— `RPC failed; curl 18 transfer closed with outstanding read data remaining` 应该是git clone过程的输出，定位到问题一切都好办，直接去搜一下git issue，果真找到解决办法：
```
$ git config --global http.postBuffer 524288000
```

<br/>
# 总结
出现这个问题的根本原因，是因为笔者在最近升级了MacOS系统，导致随着系统连带着的git也升级了，全局配置在version 25.x之后默认不超过100M，哎。。。

