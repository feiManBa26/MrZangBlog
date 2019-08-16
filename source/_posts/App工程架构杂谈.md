---
title: App工程架构杂谈
date: 2019-08-16 11:46:01
tags: 
- Android
- 架构
categories:
- Android
- 架构
description: app 程序架构 听我 胡编乱扯 开车。。。
---
这是一个准备发车的开头 准备好了吗？

### # AndroidStudio App 程序目录
- /app/ `主工程`
- /app/build/ `编译产出目录`
- /app/src/ `代码目录`
- /gradle/ `gradle插件配置目录`
- gradlew `gradle 命令集合`
- gradle.bat `gradle`
- build.gradle `工程配置文件`
- string.gradle `module 生命文件`
- local.properties `本地 sdk ndk 地址配置目录`
- /.idea/* 
- /.gradle/*

![](App工程架构杂谈\01.png)

### # 插件 工程目录

- app `主工程`
- module `类库`
- /module/build/ `编译产出目录`
- /module/src/ `代码目录`

![](App工程架构杂谈\02.png)

### # 组件化 工程目录

===============未完成 待续=========================