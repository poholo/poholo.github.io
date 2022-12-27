---
layout:     post
title:      "SwiftUI Tips"
subtitle:   " \"真金不怕火炼\""
date:       2021-12-17 15:39:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - swiftUI
---

> 路漫漫其修远兮, 吾将上下而求索

## 题记
本文本来不想发表的,都是一些基础swiftUI细节, 但在学习swiftUI的过程中,搜索相关问题,总是被付费问题恶心到, 付费没问题,关键是你的文章要达到一定的标准, 街头卖唱的本事, 就不要用这种付费伎俩来收割本身一些刚入门的开发了. 以下是我在swiftUI学习过程中遇到的一些基础问题, 很多都是来来源于免费搜索答案, 希望也能帮助到你.

## 遇到的问题

### 1. Image图像拉伸
```
Image(“xxxx")
            .resizable()
            .aspectRatio(contentMode: .fit)
```


### 2. List 不期望有箭头,一行多个Item时,使用ScrollowView包一下
```
ScrollView(.horizontal, showsIndicators: false) {
                HStack {
                    ForEach(topicDto.filterFoods) {
                        HomeFoodView(food: $0).frame(width: 100, height: nil, alignment: .leading)
                    }
                }
            }

```


### 3. List 分割线隐藏

```
iOS15前

List (0..<50) { index in
      Text("\(index)")
}
.listStyle(SidebarListStyle())

iOS15
List (0..<50) { index in
      Text("\(index)")
          .listRowSeparator(.hidden)
}

```
[注1: 来源](https://www.jianshu.com/p/c78a8f07e2b5)

### 4. Extra argument in call

```
Group包装，一个Group也最多可添加10个控件。
```

### 5. Text组件在容器中对齐 左对齐 右对齐 分散对齐
```

HStack {
    Text("left")
    Spacer()
}

HStack {
    Spacer()
    Text("right")
}

HStack {
    Text("LLLL")
    Spacer()
    Text("RRRR")

}
```

## 后记
本文是一个记述型文档,陆续会把遇到的问题更新上来.

## 文献 文档
[注1: https://www.jianshu.com/p/c78a8f07e2b5](https://www.jianshu.com/p/c78a8f07e2b5)