---
title: iOS的Auto Layout
date: 2023-08-07 16:16:38
tags: [计算机, 学习]
categories:
- [计算机, iOS]
---
我们来动手实现一个Auto layout
## 1. 初始化一个iOS App
我们打算用代码来实现一个Auto Layout，所以我们不需要使用Storyboard，我们可以在创建项目的时候把Storyboard去掉
同时修改`SceneDelegate.swift`，定义`UIWindow`，并且给window指定一个`rootViewController`。代码如下：

```swift
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
    // Use this method to optionally configure and attach the UIWindow `window` to the provided UIWindowScene `scene`.
    // If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
    // This delegate does not imply the connecting scene or session are new (see `application:configurationForConnectingSceneSession` instead).
    guard let windowScene = (scene as? UIWindowScene) else { return }
    window = UIWindow(windowScene: windowScene)
    
    window?.rootViewController = ViewController()
    window?.makeKeyAndVisible()
}
```

## 2. 添加一个ImageView
我们在`ViewController`的`viewDidLoad`方法里添加一个`UIImageView`，并且设置它的`image`属性为`UIImage(named: "image")`，代码如下：

```swift
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        view.backgroundColor = .systemYellow
        let imageView = UIImageView(image: UIImage(named: "migration"))
        imageView.frame = CGRect(x: 0, y: 0, width: 200, height: 200)
        view.addSubview(imageView)
    }
}
```
得到的结果如图所示：
{% asset_img auto-layout-01.png 300 %}

这里使用的是基于frame的layout，需要手动计算和调整位置

## 3. 使用Auto Layout
我们来添加一些约束，使用Auto Layout来实现布局

```swift
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        view.backgroundColor = .systemYellow
        let imageView = UIImageView(image: UIImage(named: "migration"))
        view.addSubview(imageView)
        
        imageView.translatesAutoresizingMaskIntoConstraints = false
        imageView.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        imageView.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
        imageView.heightAnchor.constraint(equalToConstant: 200).isActive = true
        imageView.widthAnchor.constraint(equalToConstant: 200).isActive = true
    }
}
```
得到的结果如图所示：
{% asset_img auto-layout-02.png 300 %}