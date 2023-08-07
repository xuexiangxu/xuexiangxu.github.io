---
title: ios-tips
date: 2023-08-03 17:15:03
tags: [计算机, 学习]
categories:
- [计算机, iOS]
---

## `translatesAutoresizingMaskIntoConstraints`
https://stackoverflow.com/questions/47800210/when-should-translatesautoresizingmaskintoconstraints-be-set-to-true

- 代码创建View时，View的`translatesAutoresizingMaskIntoConstraints`属性默认为true
- Interface Builder创建View时，View的`translatesAutoresizingMaskIntoConstraints`属性默认为false
- 代码创建的View，使用frame进行布局时，不能添加额外的约束，否则会导致约束冲突
- 代码创建的View，使用Auto Layout进行布局时，必须将`translatesAutoresizingMaskIntoConstraints`属性设置为false
