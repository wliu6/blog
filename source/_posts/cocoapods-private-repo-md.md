---
title: CocoaPods —— 创建私有库
date: 2016-07-20 13:36:29
tags: iOS
categories: development
copyright: true
---


> 本文介绍如何创建`CocoaPods`私有库，适合使用`CocoaPods`私有库。

<!--more-->

---


<br/>
# 创建CocoaPods私有库

业务需求，需要开发一些iOS的私有功能repo，供组织内部使用，并不对外开源，汇总了一下`CocoaPods`私有库的相关内容。

## 创建library
```
$ pod lib create <repo-name>
```
	What platform do you want to use?? [ iOS / macOS ]
	iOS
	What language do you want to use?? [ Swift / ObjC ]
	ObjC
	Would you like to provide a demo application with your library? [ Yes / No ]
	yes
	Which testing frameworks will you use? [ Specta / Kiwi / None ]
	Kiwi
	Would you like to do view based testing? [ Yes / No ]
	No
	What is your class prefix?
	BZ
简单介绍一下创建时可选项，第一个是想要使用的平台，第二个是想要使用的编程语言，第三个是是否为你的`library`创建一个`demo`工程；第四个是选择一个你习惯使用的单元测试框架，提供了两个常用的`BDD`框架；第五个是是否使用非死不可的`FBSnapshotTestCase`UI测试框架；第六个是设置一个类目前缀。

## 修改.podspec文件的信息

执行`pod lib create`命令会自动生成.podspec文件，是一个配置文件。新生成的`.podspec`包含所有参数，`.podspec`文件中有每个参数的注释。实际使用时，需要进行裁剪。常用的有
	1. name —— library的名字；
	2. version —— library的版本号；
	3. summary —— library的简介；
	4. platform —— library支持的平台；
	5. source —— library的源码引用；
	6. license —— library的开源协议；
	...

示例：
```
	Pod::Spec.new do |s|
	  s.name         = "KSZCMediateRouter"
	  s.version      = "0.1.0"
	  s.summary      = "路由中间件，target通信中介者."
	  s.platform     = :ios, '6.0'
	  s.description  = <<-DESC
	                    提供组件间通信，使用成本极低，支持常量参数，支持直接调用类方法。
	                   DESC
	  s.homepage     = "https://www.kaishiba.com/"
	  s.license      = { :type => "GPL", :file => "./LICENSE" }
	  s.author       = "wliu6"
	  s.source       = { :git => "http://gitlab.kaistart.net/iOS/KSZCMediateRouter.git", :tag => "#{s.version}" }
	  s.prefix_header_contents = '#import <Foundation/Foundation.h>'
	  s.source_files  = "KSZCMediateRouter/**/*.{h,m}"
	end
```



<br/>
# 上传CocoaPods私有库

## 添加repo
由于开发的是私有库，所以不会打包上传到[CocoaPods Public Specs Repo](https://github.com/CocoaPods/Specs)，而是上传到`Private Specs Repo`。添加私有repo，
```
$ pod repo add <specs-name> <specs-repo-url>
```

## 验证library
开发完成静态类库之后，需要验证一下类库是否符合要求（能否编译通过）。
```
$ pod lib lint xxx.podspec
```
常用option：

|option|用途|
|---|---|
|\-\-verbose|输出更详细的验证报告。|
|\-\-use-libraries|有引用静态库时使用，不然通不过验证。（验证过程默认认为library没有引用静态库）|
|\-\-only-errors|验证过程忽略警告（version<1.0.0）。|
|\-\-allow-warnings|验证过程忽略警告（version>=1.0.0）。|
|\-\-sources=master,<your-source>|library使用的源（如果library引用了私有源的库，必须传入源参数）。|




## 上传library
验证后，上传直接执行如下命令即可：

```
pod repo push <specs-name> xxx.podspec
```
常用option：

|option|用途|
|---|---|
|\-\-verbose|输出更详细的验证报告。|
|\-\-use-libraries|有引用静态库时使用，不然通不过验证。（验证过程默认认为library没有引用静态库）|
|\-\-only-errors|验证过程忽略警告（version<1.0.0）。|
|\-\-allow-warnings|验证过程忽略警告（version>=1.0.0）。|
|\-\-sources=master,<your-source>|library使用的源（如果library引用了私有源的库，必须传入源参数）。|



