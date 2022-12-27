---
layout:     post
title:      "AppTracking审核问题建议"
subtitle:   " \"给自己一点思考的时间\""
date:       2021-04-14 17:36:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - apple审核 IDFA AppTracking
---

> 采得百花成蜜后，为谁辛苦为谁甜？


#### 开头
写这篇文章是记录一下IDFA被拒绝一系列沟通，通过审核的纪实。

来看看Apple审核圆的Rejected原因：
app-A
```text
Guideline 5.1.2 - Legal - Privacy - Data Use and Sharing

We found in our review that your app collects user and device information to create a unique identifier for the user's device. Apps that fingerprint the user's device in this way are in violation of the Apple Developer Program License Agreement and are not appropriate for the App Store.

Specifically, your app uses algorithmically converted device and usage data to create a unique identifier in order to track the user. The device information collected by your app may include some of the following: 
• SimulateIDFA
• OpenUDID

Per section 3.3.9 of the Apple Developer Program License Agreement, neither you nor your app can use any permanent, device-based identifier, or any data derived therefrom, for purposes of uniquely identifying a device.

Next Steps

To resolve this issue, remove any functionality from your app that uses algorithmically converted device and usage data to create a unique identifier for the user's device. You should also remove any related code or implemented SDKs that support this functionality. 

Please note that attempting to hide or obfuscate code designed to fingerprint the user's device, or otherwise evade the review process, may result in the termination of your Apple Developer Program account. Review the Terms & Conditions of the Apple Developer Program to learn more about our policies regarding termination.

Resources

Learn more about our requirements for apps that track users.

```
app-B
```text
Guideline 5.1.1 - Legal - Privacy - Data Collection and Storage
 
 
 We noticed that your app urges or manipulates users to allow the app to track them. Specifically, your app displays its own custom permission request to the user before presenting the iOS permission request. 
 
 Permission requests on iOS give users control of their personal information. It is important to respect their decision about how their data is used. If they decide to not grant permission to your app, you should not ask them to change their mind or force them to reject the request multiple times. 
 
 Next Steps
 
 To resolve this issue, please remove any custom permission requests in your app that appear before the iOS permission request. 
 
 You can provide users with additional information about why you&rsquo;re requesting access to their data before the iOS permission request appears, as long as you don&rsquo;t include your own custom permission request.
 
 Resources 
 
 - Learn more about designing appropriate permission requests.
 - Learn more about our policies for data collection and storage in App Store Review Guideline 5.1.1.
```

##### 分析-app-A

app-A先提交审核，审核被拒绝原因主要是用到openUDID和IDFA
针对此问题我先查找这两块的代码使用：
1. openUDID是我们自身用来标记设备id使用，基于用户数量比较少，直接用uuid替了，也就忽略的uuid碰撞问题（亿级用户另寻他法）。

```text
grep -r openudid .
看看三方库是否有使用，无即可忽略了。

```
2. IDFA 
```text
代码目录命令查一下使用idfa库以及代码
grep -r advertisingIdentifier .

Binary file ./Pods/UMCommon/UMCommon_7.2.5/UMCommon.framework/UMCommon matches
Binary file ./Pods/UMCommon/UMCommon_7.2.5/UMCommon.framework/Versions/A/UMCommon matches
Binary file ./Pods/UMCommon/UMCommon_7.2.5/UMCommon.framework/Versions/Current/UMCommon matches
Binary file ./Pods/LBLelinkKit/LBLelinkKit.framework/LBLelinkKit matches
Binary file ./Pods/Weibo_SDK/libWeiboSDK/libWeiboSDK.a matches
Binary file ./Pods/GTSDK/GTSDK.framework/GTSDK matches

```

看到这几个库果断去看了umeng的合规化文档：
```text
https://developer.umeng.com/docs/119267/detail/185919
合规三步走

1、您需要确保App有《隐私政策》，并且在用户首次启动App时就弹出《隐私政策》取得用户同意。2、您务必告知用户您选择友盟+SDK服务，请在《隐私政策》中增加如下参考条款：“我们的产品集成友盟+SDK，友盟+SDK需要收集您的设备Mac地址、唯一设备识别码（IMEI/android ID/IDFA/OPENUDID/GUID、SIM 卡 IMSI 信息）以提供统计分析服务，并通过地理位置校准报表数据准确性，提供基础反作弊能力。”3、您务必确保用户同意《隐私政策》之后，再初始化友盟+SDK。具体初始化步骤详见下文。
```
根据这个合规指南，做了一个隐私使用弹框，根据用户是否同意决定代码的三方库的初始化，并在提交审核后在备注中加入了一下审核备注：
```text
您好，我们根据上次的Rejected进行了整改，整改如下：
1. 我们自身软件使用的openUDID已经移除。
2. 我们依赖的第三方统计分析库，投屏播放库可能使用了openUDID/IDFA，对于这块我们做了如下事项：
- 在用户首次安装app后，启动时，我们会弹窗询问用户，是否同意我们调用IDFA
- 在用户明确的“同意”后，我们的代码方可初始化友盟/乐播投屏库
- 若用户“拒绝”使用，我们代码将不会初始化友盟/乐播投屏库
- 在之后的启动中，我们会根据用户的授权决定是否初始化友盟/乐播投屏库。
具体如附件图1
3. 适配了iOS14关于info.plist中，数据的使用权限交给用户决定。
‘Privacy - Tracking Usage Description’-‘我们的产品集成友盟+SDK/乐播，友盟+SDK/乐播需要收集您的唯一设备识别码（IDFA/OPENUDID）以提供统计分析服务，并通过地理位置校准报表数据准确性，提供基础反作弊能力。’
4. 我们在appstoreconnect-***-App隐私中，也标示出了用于跟踪数据（标识符等）使用途径
5. 详细的隐私条款用户可以查看‘***隐私协议/用户协议’
```
binggo 审核通过 🎉

##### 分析-app-B
因为是同一个团队写的代码，底层框架是固定稳定的，故按照A的审核方案做了，结果-rejected-rejected（看上面B被rejected原因）,大概的意思是我用自定义的AppTracking诱导用户同意使用openUDID/IDFA等（果真，每个人的审核标准是不一样的），那好吧。看这几个库都不会主动调用AppTracking弹框请求，故自己手动写一个空的调用下，反正他们有无IDFA都能正常使用。
```swift
if #available(iOS 14, *) {
            ATTrackingManager.requestTrackingAuthorization { [weak self](status) in
                if status == .authorized {
                   ...
                } else {
                   ...
                }
            }
        }

```
这个代码app启动的时候调用一下，主要是掉起AppTracking弹框权限请求，以达到苹果审核想要的结果。
这样初次安装app启动后回有IDFA权限的申请，可以忽略iOS14前的设备，审核貌似只用最新版本系统审核，方法也是iOS14才有的。


binggo 审核通过 🎉

##### 反思
这么多库为何都是用IDFA呢？为何不能提供一个无IDFA的呢？
然后我去一家一家找客服谈，结果是大家都能提供无IDFA版本。
故：另外一种方案就是剔除IDFA请求，所有三方库都不使用IDFA，这个需要你一家一家问，申请无IDFA版本的SDK。
想一想：既然iOS14 Apple已经封杀跟踪IDFA，99%的用户也不会同意app跟踪用户，那三方库标记设备用户可以完全放弃IDFA方案，或许Apple的不允许跟踪用户可以编写到信息法中，当然斟酌细化细化。这个信息爆炸的时代，我们都是被没有任何隐私的查看分析，杀熟。就拿蛋壳/自如这些长租公寓，你有没有发现当你的数据被这些垄断企业掌握后，你再无谈判能力，如果大家的数据都被这些垄断企业收集分析，你的口袋或许一干二净，回头还得欠一屁股债。
支持苹果，支持不被跟踪。



