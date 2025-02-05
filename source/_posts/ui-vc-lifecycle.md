---
title: iOS中的UIViewController生命周期
date: 2023-08-04 13:33:28
tags: [iOS]
categories:
- [iOS]
---
## `viewDidLoad()`

在content view在内存中被创建，或者从Storyboard加载时调用
适合设置Notification、从Internet加载数据，设置一些UI的初始状态

## `viewWillAppear(_ animated: Bool)`

在content view将要被加载到View hierarchy中时调用
不显示在屏幕上的情况：
- 当前view controller的view被另一个view controller的view覆盖
- 当前view controller的view的IsHidden属性被设置为true

## `viewDidAppear(_ animated: Bool)`

在content view已经被加载到View hierarchy中时调用
适合执行一些动画，以及通过API加载数据

## `viewWillDisappear(_ animated: Bool)`

在content view将要从View hierarchy中移除时调用

## `viewDidDisappear(_ animated: Bool)`

在content view已经从View hierarchy中移除时调用

## `viewWillLayoutSubviews()`

在content view的bounds发生改变时，但lays out它的subviews之前调用
比如：当设备旋转时，会调用这个方法

## `viewDidLayoutSubviews()`

在content view的bounds发生改变时，但lays out它的subviews之后调用

{% asset_img view-lifecycle.png %}