---
title: event.target 和 event.currentTarget
date: 2025-02-14 16:38:47
tags: [JavaScript, DOM]
categories:
- [JavaScript]
---

`event.target` 和 `event.currentTarget` 都是事件对象 (`event`) 的属性，它们都可以用来获取事件的目标元素，但有以下关键区别：

### 1. `event.target`

- `event.target` 返回的是**触发事件的实际元素**，即事件的**目标元素**。
- 这是事件冒泡过程中的那个元素，通常是用户与之交互的元素（如按钮、链接、输入框等）。

**例子**：
假设有以下 HTML 结构：

```html
<div id="parent">
    <button id="child">点击我</button>
</div>
```

并且为父元素和子元素绑定了事件监听器：

```javascript
document.getElementById('parent').addEventListener('click', function(event) {
    console.log(event.target);  // 打印触发事件的实际元素
});
```

当点击 `button` 时，`event.target` 会输出：

```html
<button id="child">点击我</button>
```

这是因为 `button` 元素是触发事件的目标。

### 2. `event.currentTarget`

- `event.currentTarget` 返回的是**当前处理事件的元素**，即事件监听器绑定的元素。
- 无论事件是从哪个元素触发的，`currentTarget` 始终指向当前正在执行事件处理函数的元素。

**例子**：
继续以上的 HTML 结构和事件监听器：

```javascript
document.getElementById('parent').addEventListener('click', function(event) {
    console.log(event.currentTarget);  // 打印绑定事件的元素（父元素）
});
```

当点击 `button` 时，`event.currentTarget` 会输出：

```html
<div id="parent">
    <button id="child">点击我</button>
</div>
```

这是因为事件监听器绑定在父元素 `div` 上，`event.currentTarget` 始终指向 `div` 元素。

### 关键区别

- **`event.target`** 是**触发事件的元素**，即事件发生的源头。
- **`event.currentTarget`** 是**事件监听器绑定的元素**，即正在处理当前事件的元素。

### 什么时候用 `event.target` 和 `event.currentTarget`？

- **`event.target`** 用来获取事件的具体目标元素，适用于你想知道事件是在哪个元素上触发的。
- **`event.currentTarget`** 用来获取事件处理函数当前绑定的元素，适用于你想知道事件是在哪个元素上被处理的，通常用于委托事件。

### 事件委托的示例

```javascript
document.getElementById('parent').addEventListener('click', function(event) {
  console.log('目标元素：', event.target);  // 目标元素是点击的子元素
  console.log('当前处理元素：', event.currentTarget);  // 当前处理元素是父元素
});
```

- 如果你点击了 `button`，`event.target` 会是 `button` 元素，而 `event.currentTarget` 会是 `parent` 元素。

### 总结：

- **`event.target`**: 触发事件的实际元素。
- **`event.currentTarget`**: 当前正在处理事件的元素（监听器绑定的元素）。
