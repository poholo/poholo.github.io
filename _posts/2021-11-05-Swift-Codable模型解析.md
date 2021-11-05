---
layout:     post
title:      "Swift模型解析"
subtitle:   " \"分析问题需心静\""
date:       2021-11-05 15:39:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - swift codable 模型
---

> 相由心生


### 题记
此篇是记录swift使用codable模型解析, 遇到的问题.

#### 开始表演

```json
json 结构
{
    "foods": [
        {
            "id": "100261764",
            "nm": "轻芝士蛋糕",
            "pic": "http://test.com/2e/aaa.jpg"
        },
        {
           
            "id": "100445983",
            "nm": "《Tinrry+》戚风蛋糕（6寸配方）（基础、超详细教程）"
        }
    ],
    "id": "40072",
    "nm": "下午茶"
}
```

```swift
模型结构体
struct TopicDto: Codable, Identifiable {
    let id: String
    let nm: String
    let foods: [FoodDto]
}


struct FoodDto: Codable, Identifiable {
    let id: String
    let pic: String
    let nm: String
}

```

```swift
解析

let jsonData = “”.data(using: .utf8)

let decoder = JSONDecoder()
let topic = try? decoder.decode(TopicDto.self, from: jsonData)
if topic == nil {
    print("模型解析失败")
}

```

##### 错误的路 
由于解析一个[TopicDto]数组结构, 没有关注到topic解析失败, 误以为Topic节点的foods节点均为空,然后走上了
```swift
struct TopicDto: Codable, Identifiable {
    let id: String
    let nm: String
    let foods: [FoodDto]

    enum CodginKeys: String, CodingKey { case id, nm, foodarr }
    enum FoodKeys: String, CodginKey { case foods }

    init(from decoder: Decoder) throws {
        let values = try decoder.container(keyedBy: CodingKeys.self)
        id = try values.decode(String.self, forKey: .id)
        nm = try values.decode(String.self, forKey: .nm)

        let foodDicts = try values.nestedContainer(keyedBy: FoodKeys.self, forKey: .foodarr)

    }
}
```
这样一错再错.

#### 正确的发现问题
发现解析出来的topic为nil, 我方才若有所悟, 开始检查我的模型结构体和json数据的对比(此处是简化的结构体), 猜测是不是又是“有与无”的哲学问题导致的.

当我试着诸事掉 FoodDto 的 pic 成员时, 能正确解析出topic, 同事topic模型下的foods有两个food
```swift
struct FoodDto: Codable, Identifiable {
    let id: String
    let pic: String?
    let nm: String
}
```
这样修改完FoodDto, 问题解决. 


### 感谢
[Encoding and Decoding Custom Types](https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types)

[Using JSON with Custom Types](https://developer.apple.com/documentation/foundation/archives_and_serialization/using_json_with_custom_types)

[UsingJSONWithCustomTypes-playground](https://docs-assets.developer.apple.com/published/83799f6e15/UsingJSONWithCustomTypes.zip)