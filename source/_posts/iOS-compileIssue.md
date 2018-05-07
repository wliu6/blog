---
title: xcodebuild issue (2) —— EXPORT FAILED
date: 2017-05-03 21:59:05
tags: iOS
categories: development
copyright: true
---

> 最近使用 xcodebuild 打包时，报错`导出Archive无法找到method`，找到原因总结了一下，于此记录。

<!-- more -->

---

# 报错信息
```
IDEDistribution: -[IDEDistributionLogging _createLoggingBundleAtPath:]: Created bundle at path '/var/folders/qm/jnw7j5p13wn9pm1zt8wyvw6h0000gn/T/app-thermo-ios-swift_2015-09-20_07-30-24.286.xcdistributionlogs'.  
error: exportArchive: exportOptionsPlist error for key 'method': expected one of {}, but found ad-hoc  
Error Domain=IDEFoundationErrorDomain Code=1 "exportOptionsPlist error for key 'method': expected one of {}, but found ad-hoc" UserInfo=0x7f865f953140 {NSLocalizedDescription=exportOptionsPlist error for key 'method': expected one of {}, but found ad-hoc}  
** EXPORT FAILED **  
```
遇到报错，作为一名程序🐒的我，立马Google、Stack Overflow了一下，然而并没有卵用，搜到的几篇帖子并不能解决问题，心中一千万只草泥马呼啸而过啊，我是代码的搬运工啊，搜不到这可咋办。
呆毛一转，灵机一动，去[Apple开发者中心](https://developer.apple.com/search/)和搜了一下，终于找到了[组织](https://forums.developer.apple.com/message/59201#59201)，然而并没有解决问题的方案。不由感叹“撸码不易，且撸且珍惜啊”。
当初写脚本打包，目的是为了自动化且方便。可以直接根据configuration打不同配置连接不同server的包并上传piv，自动发提测邮件给测试团队（毕竟程序员都懒嘛，既然是重复活儿，当然自动化最好）。自己挖的坑得自己填啊，撸起袖子使劲儿干吧骚年。

# 分析原因
既然找不到答案，就分析下原因吧(Ps:&ensp;不然咋交差啊，饭碗重要啊)。报错信息中提到`exportOptionsPlist error for key 'method': expected one of {}, but found ad-hoc`。明显是打包脚本使用导出ipa的plist文件使用了无法识别的mothod。查下文档吧。
```
$ xcodebuild -help
```
节选出`-exportOptionsPlist`option的method相关部分：
```
	method : String

		Describes how Xcode should export the archive. Available options: app-store, ad-hoc, package, enterprise, development, developer-id, and mac-application. The list of options varies based on the type of archive. Defaults to development.
```
但是明明xcodebuild的使用文档中通过的method可用参数包含ad-hoc。为啥导出会出错呢？
将compile输出的xxx.xcarchive的文件使用Xcode打开，
![img0](/resourse/iOS/CompileIssue/0.png)

为什么会是无法识别类型呢？
打开Xcode，看看Build Settings，
![img1](/resourse/iOS/CompileIssue/1.png)
会发现在archivectures有一个Base SDK的可选项，可选的option都是编译依赖的框架版本，有mac的，有iOS的，有watchOS的，也有tvOS的，等等。
查看脚本中compile过程执行cmd如下：
```
xcodebuild archive -workspace $project_name.xcworkspace \
-scheme $scheme_name \
-configuration $config_name \
-archivePath $outputpath/$project_name.xcarchive \
```
并没有使用xcodebuild的-sdk option，试着加一下这个option看看能否解决问题。将修改如下：
```
xcodebuild archive -workspace $project_name.xcworkspace \
-scheme $scheme_name \
-configuration $config_name \
-archivePath $outputpath/$project_name.xcarchive \
-sdk $CURRENT_EQUIPMENT_iOS_SDK
```
Oh my god~！成功了！

# 总结
如果你遇到同样的错误，可以在这里找到解决方案。

使用xcodebuild打包，需要执行两个步骤：
1. 编译生成.xcarchive包
2. 导出ipa

分析问题步骤发现，在步骤1的命令添加-sdk option就可以完美解决问题。
```
$ xcodebuild archive -workspace your_workspace -scheme your_scheme_name -configuration your_config_name -archivePath outputpath/your_project_name.xcarchive -sdk your_appoint_sdk
```

sdk option可接收的参数可以使用如下命令查看：
```
$ xcodebuild -showsdks
```
