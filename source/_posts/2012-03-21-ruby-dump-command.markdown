---
layout: post
title: "Ruby 1.9 的dump开关"
date: 2012-03-21 09:33
comments: true
categories: ruby
---
Ruby1.9 加入一个隐藏的dump命令行选项，用于研究ruby所用，官方并没文档说明，如下:
<!-- more -->

1. ### `ruby --dump=yydebug test.rb`
  用于当ruby分析test.rb语法时, Bison parser输出的信息。

2. ### `ruby --dump=syntax test.rb`
  用于检查test.rb的语法是否有效。
  
3. ### `ruby --dump=parsetree test.rb`
  用于输出test.rb的解析后的语法树。
  
4. ### `ruby --dump=parsetree_with_comment test.rb`
  用于输出test.rb的解析后的带有注释的语法树。
  
5. ### `ruby --dump=insns test.rb`
   用于输出test.rb的指令码序列。
   
----------   
以上这些，在解释一些有趣的语法现象时，会用到。