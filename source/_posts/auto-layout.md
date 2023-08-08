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
        let imageView = UIImageView(image: UIImage(named: "doge"))
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
        let imageView = UIImageView(image: UIImage(named: "doge"))
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

我们并不想把Image放在正中心，而是中心偏上的部分，因此可以使用`topAnchor`:
```swift
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        view.backgroundColor = .systemYellow
        let imageView = UIImageView(image: UIImage(named: "doge"))
        view.addSubview(imageView)
        
        imageView.translatesAutoresizingMaskIntoConstraints = false
        imageView.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        imageView.topAnchor.constraint(equalTo: view.topAnchor, constant: 100).isActive = true
        imageView.heightAnchor.constraint(equalToConstant: 200).isActive = true
        imageView.widthAnchor.constraint(equalToConstant: 200).isActive = true
    }
}
```

得到的结果如图所示：
{% asset_img auto-layout-03.png 300 %}

## 4. 整理代码
我们可以把创建`UIImageView`的代码提取出来，放到`viewDidLoad`之外，这样代码看起来更整洁一些

```swift
import UIKit

class ViewController: UIViewController {

    let dogeImageView: UIImageView = {
        let imageView = UIImageView(image: UIImage(named: "doge"))
        imageView.translatesAutoresizingMaskIntoConstraints = false
        return imageView
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        view.backgroundColor = .systemYellow
        view.addSubview(dogeImageView)
        configureLayout()
    }
    
    func configureLayout() {
        dogeImageView.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        dogeImageView.topAnchor.constraint(equalTo: view.topAnchor, constant: 100).isActive = true
        dogeImageView.heightAnchor.constraint(equalToConstant: 200).isActive = true
        dogeImageView.widthAnchor.constraint(equalToConstant: 200).isActive = true
    }
}
```

得到的结果如图所示：
{% asset_img auto-layout-04.png 300 %}

再加上一些style：
`textAlignment`：文字对齐方式
`isEditable`：是否可以编辑，默认是true
`isScrollEnabled`：是否可以滚动，默认是true

```swift
import UIKit

class ViewController: UIViewController {

    let dogeImageView: UIImageView = {
        let imageView = UIImageView(image: UIImage(named: "doge"))
        imageView.translatesAutoresizingMaskIntoConstraints = false
        return imageView
    }()
    
    let descriptionTextView: UITextView = {
        let textView = UITextView()
        textView.translatesAutoresizingMaskIntoConstraints = false
        textView.text = "Join us today in our fun and games!"
        textView.font = UIFont.boldSystemFont(ofSize: 18)
        textView.textAlignment = .center
        textView.isEditable = false
        textView.isScrollEnabled = false
        textView.backgroundColor = .systemYellow
        return textView
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        view.backgroundColor = .systemYellow
        view.addSubview(dogeImageView)
        view.addSubview(descriptionTextView)
        configureLayout()
    }
    
    func configureLayout() {
        dogeImageView.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        dogeImageView.topAnchor.constraint(equalTo: view.topAnchor, constant: 100).isActive = true
        dogeImageView.heightAnchor.constraint(equalToConstant: 200).isActive = true
        dogeImageView.widthAnchor.constraint(equalToConstant: 200).isActive = true
        
        descriptionTextView.topAnchor.constraint(equalTo: dogeImageView.bottomAnchor, constant: 150).isActive = true
        descriptionTextView.leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
        descriptionTextView.trailingAnchor.constraint(equalTo: view.trailingAnchor).isActive = true
        descriptionTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true
    }
}
```
结果如图所示：
{% asset_img auto-layout-05.png 300 %}

## 5. 支持旋转
在旋转成横屏后，图片会被压缩变形，并且文字会被遮挡，我们需要对其进行调整

{% asset_img auto-layout-06.png height=200 %}

解决图片压缩，可以设置`contentMode`为`.scaleAspectFit`，让图片的宽高比保持不变
解决文字被遮挡，一个方法是使用container view，让它只占屏幕的一个百分比，然后把图片放在container view里，这样就可以保证有足够的空间来显示文字

```swift
import UIKit

class ViewController: UIViewController {
    
    let dogeImageView: UIImageView = {
        let imageView = UIImageView(image: UIImage(named: "doge"))
        imageView.translatesAutoresizingMaskIntoConstraints = false
        imageView.contentMode = .scaleAspectFit
        return imageView
    }()
    
    let descriptionTextView: UITextView = {
        let textView = UITextView()
        textView.translatesAutoresizingMaskIntoConstraints = false
        textView.text = "Join us today in our fun and games!"
        textView.font = UIFont.boldSystemFont(ofSize: 18)
        textView.textAlignment = .center
        textView.isEditable = false
        textView.isScrollEnabled = false
        textView.backgroundColor = .systemYellow
        return textView
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        view.backgroundColor = .systemYellow
        view.addSubview(descriptionTextView)
        configureLayout()
    }
    
    func configureLayout() {
        let topImageContainerView = UIView()
        view.addSubview(topImageContainerView)
        topImageContainerView.translatesAutoresizingMaskIntoConstraints = false
        
        topImageContainerView.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
        topImageContainerView.leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
        topImageContainerView.trailingAnchor.constraint(equalTo: view.trailingAnchor).isActive = true
        topImageContainerView.heightAnchor.constraint(equalTo: view.heightAnchor, multiplier: 0.5).isActive = true
        topImageContainerView.addSubview(dogeImageView)
        
        dogeImageView.centerXAnchor.constraint(equalTo: topImageContainerView.centerXAnchor).isActive = true
        dogeImageView.centerYAnchor.constraint(equalTo: topImageContainerView.centerYAnchor).isActive = true
        dogeImageView.heightAnchor.constraint(equalTo: topImageContainerView.heightAnchor, multiplier: 0.5).isActive = true
        
        descriptionTextView.topAnchor.constraint(equalTo: topImageContainerView.bottomAnchor).isActive = true
        descriptionTextView.leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
        descriptionTextView.trailingAnchor.constraint(equalTo: view.trailingAnchor).isActive = true
        descriptionTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true
    }
}
```
如图：
{% asset_img auto-layout-07.png height=200 %}

## 6. 使用`attributedText`添加副标题
`UITextView`的`text`属性是`String`类型，如果我们想要在文字中添加一些样式，比如颜色、字体等，就需要使用`attributedText`属性，它的类型是`NSAttributedString`，我们可以使用`NSMutableAttributedString`来创建它

```swift
import UIKit

class ViewController: UIViewController {
    
    let dogeImageView: UIImageView = {
        let imageView = UIImageView(image: UIImage(named: "doge"))
        imageView.translatesAutoresizingMaskIntoConstraints = false
        imageView.contentMode = .scaleAspectFit
        return imageView
    }()
    
    let descriptionTextView: UITextView = {
        let textView = UITextView()
        let attributedText = NSMutableAttributedString(
            string: "Join us today in our fun and games!",
            attributes: [NSAttributedString.Key.font: UIFont.boldSystemFont(ofSize: 18)])
        
        attributedText.append(NSMutableAttributedString(
            string: "\n\n\nAre you ready for loads and loads of fun? Don't wait any longer! We hope to see you in our stores soon.",
            attributes: [NSAttributedString.Key.font: UIFont.systemFont(ofSize: 13),
                         NSAttributedString.Key.foregroundColor: UIColor.gray]))
        textView.attributedText = attributedText
        
        textView.translatesAutoresizingMaskIntoConstraints = false
        textView.textAlignment = .center
        textView.isEditable = false
        textView.isScrollEnabled = false
        textView.backgroundColor = .systemYellow
        return textView
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        view.backgroundColor = .systemYellow
        view.addSubview(descriptionTextView)
        configureLayout()
    }
    
    func configureLayout() {
        let topImageContainerView = UIView()
        view.addSubview(topImageContainerView)
        topImageContainerView.translatesAutoresizingMaskIntoConstraints = false
        
        topImageContainerView.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
        topImageContainerView.leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
        topImageContainerView.trailingAnchor.constraint(equalTo: view.trailingAnchor).isActive = true
        topImageContainerView.heightAnchor.constraint(equalTo: view.heightAnchor, multiplier: 0.5).isActive = true
        topImageContainerView.addSubview(dogeImageView)
        
        dogeImageView.centerXAnchor.constraint(equalTo: topImageContainerView.centerXAnchor).isActive = true
        dogeImageView.centerYAnchor.constraint(equalTo: topImageContainerView.centerYAnchor).isActive = true
        dogeImageView.heightAnchor.constraint(equalTo: topImageContainerView.heightAnchor, multiplier: 0.5).isActive = true
        
        descriptionTextView.topAnchor.constraint(equalTo: topImageContainerView.bottomAnchor).isActive = true
        descriptionTextView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 24).isActive = true
        descriptionTextView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -24).isActive = true
        descriptionTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true
    }
}
```
如图：
{% asset_img auto-layout-08.png 300 %}

## 7. 添加按钮
StackView可以帮助解决safeArea的问题，我们可以把按钮放在一个StackView中，然后把StackView放在底部，这样就不用考虑safeArea的问题了

```swift
import UIKit

class ViewController: UIViewController {
    
    let dogeImageView: UIImageView = {
        let imageView = UIImageView(image: UIImage(named: "doge"))
        imageView.translatesAutoresizingMaskIntoConstraints = false
        imageView.contentMode = .scaleAspectFit
        return imageView
    }()
    
    let descriptionTextView: UITextView = {
        let textView = UITextView()
        let attributedText = NSMutableAttributedString(
            string: "Join us today in our fun and games!",
            attributes: [NSAttributedString.Key.font: UIFont.boldSystemFont(ofSize: 18)])
        
        attributedText.append(NSMutableAttributedString(
            string: "\n\n\nAre you ready for loads and loads of fun? Don't wait any longer! We hope to see you in our stores soon.",
            attributes: [NSAttributedString.Key.font: UIFont.systemFont(ofSize: 13),
                         NSAttributedString.Key.foregroundColor: UIColor.gray]))
        textView.attributedText = attributedText
        
        textView.translatesAutoresizingMaskIntoConstraints = false
        textView.textAlignment = .center
        textView.isEditable = false
        textView.isScrollEnabled = false
        textView.backgroundColor = .systemYellow
        return textView
    }()
    
    private let previousButton: UIButton = {
        let button = UIButton(type: .system)
        button.setTitle("Prev", for: .normal)
        button.translatesAutoresizingMaskIntoConstraints = false
        button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
        button.setTitleColor(.gray, for: .normal)
        return button
    }()
    
    private let nextButton: UIButton = {
        let button = UIButton(type: .system)
        button.setTitle("Next", for: .normal)
        button.translatesAutoresizingMaskIntoConstraints = false
        button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
        button.setTitleColor(.red, for: .normal)
        return button
    }()
    
    private let pageControl: UIPageControl = {
        let pc = UIPageControl()
        pc.currentPage = 0
        pc.numberOfPages = 4
        pc.currentPageIndicatorTintColor = .red
        pc.pageIndicatorTintColor = .gray
        return pc
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        view.backgroundColor = .systemYellow
        view.addSubview(descriptionTextView)
        
        configureBottomControls()
        
        configureLayout()
    }
    
    fileprivate func configureBottomControls() {
        let bottomControlsStackView = UIStackView(arrangedSubviews: [previousButton, pageControl, nextButton])
        bottomControlsStackView.translatesAutoresizingMaskIntoConstraints = false
        bottomControlsStackView.distribution = .fillEqually
        bottomControlsStackView.axis = .horizontal
        
        view.addSubview(bottomControlsStackView)
        
        NSLayoutConstraint.activate([
            bottomControlsStackView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
            bottomControlsStackView.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
            bottomControlsStackView.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor),
            bottomControlsStackView.heightAnchor.constraint(equalToConstant: 50)
        ])
    }
    
    func configureLayout() {
        let topImageContainerView = UIView()
        view.addSubview(topImageContainerView)
        topImageContainerView.translatesAutoresizingMaskIntoConstraints = false
        
        NSLayoutConstraint.activate([
            topImageContainerView.topAnchor.constraint(equalTo: view.topAnchor),
            topImageContainerView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            topImageContainerView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            topImageContainerView.heightAnchor.constraint(equalTo: view.heightAnchor, multiplier: 0.5)
        ])
        topImageContainerView.addSubview(dogeImageView)
        
        NSLayoutConstraint.activate([
            dogeImageView.centerXAnchor.constraint(equalTo: topImageContainerView.centerXAnchor),
            dogeImageView.centerYAnchor.constraint(equalTo: topImageContainerView.centerYAnchor),
            dogeImageView.heightAnchor.constraint(equalTo: topImageContainerView.heightAnchor, multiplier: 0.5)
        ])
        
        NSLayoutConstraint.activate([
            descriptionTextView.topAnchor.constraint(equalTo: topImageContainerView.bottomAnchor),
            descriptionTextView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 24),
            descriptionTextView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -24),
            descriptionTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor)
        ])
    }
}
```
如图：
{% asset_img auto-layout-09.png 300 %}