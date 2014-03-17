title: ios代码中可以加的一些特定标记的注释
date: 2012-06-18 15:13:28
categories: ios
---

在代码中可以使用一些注释的标记，标识一些事情, xcode ide可以识别出来，用于做一些提醒.
<!-- more -->
如:

    // TODO:  标识要做的事情.     
    // FIXME: 标识需要修复的代码.  
    // ???    表示一些东西不确定.  
    // !!!    表示一些代码要特别注意.  
    // MARK:  和#pragma mark 的作用相同.  
  
  
具体参考
<http://macdevelopertips.com/xcode/xcode-fixme-and-todo.html>       

根据这个文章中所说，好像// XXX: 这种标记在xcode4 中，只有在函数和方法的外面，才会列出在JumpBar上.