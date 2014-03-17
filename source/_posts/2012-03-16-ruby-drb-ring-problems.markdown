---
layout: post
title: "关于Ruby rinda中的RingFinger.primary导致程序挂起的问题"
date: 2012-03-16 16:19
comments: true
categories: ruby
---
<!-- more -->
最近在调查ruby的分布式编程，正在看&lt;&lt;DISTRIBUTED PROGRAMMING
WITH RUBY&gt;&gt;, 看到65页，其中有段程序，使用定制端口号，启动RingServer。

``` ruby ring_server.rb
require 'rinda/ring'
require 'rinda/tuplespace'

DRb.start_service
Rinda::RingServer.new(Rinda::TupleSpace.new, 9000)

DRb.thread.join 
```
``` ruby finger.rb
require 'rinda/ring'

DRb.start_service
ring_server = Rinda::RingFinger.primary
```

运行之

```
  ruby ring_server.rb
  ruby finger.rb
```

按照书上的描述，`Rinda::RingFinger.primary`应该抛出`RuntimeError: RingNotFound`,
但是实际运行时，Rinda::RingFinger.primary的调用导致程序挂起，很是奇怪。

没办法，拿起rdebug，调试一把，最终找到问题在`lib\ruby\1.9.1\rinda\ring.rb`, 其中有段代码
``` ruby ring.rb 198行
def lookup_ring_any(timeout=5)
  queue = Queue.new

  th = Thread.new do
      self.lookup_ring(timeout) do |ts|
        queue.push(ts)
      end
      
      queue.push(nil)
      while it = queue.pop
        @rings.push(it)
	  end
  end

  @primary = queue.pop
  raise('RingNotFound') if @primary.nil?
  @primary
end
```
这个`lookup_ring_any`会被`Rinda::RingFinger.primary`调用, 而其中的代码
```ruby
  	queue.push(nil)
	  while it = queue.pop
	    @rings.push(it)
    end
```
和下面的代码
```ruby
  @primary = queue.pop
```
属于在2个不同的线程内，会产生竞争，可能queue中push的元素被
```ruby
while it = queue.pop
    @rings.push(it)
end
```
这段代码取走，都放在`@rings`里,而外面的`@primary = queue.pop`，
这个将一直挂起，收不到数据。
知道了问题，改成如下，就好了.

```ruby
def lookup_ring_any(timeout=5)
  queue = Queue.new

  th = Thread.new do
  	self.lookup_ring(timeout) do |ts|
  	  queue.push(ts)
  	end
  	queue.push(nil)
  end

  @primary = queue.pop
  raise('RingNotFound') if @primary.nil?

  while not queue.empty?
    @rings.push(queue.pop)
  end
  
  @primary
end
```

另外说下我的ruby环境为ruby1.9.2p0, 换了ruby 1.8, 没有问题，
有时间在研究研究，可能跟2个版本的线程模型有关了。
