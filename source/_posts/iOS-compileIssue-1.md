---
title: xcodebuild issue (1) —— unable to find utility "PackageApplication"
date: 2017-01-05 16:10:04
tags: iOS
categories: development
copyright: true
---
> 更新到Xcode8.3之后发现，之前的打包脚本不能用了，不能用了。
本文介绍如何解该问题。

<!-- more -->

---

<br/>

# 脚本报错信息
```
xcrun: error: unable to find utility "PackageApplication", not a developer tool or in PATH
```
<br/>

# 分析原因并解决
## 方案一
无法找到"PackageApplication"，想了是不是把这个软件放到该目录下就可以了。先去找个旧版的Xcode里面copy一份过来
放到下面这个目录：
`/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/`
继续`xcrun`，发现PackageApplication无法执行，给个执行权限就好了。
```
$ sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer/
$ chmod +x /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/PackageApplication
```

## 方案二
后来查了下文档，发现
```
PackageApplication is removed from Xcode 8.3.
```
在强行使用`PackageApplication`的话不太友好。Apple推荐Xcode&ensp;8.3之后使用xcodebuild导出.ipa文件。将导入.ipa文件的是cmd换成如下：

```
xcodebuild \
-exportArchive \
-archivePath "${PROJECT_BUILDDIR}/${SCHEME_NAME}.xcarchive" \
-exportPath "${BUILD_OUTPUT_DIR}"
```
发现汇报错：
```
xcodebuild: error: The flag -exportOptionsPlist is required when specifying -exportArchive.
```
原来通过.xcarchive导出.ipa，有一个必要参数-exportOptionsPlist，改成如下，便可成功打包。
```
xcodebuild \
-exportArchive \
-archivePath "${PROJECT_BUILDDIR}/${SCHEME_NAME}.xcarchive" \
-exportOptionsPlist "${EXPORT_PLIST}" \
-exportPath "${BUILD_OUTPUT_DIR}"
```
但是这个option的参数是啥？
直接看文档吧。
```
$ xcodebuild -help
```
```
# -exportOptionsPlist 参数说明
-exportOptionsPlist PATH    specifies a path to a plist file that configures archive exporting

# plist 文件的参数说明如下
Available keys for -exportOptionsPlist:

	compileBitcode : Bool

		For non-App Store exports, should Xcode re-compile the app from bitcode? Defaults to YES.

	embedOnDemandResourcesAssetPacksInBundle : Bool

		For non-App Store exports, if the app uses On Demand Resources and this is YES, asset packs are embedded in the app bundle so that the app can be tested without a server to host asset packs. Defaults to YES unless onDemandResourcesAssetPacksBaseURL is specified.

	iCloudContainerEnvironment : String

		If the app is using CloudKit, this configures the "com.apple.developer.icloud-container-environment" entitlement. Available options vary depending on the type of provisioning profile used, but may include: Development and Production.

	installerSigningCertificate : String

		For manual signing only. Provide a certificate name, SHA-1 hash, or automatic selector to use for signing. Automatic selectors allow Xcode to pick the newest installed certificate of a particular type. The available automatic selectors are "Mac Installer Distribution" and "Developer ID Installer". Defaults to an automatic certificate selector matching the current distribution method.

	manifest : Dictionary

		For non-App Store exports, users can download your app over the web by opening your distribution manifest file in a web browser. To generate a distribution manifest, the value of this key should be a dictionary with three sub-keys: appURL, displayImageURL, fullSizeImageURL. The additional sub-key assetPackManifestURL is required when using on-demand resources.

	method : String

		Describes how Xcode should export the archive. Available options: app-store, ad-hoc, package, enterprise, development, developer-id, and mac-application. The list of options varies based on the type of archive. Defaults to development.

	onDemandResourcesAssetPacksBaseURL : String

		For non-App Store exports, if the app uses On Demand Resources and embedOnDemandResourcesAssetPacksInBundle isn't YES, this should be a base URL specifying where asset packs are going to be hosted. This configures the app to download asset packs from the specified URL.

	provisioningProfiles : Dictionary

		For manual signing only. Specify the provisioning profile to use for each executable in your app. Keys in this dictionary are the bundle identifiers of executables; values are the provisioning profile name or UUID to use.

	signingCertificate : String

		For manual signing only. Provide a certificate name, SHA-1 hash, or automatic selector to use for signing. Automatic selectors allow Xcode to pick the newest installed certificate of a particular type. The available automatic selectors are "Mac App Distribution", "iOS Developer", "iOS Distribution", "Developer ID Application", and "Mac Developer". Defaults to an automatic certificate selector matching the current distribution method.

	signingStyle : String

		The signing style to use when re-signing the app for distribution. Options are manual or automatic. Apps that were automatically signed when archived can be signed manually or automatically during distribution, and default to automatic. Apps that were manually signed when archived must be manually signed during distribtion, so the value of signingStyle is ignored.

	stripSwiftSymbols : Bool

		Should symbols be stripped from Swift libraries in your IPA? Defaults to YES.

	teamID : String

		The Developer Portal team to use for this export. Defaults to the team used to build the archive.

	thinning : String

		For non-App Store exports, should Xcode thin the package for one or more device variants? Available options: <none> (Xcode produces a non-thinned universal app), <thin-for-all-variants> (Xcode produces a universal app and all available thinned variants), or a model identifier for a specific device (e.g. "iPhone7,1"). Defaults to <none>.

	uploadBitcode : Bool

		For App Store exports, should the package include bitcode? Defaults to YES.

	uploadSymbols : Bool

		For App Store exports, should the package include symbols? Defaults to YES.
```




