---
title: Cocoapods Error —— [!] Oh no, an error occurred.
date: 2017-06-26 11:23:06
tags: iOS
categories: development
copyright: true
---

> 使用 pod search 'xxx' 时报错 `JSON::ParserError - A JSON text must at least contain two octets!` ，于此记录下问题。

<!--more-->

---

<br/>
# 使用 Cocoapods 报错场景

使用 Cocoapods 搜索一个库
```
$ pod search 'xxx'
```

报错内容
```
[!] Oh no, an error occurred.

Search for existing GitHub issues similar to yours:
https://github.com/CocoaPods/CocoaPods/search?q=A+JSON+text+must+at+least+contain+two+octets%21&type=Issues

If none exists, create a ticket, with the template displayed above, on:
https://github.com/CocoaPods/CocoaPods/issues/new

Be sure to first read the contributing guide for details on how to properly submit a ticket:
https://github.com/CocoaPods/CocoaPods/blob/master/CONTRIBUTING.md

Don't forget to anonymize any private data!

/usr/local/lib/ruby/gems/2.3.0/gems/gh_inspector-1.0.2/lib/gh_inspector/sidekick.rb:91:in `block in validate_delegate': #<Pod::UserInterface::InspectorReporter:0x007fa2940bf9d0> does not handle inspector_successfully_recieved_report (RuntimeError)
	from /usr/local/lib/ruby/gems/2.3.0/gems/gh_inspector-1.0.2/lib/gh_inspector/sidekick.rb:90:in `each'
	from /usr/local/lib/ruby/gems/2.3.0/gems/gh_inspector-1.0.2/lib/gh_inspector/sidekick.rb:90:in `validate_delegate'
	from /usr/local/lib/ruby/gems/2.3.0/gems/gh_inspector-1.0.2/lib/gh_inspector/sidekick.rb:20:in `search'
	from /usr/local/lib/ruby/gems/2.3.0/gems/gh_inspector-1.0.2/lib/gh_inspector/inspector.rb:65:in `search_query'
	from /usr/local/lib/ruby/gems/2.3.0/gems/gh_inspector-1.0.2/lib/gh_inspector/inspector.rb:59:in `search_exception'
	from /usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-1.5.0/lib/cocoapods/user_interface/error_report.rb:119:in `search_for_exceptions'
	from /usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-1.5.0/lib/cocoapods/command.rb:67:in `report_error'
	from /usr/local/lib/ruby/gems/2.3.0/gems/claide-1.0.2/lib/claide/command.rb:396:in `handle_exception'
	from /usr/local/lib/ruby/gems/2.3.0/gems/claide-1.0.2/lib/claide/command.rb:337:in `rescue in run'
	from /usr/local/lib/ruby/gems/2.3.0/gems/claide-1.0.2/lib/claide/command.rb:325:in `run'
	from /usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-1.5.0/lib/cocoapods/command.rb:52:in `run'
	from /usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-1.5.0/bin/pod:55:in `<top (required)>'
	from /usr/local/bin/pod:22:in `load'
	from /usr/local/bin/pod:22:in `<main>'
```

不常使用 `cmd` 的小伙伴，看到报错应该会感到蛋蛋的忧桑吧~~~

<br/>
# 解决问题

静静看看，错误报文已经给出两个建议：
1. [搜索 github 已经存在的 issue](https://github.com/CocoaPods/CocoaPods/search?q=A+JSON+text+must+at+least+contain+two+octets%21&type=Issues)
2. [如果没有已存在的 issue 使用模板创建一个新的 issue](https://github.com/CocoaPods/CocoaPods/issues/new)

我们先试试搜索一下，是否存在相同的问题（是否有人踩过这个坑）。。。

![img0](/resourse/iOS/Cocoapods/0.png)

oh my god~ 有两个，纳尼！怎么这两个报错的描述和标题与我的报错 `[!] Oh no, an error occurred.` 都不一样啊。

算了去搜索引擎搜一搜 `[!] Oh no, an error occurred.`吧，Baidu、Google后并没什么卵用，都是让删除本地Pod repo缓存重新下载，日哦，下载不要时间的啊。

不要慌，慢慢来。再仔细看看错误报文(Ps:前面还有一页错误信心描述，粗心累死了多少程序🐒啊！！！)。
```
――― MARKDOWN TEMPLATE ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――

### Command


/usr/local/bin/pod search AFN


### Report

* What did you do?

* What did you expect to happen?

* What happened instead?


### Stack


   CocoaPods : 1.5.0
        Ruby : ruby 2.3.3p222 (2016-11-21 revision 56859) [x86_64-darwin16]
    RubyGems : 2.5.2
        Host : Mac OS X 10.12.6 (16G1504)
       Xcode : 9.0.1 (9A1004)
         Git : git version 2.13.5 (Apple Git-94)
Ruby lib dir : /usr/local/Cellar/ruby/2.3.3/lib
Repositories : coding-kaistart-ksspecs - https://git.coding.net/kaistart/KSSpecs.git @ dc1bb74b7ac2e82345dc0992c671755888e91795
               coding-pirate6-ksspecs - https://git.coding.net/pirate6/KSSpecs.git @ 941a46bfd3fb9c76966e7c6982bfccf028984bcf
               kaistart-ios-ksspecs - http://gitlab.kaistart.net/iOS/KSSpecs.git @ 207791a83fc010410b4e46dbfedd2452068e154d
               KSSpecs - git@git.coding.net:kaistart/KSSpecs.git @ dc1bb74b7ac2e82345dc0992c671755888e91795
               KSZC - https://git.coding.net/pirate6/KSSpecs.git @ 941a46bfd3fb9c76966e7c6982bfccf028984bcf
               master - https://github.com/CocoaPods/Specs.git @ a1c2e4450029d193bd8336d18577aff34c0136cc


### Plugins


cocoapods-deintegrate : 1.0.2
cocoapods-plugins     : 1.0.0
cocoapods-search      : 1.0.0
cocoapods-stats       : 1.0.0
cocoapods-trunk       : 1.3.0
cocoapods-try         : 1.1.0


### Error


JSON::ParserError - A JSON text must at least contain two octets!
/usr/local/Cellar/ruby/2.3.3/lib/ruby/2.3.0/json/common.rb:156:in `initialize'
/usr/local/Cellar/ruby/2.3.3/lib/ruby/2.3.0/json/common.rb:156:in `new'
/usr/local/Cellar/ruby/2.3.3/lib/ruby/2.3.0/json/common.rb:156:in `parse'
/usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-core-1.5.0/lib/cocoapods-core/source/manager.rb:251:in `stored_search_index'
/usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-core-1.5.0/lib/cocoapods-core/source/manager.rb:187:in `updated_search_index'
/usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-core-1.5.0/lib/cocoapods-core/source/manager.rb:118:in `search_by_name'
/usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-search-1.0.0/lib/cocoapods-search/command/search.rb:87:in `local_search'
/usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-search-1.0.0/lib/cocoapods-search/command/search.rb:63:in `run'
/usr/local/lib/ruby/gems/2.3.0/gems/claide-1.0.2/lib/claide/command.rb:334:in `run'
/usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-1.5.0/lib/cocoapods/command.rb:52:in `run'
/usr/local/lib/ruby/gems/2.3.0/gems/cocoapods-1.5.0/bin/pod:55:in `<top (required)>'
/usr/local/bin/pod:22:in `load'
/usr/local/bin/pod:22:in `<main>'


――― TEMPLATE END ――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――
```

![img1](/resourse/iOS/Cocoapods/1.jpg)

```
JSON::ParserError - A JSON text must at least contain two octets!
```

Nice，跟错误报文搜索到的 issue 完全匹配，去看看两个 issue 里的讨论。
找到解决办法了：
![img2](/resourse/iOS/Cocoapods/2.png)


具体解决方案如下：
1. `cd ~/Library/Caches/CocoaPods/`看看是否存在`search_index.json`文件;
2. 如果存在，看看`search_index.json`文件大小是否是0字节;
3. 如果`search_index.json`文件大小是否是0字节，删除该文件，重新执行`pod search 'xxx'`即可；

Cocoapods 会重新创建 search_index.json 文件，会有如下输出：
```
Creating search index for spec repo 'coding-xxx-xxx'.. Done!
Creating search index for spec repo 'kaistart-ios-xxx'.. Done!
Creating search index for spec repo 'master'.. Done!
```


<br/>
# 总结
去`Cocoapods`官网溜达一圈，原来`Cocoapods`在缓存策略在操作主机本地有二级缓存，一级在系统资源库的`Caches`目录下，第二级缓存在使用`Cocoapods`的工程目录下。

在执行`pod install`时，会先从本地缓存`copy`到工程目录下，再建立项目工程与项目目录下缓存的依赖关系，而从本地一级缓存查询就是利用这个搜索索引的json文件`search_index.json`，该json文件的内容就是一级缓存的目录。

每次更新仓库的时候`search_index.json`文件就会重新创建，出现`JSON::ParserError - A JSON text must at least contain two octets!`错误可能是因为在更新仓库的时候被打断了（或主动中断进程），所以Google到的通过重新更新仓库来解决这个问题，也是可以的。

但是国内的网络问题，更新起来很慢，故可以删除`search_index.json`文件，在使用`Cocoapods`命令`install`丨`search`等都会重新创建`search_index.json`文件，便可解决`JSON::ParserError - A JSON text must at least contain two octets!`问题。

终于告一段落，我佛慈悲，无量寿佛...


