---
layout:     post
title:      "objc&swift5混编iOS9 load崩溃"
subtitle:   " \"往往我们在骑驴找驴\""
date:       2019-09-19 10:57:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - iOS9
---

> ...

# 事由
项目没多少事，自从swift推出已有5年之久，由于以前项目的慎重，很少写swift代码，故在这儿小玩一下，无奈上线后有个崩溃一只寻不到，原因有二，一是无iOS9的硬件设备，二是分析问题不够透彻，一昧陷入网上前辈踏过的路。跟客户沟通良久方解决此问题，故记叙一下。

# Crash信息
由于是load崩溃，所有的收集信息的库基本是摆设，故无法通过umeng后台查找，你只能再xcode->Organizer->Crashs来看。

```text
Dyld Message: Library not loaded: @rpath/libswiftCore.dylib + 0

```

# 分析
开始查这个问题有很多解决办法，而对于我的项目来说都无济于事，这儿直接略过... 耗费一段时间 ...
其实分析崩溃信息，就是无法加载swift库，我来判断swift5支持到iOS几
```text
It is IOS 8.
See here
https://stackoverflow.com/questions/56766387/what-is-the-minimum-ios-deployment-target-for-swift-5
https://forums.developer.apple.com/message/381852#381852
```
所以这个方向貌似是偏航了

恍然间看到很多lib库是可以自己添加的，为何我不能libSwiftCore.dylib添加进来了，故添加之，由于用的Xcode11GM故可以直接搜到
若非xcode11，我也无法验证是否有此libSwfitCore.tbd, 看配置项有两个，一个是libSwfitCore.tbd库，一个是swift的searchpaths
```text
libSwfitCore.tbd 库地址在xcode11以下 /Applications/Xcode11.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk/usr/lib/swift/libswiftCore.tbd 
"$(SDKROOT)/usr/lib/swift", //包含了swift库，包括libSwiftCore.dylib
```
打包发给客户, 客户的回答是YES.


![夜天安门](/img/post/19-09-19.jpg)
