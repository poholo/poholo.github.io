---
layout:     post
title:      "SDVersion->MCVersion"
subtitle:   " \"iOS设备版本\""
date:       2020-11-24 11:14:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - 罗列model的iOS设备...
---

> 前人扛着大旗，突然有一天迷失了，后人再扛起大旗...

## SDVersion
SDversion是一款很好的设备版本开源库，作者好几年不更新了，多次联系无果，另外我有几个库是依赖SDVersion，在pod trunk时又无法指定pod的地址，故再维护两三年的iOS models后，用MCVersion发布接下来的iOS models，前辈见谅。

## 集成方式
```ruby
        pod 'SDVersion', git:'https://github.com/poholo/SDVersion.git'
        or 
        pod 'MCVersion'
```

```objective-c
      // Check for device model
      if ([SDVersion deviceVersion] == iPhone7)
           NSLog(@"You got the iPhone 7. Sweet 🍭!");
      else if ([SDVersion deviceVersion] == iPhone6SPlus)
           NSLog(@"iPhone 6S Plus? Bigger is better!");
      else if ([SDVersion deviceVersion] == iPadAir2)
      	   NSLog(@"You own an iPad Air 2 🌀!");

      // Check for device screen size
      if ([SDVersion deviceSize] == Screen4Dot7inch)
           NSLog(@"Your screen is 4.7 inches");

      // Check if screen is in zoom mode
      if ([SDVersion isZoomed])
      	   NSLog(@"Your device is in Zoom Mode 🔎");

      // Get device name
      NSLog(@"%@", [SDVersion deviceNameString]);
      /* e.g: Outputs 'iPhone 7 Plus' */

      // Check for iOS Version
      if ([SDVersion versionGreaterThanOrEqualTo:@"10"])
           NSLog(@"You are running iOS 10 or above!");
```

### 设备列表
#### Targetable models
```
	iPhone4               
	iPhone4S              
	iPhone5               
	iPhone5C              
	iPhone5S              
	iPhone6               
	iPhone6Plus           
	iPhone6S              
	iPhone6SPlus          
	iPhone7               
	iPhone7Plus           
	iPhone8               
	iPhone8Plus           
	iPhoneX               
	iPhoneSE              
	iPhoneXS              
	iPhoneXR              
	iPhoneXSMax
	iPhone11
	iPhone11Pro
	iPhone11ProMax
	iPhoneSE2
	iPhone12      
	iPhone12Pro   
	iPhone12ProMax
	iPhone12Mini  

	iPad1                 
	iPad2                 
	iPadMini              
	iPad3                 
	iPad4                 
	iPadAir               
	iPadMini2             
	iPadAir2              
	iPadMini3             
	iPadMini4             
	iPadPro12Dot9Inch     
	iPadPro9Dot7Inch      
	iPad5                     
	iPadPro12Dot9Inch2Gen 
	iPadPro10Dot5Inch     
	iPadPro11Inch         
	iPadAir3
	iPad6
	iPadPro12Dot9Inch4Gen
	iPadPro11Inch2Gen 
	iPad8   
	iPadAir4
	              
	iPodTouch1Gen         
	iPodTouch2Gen         
	iPodTouch3Gen         
	iPodTouch4Gen         
	iPodTouch5Gen         
	iPodTouch6Gen
	iPodTouch6Gen   
	iPodTouch7Gen   
```
#### Targetable screen sizes
```
    Screen3Dot5inch
    Screen4inch
    Screen4Dot7inch
    Screen5Dot5inch
    Screen5Dot8inch
    Screen6Dot1inch
    Screen6Dot5inch
```

#### Targetable models
```
	Apple Watch 38mm
	Apple Watch 42mm
	Apple Watch 38mm Series 1
	Apple Watch 42mm Series 1
	Apple Watch 38mm Series 2
	Apple Watch 42mm Series 2
	Apple Watch 38mm Series 3
	Apple Watch 42mm Series 3
	Apple Watch 40mm Series 4
	Apple Watch 44mm Series 4
	Apple Watch 40mm Series 5
	Apple Watch 44mm Series 5
	Apple Watch 40mm Series 6
	Apple Watch 44mm Series 6
	Simulator
```

#### Targetable screen sizes
```
	Screen38mm
	Screen40mm
	Screen42mm
	Screen44mm
```









