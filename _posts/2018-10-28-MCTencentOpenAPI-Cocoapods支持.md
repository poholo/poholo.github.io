---
layout:     post
title:      "MCTencentOpenAPI Cocoapods支持"
subtitle:   " \"MCTencentOpenAPI Cocoapods支持\""
date:       2018-10-28 18:00:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - MCTencentOpenAPI
    - TencentOpenApiSDK
    - TencentOpenAPI
---

# 前言
自从开源MCShare模块，以及维护了LDSDKManager之后，发现第三方库的管理比较混乱，看到很多前辈举起改革的大旗，若干年后旗帜在那里，人却不见了，作为后生，不得已重新扛起这面旗帜，MCTenecentOpenAPI3.3.3版本fork于[TencentOpenApiSDK](https://github.com/candyan/TencentOpenApiSDK), 3.3.3 版本映射腾讯官方3.3.3版。
欢迎来提pr[MCTencentOpenAPI](https://github.com/poholo/MCTencentOpenAPI)

# MCTencentOpenAPI
MCTencentOpenAPI是对TencentOpenAPI的映射，使用pod依赖管理，便于开发者快速集成TencentOpenAPI的QQ登录、分享等功能。

# 集成方式
```
pod 'MCTencentOpenAPI'

老版本请使用(3.0)之前
使用 Cocoapods-depend 插件
pod depend add TencentOpenApiSDK
或者在 Podfile 文件下添加
pod 'TencentOpenApiSDK'
```
# notes
建立仓库的本意在于LDSDKManager集成三方库比较陈旧，当想换新的库时，发现非常难，而第三方库的管理也比较佛系，基本很难追踪历史，故打算长更新此仓库，此仓库的老版本走 TencentOpenApiSDK，3.3.3版本后会逐步转移到这个仓库。
# doc
文档在目录doc下

SDKDemo

# Link
[文档地址-官方](http://wiki.open.qq.com/index.php?title=iOS_API调用说明&oldid=46716)

[sdk地址-官方](http://wiki.open.qq.com/wiki/mobile/SDK下载)

