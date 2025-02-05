---
title: iOS中的View Controller
date: 2023-08-01 10:49:57
tags: [iOS]
categories:
- [iOS]
---
`View controller`用来管理`UIKit` app的界面。每个`View Controller`管理一个root view，这个root view可以包含一些subviews。

每个app至少有一个`View Controller`，这个`View Controller`的内容填满主`Window`。
如果你的app有更多内容需要展示，你可以创建更多的`View Controller`，每个`View Controller`管理一个子界面。

`UIKit`提供了一些标准的`View Controller`，例如导航控制器（navigation controller）和分栏控制器（split view controller）。

# 管理app的Window里的内容

你的应用程序界面中的每个场景都包含一个窗口对象（`Window`）和一个或多个视图对象（`View`）。`Window`作为不可见的容器，用于包含其余的UI元素，它是`View`的顶级容器，并将事件路由到`View`。

`View`则提供用户在屏幕上看到文本、图像和其他类型的自定义内容等实际内容。`Window`是长期存在的对象，只有在销毁整个场景的UI时才会将其销毁。相比之下，你可能会频繁地更改Window中的View，特别是当你想要显示新的内容时。

{% asset_img view-controller-architecture@2x.png %}

为了有效的管理你的View，并且能够轻松的将它们从Window中添加或删除，UIKit提供了`View Controller`。`View Controller`管理app的一组`View`，并保持更新这些`View`中的信息。

每个`Window`都有一个`Root View Controller`，可以用它来指定Window的初试`View`的集合。当需要改变这组`View`时，告诉`UIKit`展示或者移除其他的`View Controller`。`UIKit`会把一组`View`换成另一组，并通过`View Controller`对象来管理整个app的界面

## 为每个单独的页面定义View Controller
在设计app的UI时，首先要把UI分成不同内容的页面。每个页面会有不同的方式去展示数据。

重要的是每个页面的结构和整体外观，而非所要展示的具体数据。例如，一个app可能有一个页面展示一个列表，另一个页面展示一个表格，但是这两个页面都是用来展示数据的。

{% asset_img view-controller-types@2x.png %}

每个View Controller需要做以下的事情：
- 为View提供数据，并且在数据改变时更新View
- 将数据变化反馈给数据模型对象
- 调整大小、位置和View的可见性来适配当前环境
- 促进切换到其他内容页面的过渡

## 为你的内容选择Navigation model
UIKit提供了`Container View Controller`，实现了通用的导航（Navigation）模型

`Container View Controller`是一个特殊的`View Controller`，它用来管理其他的子`View Controller`

永远使用UIKit的`Container View Controller`来实现通用的导航模型：
- UINavigationController
- UISplitViewController
- UITabBarController
- UIPageViewController

{% asset_img interface-types@2x.png %}

## 给每个Window分配一个Root View Controller
如果

如果用代码创建你的app的UI，你需要在创建Window后，给Window的`rootViewController`属性赋值。这个属性的值是一个`View Controller`对象，这个对象管理Window的根View。

{% asset_img view-controller-hierarchy@2x.png %}

## Content View Controller
所有的UIKit app非常依赖于`View Controller`来展示内容。我们通常会自定义`View Controller`来管理我们的`View`。

绝大多数的自定义`View Controller`都是`Content View Controller`。`Content View Controller`拥有它们所有的`View`，并且管理这些`View`的交互。

与`Content View Controller`相对的是`Container View Controller`，它将其他的`View Controller`内容整合到它的`View`层级中。

{% asset_img VCPG-custom-subclasses@2x.png %}

为了定义一个`Content View Controller`，首先需要继承`UIViewController`。如果你的界面有table view或者collection view，你可以继承`UITableViewController`或者`UICollectionViewController`。

## 往View Controller中添加View
UIViewController类有一个`view`属性，这个属性是一个`UIView`对象，这个对象是`View Controller`管理的`View`层级的`root view`。
你可以往那个`root view`上添加自定义的`view`。
  
{% asset_img storyboard-add-views@2x.png %}

往view controller加完view后，永远使用Auto Layout constriants来设置大小和位置。

## 存储重要view的引用
有时需要在运行时访问view controller的某些view。例如，你可能需要在运行时改变view的外观，或者在用户点击view时执行某些操作。
```swift
@IBOutlet weak var imageView : UIImageView?
@IBOutlet weak var button : UIButton?
```
如果使用storyboard，outlet会在storyboard加载时被自动设置。UIKit在viewDidLoad()方法之前重建连接outlets，因此可以在这个方法里访问这些属性的对象。
如果使用代码创建view controller，你需要在view controller的初始化方法里设置适当的属性。

## 处理views和controls里的事件
Controls使用target-action设计模式来报告用户交互，一些view会post notification或者调用delegate方法来响应这些改动。
View controller需要知道这些交互，以便更新View。有若干种方法：
- 在View Controller实现delegate和action方法
  - 简单易实现，但不灵活难以测试
- 在View Controller类的extension中实现delegate和action方法
  - 分离了事件处理的代码，更容易测试
- 在专用对象中实现delegate和action方法，然后将相关信息转发给View Controller
  - 最具有灵活性和可复用性，容易测试

为了响应用户与controls的交互，定义一个action方法：
```swift
@IBAction func doSomthing()
@IBAction func doSomething(sender: UIControl)
@IBAction func doSomething(sender: UIControl, forEvent event: UIEvent)
```

## View的展示
当你把view controller展示到屏幕上时，UIKit需要首先加载和配置相应的View：
1. 使用`init(coder:)`方法创建每个view
2. 在View Controller中连接view到对应的action和outlet
3. 调用每个view和View Controller的`awakeFromNib()`方法
4. 给View Controller的`view`属性赋值`view hierarchy`
5. 调用View Controller的`viewDidLoad()`方法

当你的视图首次出现在屏幕上时，执行任何最终与视图相关的任务。UIKit在以下方式中通知拥有视图控制器的对象它的视图即将出现在屏幕上，并更新这些视图的布局以适应当前环境：

1. 在过渡开始时调用`viewWillAppear(_:)`
2. 将`View`添加到`view hierarchy`
3. 更新`View Controller`及其`View`的trait collections（特性集合）
4. 更新`View`的几何属性，包括其在其`superview`中的大小和位置。更新布局边距（layout margins）和安全区域（safe area），并在需要时调用 `viewLayoutMarginsDidChange()` 和 `viewSafeAreaInsetsDidChange()`
5. 调用`viewIsAppearing(_:)`方法，让你知道`View Controller`的`View`正在出现在屏幕上
6. 调用`viewWillLayoutSubviews()`方法
7. 更新`view hierarchy`的布局
8. 调用`viewDidLayoutSubviews()`方法
9. 将`View`显示在屏幕上
10. 在任何动画完成后调用`View Controller`的`viewDidAppear(_:)`方法