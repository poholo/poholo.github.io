---
layout:     post
title:      "AlipaySDK Cocoapods支持"
subtitle:   " \"第三方库支付宝集成\""
date:       2018-10-29 16:41:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - MCAlipaySDK
    - AlipaySDK
    - SDK
---

> biu, 阿里在库的管理上相对比较适合开发者，写完此项目后发现他们有自己的pod私有库管理，使用 pod 'AlipaySDK-iOS'即可.


# MCAlipaySDK
MCAlipaySDK是对AlipaySDK的映射，使用pod依赖管理，便于开发者快速集成AlipaySDK的支付授权等功能。

# 集成方式
```
pod 'MCAlipaySDK'

老版本请使用(3.0)之前
使用 Cocoapods-depend 插件
pod depend add AlipaySDK-2.0
或者在 Podfile 文件下添加
pod 'AlipaySDK-2.0'
```
# notes
建立仓库的本意在于LDSDKManager集成三方库比较陈旧，当想换新的库时，发现非常难，而第三方库的管理也比较佛系，基本很难追踪历史，故打算长更新此仓库，此仓库的老版本走 [candyan/AlipaySDK](https://github.com/candyan/AlipaySDK)，15.5.7版本后会逐步转移到这个仓库。

# Link
[文档地址-官方](https://docs.open.alipay.com/204/105295/)

[sdk地址-官方](https://docs.open.alipay.com/54/104509/)

enjoy time!


