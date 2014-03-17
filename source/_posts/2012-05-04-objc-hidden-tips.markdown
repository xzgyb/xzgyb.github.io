---
layout: post
title: "ObjC 一些不常用的函数及设置收集"
date: 2012-05-04 11:50
comments: true
categories: ios
---

<!-- more -->
1. ### 显示系统中的所有AutoReleasePool信息.  

   * [NSAutoreleasePool showPools];
   * extern void _objc_autoreleasePoolPrint();  
     objc_autoreleasePoolPrint();     
     
2. ### 用于查看ObjC运行时的相关环境变量.
   
   以下环境变量在xcode的设置环境变量的对话框中设置, 信息输出在xcode的控制台输出窗口中.
   * OBJC_HELP: 输出OBJC的选项设置及相关作用.
   * OBJC_PRINT_OPTIONS: 输出OBJC已设置的选项.
   * OBJC_PRINT_IMAGES: 输出已装载的image信息.
   * OBJC_PRINT_LOAD_METHODS: 输出已装载的方法信息.
   * OBJC_PRINT_EXCEPTION_THROW: 输出异常发生时的堆栈.
   
3. ### 跟踪objc 发送的消息.

   extern void instrumentObjcMessageSends(BOOL enable);
   instrumentObjcMessageSends(YES);
   
   这个函数可以在程序中动态的调用.
   这样在会生成一个文件，/tmp/msgSends-<pid>，其中包含了objc message的名字.