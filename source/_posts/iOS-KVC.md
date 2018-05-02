---
title: 大话KVC
date: 2016-05-30 11:23:06
tags:
categories:
copyright: true
---

> 本文意在解释KVC原理，使用KVC并不会调用属性set方法，本文也会告知读者如何优化KVC。

<!--more-->

---

<br/>
# KVC 使用

KVC（Key-value coding），键值编码。利用遵循非正式协议NSKeyValueCoding的对象，可以通过key-value关系访问对象的ivar列表。KVO、Core Data、Cocoa bindings、AppleScript均是基于KVC实现的。

提供了如下方法进行调用
```
// Objective-C
- (nullable id)valueForKey:(NSString *)key;
- (void)setValue:(nullable id)value forKey:(NSString *)key; 
- (nullable id)valueForKeyPath:(NSString *)keyPath;
- (void)setValue:(nullable id)value forKeyPath:(NSString *)keyPath;
...

// swift
// 苹果在Swift 4 中苹果修改了自动添加 @objc 的逻辑，在使用KVC时记得添加
open func value(forKey key: String) -> Any?
open func setValue(_ value: Any?, forKey key: String)
...
```

<br/>

# KVC 原理

使用KVC设置对象成员变量的值
```
[obj setValue:ValueObject forKey:@"InstanceKey"];
```

KVC基于runtime，要解释KVC原理，却繁之又繁，为节省读者理解作者语义的时间，下面便使用runtime实现KVC赋值的代码

```
id value = NSObject.new;
Class class = objc_getClass("TmpObject");
id object = class_createInstance(class, class_getInstanceSize(class));
Ivar ivar = class_getInstanceVariable(class, "instance_name");
object_setIvar(object, ivar, value);
```

使用KVC时会发现一个严重的问题，给属性赋值时，override set方法不会被invoke，那么该如何解决这个问题呢？

可以用`objc_msgSend(void /* id self, SEL op, ... */ )`在设置成员变量值时，直接调用SEL。但是由于该函数被限制，使用时会如下警告:
<img src="/resourse/iOS/KVC/0.png" style="margin-left:0px" width="1080">

但是我们可以退而求其次，使用`- (id)performSelector:(SEL)aSelector withObject:(id)object;`也可以打到我们的预期效果。
```
- (void)optimizedCSetValue:(id)value forKey:(NSString *)key
{
    if (!key) return;
    if (key.length == 0) return;
    NSString *prefix = [key substringToIndex:1].uppercaseString;
    NSString *subkey = [key substringFromIndex:1];
    NSString *selName = [NSString stringWithFormat:@"set%@%@:", prefix, subkey];
    SEL sel = NSSelectorFromString(selName);
    NSAssert([self respondsToSelector:sel], @"当前对象不能响应该方法");
    [obj performSelectorInBackground:sel withObject:value];
}
```

但是该方法值只能对id类型的属性赋值，并执行属性的set方法。如果是scalar和struct类型的属性，难道就无计可施了吗？

66给你指条明路，就是使用NSIvocation来实现，NSIvocation的`- (void)setArgument:(void *)argumentLocation atIndex:(NSInteger)idx;`方法可以实现赋值任意类型`属性`的值。
```
- (void)optimizedCSetValue:(void *)value forKey:(NSString *)key
{
    if (!key) return;
    if (key.length == 0) return;
    NSString *prefix = [key substringToIndex:1].uppercaseString;
    NSString *subkey = [key substringFromIndex:1];
    NSString *selName = [NSString stringWithFormat:@"set%@%@:", prefix, subkey];
    SEL sel = NSSelectorFromString(selName);
    NSAssert([self respondsToSelector:sel], @"当前对象不能响应该方法");
    if (![self respondsToSelector:sel]) return;
    NSMethodSignature *ms = [self methodSignatureForSelector:sel];
    NSInvocation *inv = [NSInvocation invocationWithMethodSignature:ms];
    [inv setTarget:self];
    [inv setSelector:sel];
    [inv setArgument:value atIndex:2];
    [inv invoke];
}
```

