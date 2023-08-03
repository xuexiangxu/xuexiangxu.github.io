---
title: Stack View
date: 2023-08-03 16:30:17
tags: [计算机, 学习]
categories:
- [计算机, iOS]
---
Stack View能够使用Auto Layout的能力，创建自适应设备方向、屏幕尺寸和内容变化的UI。

通过`arrangedSubviews`属性管理所有View的布局

这些View沿着Stack View的轴线排列，并且按照它们在`arrangedSubviews`数组里的顺序进行布局。

具体的布局取决于：
- axis
- distribution
- alignment
- spacing

以及其他属性
{% asset_img ui-stack-view@2x.png %}

注意：你的职责是定义Stack View的position和size（可选的），然后Stack View会管理layout和内容的大小

## Stack View和自动布局（Auto Layout）
Stack View把它第一个和最后一个View与Stack的轴的边界（edge）对齐

如果设置了`isLayoutMarginsRelativeArrangement`属性为true，Stack View会把它的内容pin到relevant margin上，而不是edge上

对于除了`UIStackView.Distribution.fillEqually`以外的distribution，Stack View会根据每个arranged view到`intrinsiceContentSize`的属性来计算它沿着Stack轴到大小

而对于`UIStackView.Distribution.fillEqually`，则会resize所有的arranged view，使它们的大小相等

Stack View会伸展所有的arranged views，来匹配沿着Stack轴的最长intrinsic size

## 常用的Stack View布局定义
### 只定义position
{% asset_img position-only.png %}
### 定义沿着轴的size
{% asset_img size-along-axis.png %}
### 定义垂直于轴的size
{% asset_img size-perpendicular-axis.png %}
### 定义size和position
{% asset_img size-position.png %}