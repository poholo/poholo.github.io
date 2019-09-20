---
layout:     post
title:      "Apple这个老父亲"
subtitle:   " \"在擦屁股的路上越跑越远\""
date:       2019-09-19 10:57:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - iOS9
---

> 我在扎起篱笆的时候，同时将自己圈养了

# 发现Apple自己的一个bug
苹果伴随我的开发生涯有7年之久，作为整个生态家长Apple就像一位老父亲，一遍不停的约束孩儿们，一遍又望子成龙，作为开发者们，在这个生态中体验如鱼得水或水土不服，此小记也是来吐槽一下。

xode10左右的版本 iOS12以后，开始莫名的检查一些UI线程问题，给开发者造成很大的不变，比如我在异步线程看一下
```objc
[UIApplication applicationState]
```
你现在就不得不切换线程来访问，或者做个婉转的调用。
我觉得一样技术方案的设定一定基于各方面考虑，我不太清楚Apple自家的工程师现在吃这坨线程检查的x是什么感受，特献上一个线程检查的异常
```text
设备: xs xr
发生条件：PhotoKit调用
异常信息：https://stackoverflow.com/questions/52979259/cmotionmanager-crash-on-instantiation
```


![白云朵朵](/img/post/19-09-20.jpg)
