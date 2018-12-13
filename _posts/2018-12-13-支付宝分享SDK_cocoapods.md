---
layout:     post
title:      "APOpenSdk支付宝分享Cocoapods"
subtitle:   " \"支付宝官方库映射\""
date:       2018-12-13 18:55:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - Alipay
    - 支付宝
    - APOpenSdk
    - SDK
---

> Ha, 在写支付宝授权的时候偶然发现这个库，之前发AppleStore版本时，老会因为支付宝的库被拒绝，支付宝分享有可能是规避此类问题（支付宝支付隐藏功能）一个点。

# APOpenSdk

iOS支付宝分享 [APOpenSdk](https://github.com/poholo/APOpenSdk)

APOpenSdk是对支付宝分享的映射，使用pod依赖管理，便于开发者快速集成支付宝的分享功能。

# 集成方式
```
pod 'APOpenSdk'

```
# notes
建立仓库的本意在于LDSDKManager集成三方库比较陈旧，三方库管理比较凌乱，故再官方的基础上映射维护此库，欢迎pr。

# Link
[文档地址-官方](https://docs.open.alipay.com/215/105276/)

[sdk地址-官方](https://docs.open.alipay.com/215/105277/)

# updates
```xml

版本号：v1.1.0
1. 新增三合一分享（好友、首页、圈子）
1. 新增支持生活圈分享。

版本号：v1.0.1
1. 新增支持bitcode的SDK版本。

```
