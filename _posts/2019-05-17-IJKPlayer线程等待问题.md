---
layout:     post
title:      "IJKPlayer线程等待问题"
subtitle:   " \"MCIJKPlayer 0.0.3\""
date:       2019-05-17 16:08:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - IJKPlayer
    - 播放器
---

> 路漫漫其修远兮，吾将上下而求索。

# MCIJKPlayer
ijkplayer目前维护感觉比较慢，估计开源方已倦5-7年的无休止的更新迭代。MCIJKPlayer是解决xcode 9.0后，系统对于UIKit某些方法必须主线程检查，造成的线程等待问题。

如果你的项目使用的IJKPlayer, 你也遇到线程卡顿问题，那请使用MCIJKPlayer, 集成方法

```text
pod 'MCIJKPlayer'
```

项目源码 [poholo:IJKPlayer](https://github.com/poholo/ijkplayer.git)




