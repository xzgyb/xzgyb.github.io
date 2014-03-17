---
layout: post
title: "Objective C 惯用法收集"
date: 2012-04-19 15:42
comments: true
categories: ios
---

<!-- more -->
1. ### @synthesize firstName = _firstName.
  
    @synthesize合成属性时, 通常使用上面写法。

    原因是在一个类的实现中, 通常应该使用属性来访问实例变量, 即通过self.firstName来访问, 
    因为有时候一个属性的getter或setter方法可能被定制, 如果直接访问实例变量, 会破坏一个
    对象的内部状态, 另外编译器合成的setter或getter方法, 是KVO compliant的, 直接使用
    属性时，会产生正常的KVO通知. 

    而如果使用@synthesize firstName来合成属性时,@interface中没有声明firstName实例变量时，
    则编译器生成一个firstName的实例变量, 这样在代码中, 很容易直接使用firstName, 
    而忘记去用self.firstName来访问.

    另外对于直接访问实例变量 _firstName, 一般都在init, dealloc方法中使用.

    还有个好处就是, 把实例变量声明为_firstName, 在类似如下的方法中.

    -(id)initWithFirstName:(NSString *)firstName.

    如果直接访问实例变量_firstName, 不会冲突，如果实例变量命名为firstName, 这跟参数名firstName冲突, 
    则参数名不得不改成aFirstName或theFirstName类似的形式, 有点别扭.

    坏处就是, 如果这个_firstName不声明成属性, 在类的实现中, 直接用
    这个_firstName时, 代码比较难看.
  
2. ### 调用属性时, 通常用self.firstName这种形式.
   
    优点是简单, 直接.

    缺点是当属性是结构体时, 有点混乱, 比如.
    self.view.frame.orgin = CGPointMake(2, 3);

    这个无法编译, 因为frame实际上是view的属性, 实际上调用的是[[self view] frame], 
    frame方法返回的是一个CGRect的临时变量.
  
3. ### init方法的实现.
   
    init方法的实现, 通常应该避免调用一些可能产生错误的方法或者一些
    运算量大的方法, 另外通常应该直接使用实例变量进行赋值, 而不用属性,
    因为如用属性的话, 有可能别人的继承这个类的子类, 覆盖了这个属性的setter方法,
    从而破坏这个类的init方法的完整性.
    
4. ### 在设计一个简单的数据模型类时, 最好设计成不可变的类, 如下:
``` objc
@interface Person : NSObject
@property (nonatomic, strong, readonly) NSString *name;
@property (nonatomic, assign, readonly) NSInteger age;
@end
```
    原因是这让代码更简单，更清晰，更安全.

5. ### 关于Designated initializer.

    当子类实现了自己的Designated initializer, 也要覆盖基类的Designated initializer.
``` objc
// Person.h
@interface Person : NSObject
@property (nonatomic, strong, readonly) NSString   *name;
@property (nonatomic, assign, readonly) NSUInteger age;

- (id)initWithName:(NSString *)name
                age:(NSUInteger)age;
@end

// Person.m
#import "Person.h"

@implementation Person
@synthesize name = _name;
@synthesize age  = _age;

- (id)initWithName:(NSString *)name
                age:(NSUInteger)age
{
    self = [super init];
    
    if (self) {
        _name = name;  
        _age  = age;
    }
    
    return self;
}   

// Override the superclass's designated initializer.
- (id)init {
    return [self initWithName:@"anonymous" age: 0];
}                 
@end
```

    这么做的原因是, 如果不覆盖基类的Designated initializer, 那么当调用子类从父类继承且没覆盖的
    initXXX方法时, 子类的内部成员状态将变得不一致.
            
    如果在子类的这个覆盖基类的Designated initializer方法中,
    不想给予实现，甚至可以抛出一个异常, 这样将更加安全。
              
6. ### 关于Exception的使用.
      
   使用Exception来表示正常情况下不可能发生的错误, 通常表示一个方法的调用者违背
   的这个方法的前置条件, 或者这个方法的内部状态不一致，通常表示开发者的错误.
   而用户的错误，或者一些外部的错误(比如文件打开失败，网络连接失败), 通常用
   返回值表示或者NSError表示.
    
   原因是, 如果大量的使用异常，会影响性能, 且会打乱程序的执行流.
    
7. ### 关于浮点数的使用.
   
   使用浮点数时, 总是显示的加上f后缀, 如 33.2f, 
   原因是更清晰，同时iOS对于float有优化.
    
8. ### 对于UIView的tag.    
   值从1000开始, 因为使用一些小的tag值，可能会跟UIKit的一些View的内置控件的tag值重复.
   比如定制UIAlertView时，UIAlertView使用的控件tag值好像就包括0, 1等.
    
9. ### 关于@property copy attribute的使用.
   对于immutable class, 如NSString, NSArray等，用copy属性, 因为有可能这个属性被赋值为
   相应的mutalbe subclass, 如NSMutableString, NSMutableArray, 这样这个mutable object
   如果被调用者被改变，属性的immutable语义就不对了，程序内部状态会被破坏.

   对于immutable class, 使用copy，是廉价的，事实上就是使用的retain, 
   而对Mutalbe object赋值后，可能就要进行真的copy了，对性能稍有影响，
   但这也是必须的.
   
10. ### 关于实例变量.
    实例变量放的位置，一个是都放在@interface中，另一个是在.m中作为@property,并用@synthesize
    合成实例变量，不要@interface放一部分，.m中放一部分.
    建议的是都放在.m中。
    另外在iOS5下, 也支持在@implementation下直接定义实例变量的用法.
``` objc    
@implementation {
  int a;
  int b;
}
```

    也支持在extension中定义实例变量.
``` objc @interface ViewController() {
  int a;
  int b;
}
@end
```    
    但不支持在category中定义实例变量.
    
11. ### 关于UITableViewController
    如果在TableView中使用UITextField, UITextView, 并且使用的是UITableViewController,
    那么当用户手指点TableView的最后一个Cell的TextField或TextView时, UITableViewController会做
    些处理，自动调整包含TextField或TextView的cell的位置，以不至于让Keyboard盖住包含
    TextField或TextView的Cell, 如果单纯使用UIViewController和TableView，则需要响应keyboard通知
    写代码调整.