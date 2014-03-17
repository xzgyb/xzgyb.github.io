---
layout: post
title: "iOS程序的布局"
date: 2012-06-18 14:36
comments: true
categories: ios
---

  简要介绍下iOS程序的头文件和.m文件的程序结构布局，参考一些文章，和程序.
  
<!-- more -->

# .h文件
1. \#import 头文件.

2. 宏定义, (使用#define XXX), 这里的宏是别的模块要用的，如果不需要别的模块使用，
放到.m中, 宏的命名使用大写单词, 单词间下划线分隔这种形式, 如:
``` objc
#define MAX_VALUE   3
```

3. 常量定义, (使用const), 命名使用k前缀的形式，如:
``` objc
const NSInteger kMaxValue = 3;
const NSString * const kItemCaption = @"Hello";
```

4. 枚举定义, 枚举值名为类型名加成员名，如:
``` objc
typedef enum {  
    UITableViewCellStyleDefault,  
    UITableViewCellStyleValue1,  
} UITableViewCellStyle;  

typedef enum {  
    UITableViewCellAccessoryNone,  // 这里把Type省略掉了，虽然违反上面的规则，但以代码阅读顺畅优先.        
    UITableViewCellAccessoryCheckmark  
} UITableViewCellAccessoryType;  
```

5. 前置声明, 如:
    @class, @protocol等.
    
6. @interface 声明.
  
7. 实例变量, 命名以下划线为前缀.
  
8. 类方法声明.
  
9. 属性声明.
  
10. 方法声明.
  
11. 其他的@interface 或 @protocol声明.

----------------

# .m文件
1. \#import 与.m文件同名的头文件, 如Hello.m, 要#import "Hello.h", 并且这个放在第一行.
  
2. \#import 系统头文件, 按首字母顺序排列.
  
3. \#import 工程内使用的其他头文件, 按首字母顺序排列.
  
4. .m文件所用的宏定义，常量, 枚举等定义，顺序同头文件.
 
5. 辅助函数原型前置声明，可以用注释标识.
  // Helper function prototypes
    
6. @interface 的扩展声明, 用于类的内部私有实现, 如:
``` objc
@interface Hello()
@end
```

7. @implementation段部分.
  
8. @synthesize 声明，使用指定下划线变量名的形式，如
``` objc
@synthesize height = _height;
```

9. 类方法的定义.
``` objc
#pragma mark - Class Methods
```

10. init函数, dealloc函数的定义, 可用#pragma mark标识.
``` objc
#pragma mark - Lifecycle management  

// 先写dealloc函数的定义.
- (void)dealloc {      
}

- (id)init {      
}

- (id)initXXXX {

}
```

11. 对于UIViewController的派生类，写view相关的生命期管理部分.
``` objc
#pragma mark - View lifecycle management
- (void)viewDidLoad {      
}

- (void)viewDidUnLoad {
  
}

- (void)viewWillAppear {
  
}

- (void)viewDidAppear {
  
}

- (void)viewWillDisappear {
  
}

- (void)viewDidDisappear {
  
}
  
- (void)loadView {
  
}
```

12. Overrides部分, 比如覆盖UIViewController的某些方法, mark中显示的标出覆盖的方法所在的类的名称.
```
#pragma mark - UIViewController Overrides
- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation
{      
}
```

13. Delegate方法定义, 比如: 
``` objc
#pragma mark - UITableViewDelegate Methods
    
#pragma mark - UITableViewDataSource Methods
```

14. 控件的动作部分, 比如
``` objc
#pragma mark - User Actions  
```

15. 公共函数定义部分.
``` objc
#pragma mark - Public Methods
```

16. 访问器部分.
``` objc
#pragma mark - Accessors
```

17. 私用方法定义部分
``` objc
#pragma mark - Internal Methods
#pragma mark - Private Methods
```

18. 辅助函数定义部分.
``` objc
#pragma mark - Helper functions
```
   
  