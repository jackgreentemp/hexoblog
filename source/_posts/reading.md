---
title: reading
date: 2016-08-02 11:42:34
tags: ReactNative
categories: ReactNative知识点
---
# 如何将ReactNative开源项目[reading](http://richardcao.me/2016/07/05/Talk-About-Reading/#more)工程改造成另外一个工程

## IOS

1. 创建一个工程helloword

2. 把reading的app文件夹复制到helloworld文件夹下

3. 修改helloworld的index.ios.js

4. 使用xcode打开工程，添加缺失的library
![](http://jackgreentemp.github.io/images/1.png)

5. 添加helloworld工程的header search path
TARGETS-helloworld-Build Settings-Hearder search Paths
$(inherited) 
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include $(SRCROOT)/../node_modules/react-native/React/** 
$(SRCROOT)/../node_modules/react-native-wechat/ios/** $(SRCROOT)/../node_modules/react-native-code-push/** 
$(SRCROOT)/../node_modules/react-native-device-info/RNDeviceInfo

6. 添加RCTWeChat.xcodeproj的Hearder search Paths，否则会编译失败
$(inherited) 
$(SRCROOT)/../../react-native/React/** 
$(SRCROOT)/../../react-native/Libraries/Image/**

7. 添加CodePush.codeproj的Hearder search Paths
$(inherited) 
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include 
$(SRCROOT)/../../react-native/React/** 
$(SRCROOT)/../Examples/CodePushDemoApp/node_modules/react-native/React/**

8. 添加其它需要的配置，参考wechat模块readme

### 参考：
https://github.com/weflex/react-native-wechat