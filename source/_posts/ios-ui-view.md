---
title: ios-ui-view
date: 2023-08-02 11:48:26
tags:
---
View和Control是app的UI的基本元素。

View里面可以包含其他的View （Superview，Subview）

- UIView：所有View的基类
- UIControl：control的基类，包含button、switch、slider等

```swift
@MainActor
class UIView: UIResponder
```

```swift
@MainActor
class UIControl: UIView
```

## View对象的职责：
- 绘制和动画
  - View使用UIKit或者Core Graphics在自己的矩形中绘制内容
  - 可以改变属性来控制动画
- 布局和子视图管理
  - View可以包含其他的View
  - View可以管理子视图的位置和大小
  - 使用Auto Layout来定义View的resizing和reposioning规则来响应view hierarchy的变化
- 事件处理
  - View是UIResponder的子类，可以处理触摸事件和其他类型的事件
  - View可以安装手势识别器来处理手势

默认情况下，subview的可见区域超出superview的边界时，超出部分不会被裁剪，可以通过设置clipsToBounds属性来改变这种行为。

- frame：在superview的坐标系中定义了View的原点和尺寸
- bounds：定义了View自己看到的内部尺寸，几乎专门用于自定义绘制代码
- center：提供了一种方便的重新定位View的方法，无需直接更改frame或bounds属性

## 创建View
- 通过Storyboard创建
- 通过代码创建

例如，以下代码把View放置在superview坐标系统的左上角(10, 10)的坐标上：
```swift
let rect = CGRect(x: 10, y: 10, width: 100, height: 100)
let myView = UIView(frame: rect)
```
通过调用`addSubview(_:)`方法，可以把View添加到superview中，每次调用该方法时，会把新的View放置在所有siblings的最上面。

可以通过调用`insertSubview(_:aboveSUbview:)`或者`insertSubview(_:belowSubview:)`方法来改变z轴顺序。

## 绘制View
View的绘制是在需要时进行的。当一个`View`首次显示出来，或者由于布局变化而使其全部或部分变得可见时，系统会要求`View`绘制其内容。对于包含使用UIKit或Core Graphics进行自定义内容的`View`，系统会调用`View`的`draw(_:)`方法。你实现这个方法负责将`View`的内容绘制到当前的图形上下文中，该图形上下文由系统在调用此方法之前自动设置。这将创建一个`View`内容的static representation，并可以在屏幕上显示。

当View的实际内容发生变化时，你需要负责通知系统重新绘制View。你可以通过调用View的`setNeedsDisplay()`或`setNeedsDisplay(_:)`方法来实现。这些方法告诉系统在下一个绘制周期中更新`View`。由于它等待到下一个绘制周期才更新`View`，你可以在多个`View`上调用这些方法，同时更新它们。

## 动画View
可动画的属性：
- frame
- bounds
- center
- transform
- alpha
- backgroundColor

创建一个`UIViewPropertyAnimator`对象，用它的handler block来改变View的属性

## 线程
必须在主线程

## Override的方法
- Initialization
  - init(frame:) - 推荐使用
  - init(coder:) - 从storyboard或者nib文件中加载时使用
  - layerClass - 只在想要使用不同的Core Animation layer时才需要使用
- Drawing and painting
  - draw(_:) - 如果View需要自定义内容，需要实现这个方法。如果View不进行任何自定义绘制，不要实现这个方法
  - draw(_:for:) - 仅在你想在打印时以不同方式绘制View内容时才实现这个方法
- Layout and Constraints
  - requiresConstraintBasedLayout - 如果需要constraints
  - updateConstraints - 如果view和subviews之间需要自定义constraints
  - alignmentRect(forFrame:), frame(forAlignmentRect:) - view和其他view对齐时使用
  - didAddSubview(_:), willRemoveSubview(_:) - 用来track subview的添加和删除
  - willMove(toSuperview:), didMoveToSuperview() - 用来track你的view在view hierarchy之间的变化
- Event Handling
  - gestureRecognizerShouldBegin(_:) - 如果你的视图直接处理触摸事件，并且可能希望阻止附加的手势识别器触发其他操作，请实现这个方法
  - touchesBegan(_:with:), touchesMoved(_:with:), touchesEnded(_:with:), touchesCancelled(_:with:) - 如果想用来直接处理触摸事件 （对于手势基本输入，使用gesture recognizers）

## Subclassing的替代方案
- addConstraints(_:)
- autoresizingMask
- contentMode
- isHidden或alpha
- backgroundColor
- Subviews
- Gesture recognizers
- Animations
- Image-based backgrounds

# 常用的View
## Container views
### Collection views
[Documentation](https://developer.apple.com/documentation/uikit/views_and_controls/collection_views)

- Cells
- Layouts
- Data source object
- Delegate object
- Collection view controller

{% asset_img collection-views@2x.png %}

### Table views
[Documentation](https://developer.apple.com/documentation/uikit/views_and_controls/table_views)

- Cells
- Table view controller
- Data source object
- Delegate object

{% asset_img table-views@2x.png %}

### UICollectionView
[Documentation](https://developer.apple.com/documentation/uikit/uicollectionview)

{% asset_img ui-collection-view@2x.png %}

```swift
@MainActor
class UICollectionView : UIScrollView
```

### UIStackView
[Documentation](https://developer.apple.com/documentation/uikit/uistackview)

{% asset_img ui-stack-view@2x.png %}

```swift
@MainActor
class UIStackView : UIView
```

### UIScrollView

## Content views
### UIActivityIndicatorView
### UICalendarView
### UIImageView
### UIPickerView
### UIProgressView

