---
title: 基础框架-Maven模块化
date: 2018-01-20 15:08:32
categories: Datadriver
tags:
     - black magic
     - fucking trick
---
这个是maven分模块的工程。
<!-- more -->
## IDE设置
1. git clone 代码
2. `mvn install`
3. 在工作目录中使用`mvn eclipse:eclipse`[Eclipse]或`mvn idea:idea`[Intellij IDEA]初始化工程
4. 在IDE中导入已有工程

## 打包与运行
* 打包jar
命令：`mvn package -P dev`[开发环境配置]，`mvn package -P online`[生产环境配置]<br>
其中，开发环境dev的配置位于`src/main/resources/dev`中，生产环境online的配置位于`src/main/resources/online`中<br>
默认为dev开发环境的配置

* 运行
其中`datadriver-web`中是war包，该包中包含有其他模块的jar在lib目录中
