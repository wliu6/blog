---
title: iOS Variable Argument
date: 2016-06-12 20:08:00
tags: iOS
categories: development
copyright: true
---

> iOS的可变参数本质上是C语言特性，也就是说类C语言都可用使用，只需导入头文件`#include <stdarg.h>`。本文将以Objective-C来介绍什么是可变参数，如何使用可变参数，以及可变参数如何传递。

<!-- more -->

---
<br/>

# 什么是可变参数
可变参数只能作为函数的最后一个参数，`...`前`,`隔开的参数就是可变参数，可变参数本质上是一个参数指针，每一次取参后指针地址位移，指向下一个参数的内存地址，知道指向野指针(nil)为止。使用时多个参数以`,`隔开。

下面例子中的format参数便是一个可变参数。
```
+ (instancetype)stringWithFormat:(NSString *)format, ... NS_FORMAT_FUNCTION(1,2);
```
使用示例：
```
[NSString stringWithFormat:@"项目结束时间: %@\n%@\n开奖时间: %@\n%@\n大盘指数来源交易日: %@\n%@\n上证指数: %@\n%@\n深证指数: %@\n%@\n档位认筹人数: %@\n%@\n抽奖规则: %@\n%@\n中奖名额: %@\n%@\n抽奖码个数: %@",[dic ks_objectForKey:@"time" replace:@""],@"",[dic ks_objectForKey:@"lotteryTime" replace:@""],@"",[dic ks_objectForKey:@"date" replace:@""],@"",[dic ks_objectForKey:@"shse" replace:@""],@"",[dic ks_objectForKey:@"size" replace:@""],@"",[dic ks_objectForKey:@"num" replace:@""],@"",[dic ks_objectForKey:@"rule" replace:@""],@"",[dic ks_objectForKey:@"winnerNum" replace:@""],@"",[dic ks_objectForKey:@"lotteryCodeNum" replace:@""]];
```
<br/>

# 可变参数如何解析多个参数
可变参数实际上是利用几个C语言的宏来实现参数解析，
```
#ifndef _VA_LIST
typedef __builtin_va_list va_list;
#define _VA_LIST
#endif
#define va_start(ap, param) __builtin_va_start(ap, param)
#define va_end(ap)          __builtin_va_end(ap)
#define va_arg(ap, type)    __builtin_va_arg(ap, type)
```
1. `va_list` —— 指向可变参数的指针；
2. `va_start(ap, param)` —— 开始读取可变参数列表；
3. `va_start(ap, param)` —— 结束读取可变参数列表；
4. `va_arg(ap, type)` —— 读取参数(每读取一次，参数列表指针将指向下一个参数的内存地址)；

<br/>

# 实现可变参数函数
## 参数为Objective-C对象
```
- (void)writeWithFormat:(NSObject *)string, ... NS_REQUIRES_NIL_TERMINATION;

- (void)writeWithFormat:(NSObject *)string, ... {
  // 定义一个指向个数可变的参数列表指针；
  va_list args; 
  va_start(args, string);
  if (string) {
    NSLog(@"Do something with First: %@", string);
    NSObject *other;
    // 依次取得除第一个参数以外的参数
    while ((other = va_arg(args, NSObject *))) {
      NSLog(@"Do something with other: %@", other);
    }
  }
  // 清空参数列表，并置参数指针args无效。
  va_end(args); 
}

```
## 参数为scalar或struct对象
实现参数为常量或者结构体的可变参数函数，实现方法和参数为Objective-C一样，此处不赘述。

## 任意参数类型
因为Variable Argument是C语言特性，所以参数可以是C指针，可以利用C指针指向内存地址的特性来实现任意参数类型的可变参数函数。
```
- (void)method:(void *)arguments, ... NS_REQUIRES_NIL_TERMINATION;

- (void)method:(void *)arguments, ... {
  va_list args; 
  va_start(args, arguments);
  if (arguments) {
    void *other = ;
    while ((other = va_arg(args, void *))) {
      // use arguments to do sth
    }
  }
  va_end(args); 
}

```
使用场景：[优化performSelector函数]()


但是获取到的C指针如何处理呢？
可以使用[@encode()]()获取编码类型，然后__bridge一下^_^。

## 可变参数传参
由于可变参数，在运行时才可以确定参数列表。传参时会发现，如果将可变参数当做普通指针参数传递的话，接收参数的函数只能拿到可变参数指针指向的第一个参数。那么如何进行可变参数传参呢？

可变参数传参需要一个迂回的操作，前文有提到va_list是可变参数参数指针，可以传参的时候顺道把指针丢过去即可。
```
- (void)xxxParams:(void *)params, ...
{
  va_list args; 
  va_start(args, params);
  [self testParams:params args:args];
  va_end(args);
}

- (void)testParams:(void *)params args:(va_list)args {
  void *tmp;
  while ((tmp = va_arg(args, void *))) {
    // to do sth
  }
}
```

