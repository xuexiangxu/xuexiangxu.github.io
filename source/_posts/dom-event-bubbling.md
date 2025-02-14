---
title: 事件冒泡
date: 2025-02-14 16:18:55
tags: [JavaScript, DOM]
categories:
- [JavaScript]
---

**事件冒泡（Event Bubbling）** 是 DOM 事件传播的一种机制。它描述了事件从目标元素开始，逐级向上冒泡到父元素、祖先元素，直到 `document` 对象的过程。

### 事件冒泡的过程

1. 当用户在一个元素上触发事件时，事件首先会在该元素上被处理（目标元素）。
2. 然后事件会冒泡到该元素的父元素，接着是父元素的父元素，直到 `document` 对象。
3. 事件会按照这个顺序进行传播，直到事件到达最顶层的元素或者被阻止。

### 例子

假设有如下的 HTML 结构：

```html
<div id="parent">
  <button id="child">点击我</button>
</div>
```

并且在 JavaScript 中给父元素和子元素分别绑定了事件监听器：

```javascript
document.getElementById('parent').addEventListener('click', function() {
  alert('父元素被点击');
});

document.getElementById('child').addEventListener('click', function() {
  alert('子元素被点击');
});
```

当你点击按钮时，事件首先会在按钮上触发（目标元素），然后冒泡到父元素 `div` 上。输出结果将是：

1. `"子元素被点击"`（按钮的点击事件）
2. `"父元素被点击"`（父 `div` 的点击事件）

### 为什么叫“冒泡”？

就像水滴从水面滴下，事件从目标元素冒泡到最顶层一样，因此叫做“事件冒泡”。

### 阻止事件冒泡

你可以使用 `event.stopPropagation()` 来阻止事件继续冒泡到父元素。

**例子**：

```javascript
document.getElementById('child').addEventListener('click', function(event) {
  alert('子元素被点击');
  event.stopPropagation();  // 阻止冒泡
});
```

这时，点击按钮时，只会显示 `"子元素被点击"`，父元素的点击事件不会被触发。

## `event.preventDefault()` 和 `event.stopPropagation()`的区别

`event.preventDefault()` 和 `event.stopPropagation()` 都是用来控制事件流的，但它们的作用不同：

1. **`event.preventDefault()`**:
   - 这个方法用来**阻止事件的默认行为**。例如，点击一个链接时，浏览器默认会跳转到链接地址。如果你调用了 `event.preventDefault()`，浏览器就不会执行这个跳转操作。
   - 它不会停止事件的传播（即事件仍然会冒泡或捕获）。

   **例子**:

   ```javascript
   document.querySelector('form').addEventListener('submit', function(event) {
     event.preventDefault();  // 阻止表单提交
     alert('表单未提交');
   });
   ```

2. **`event.stopPropagation()`**:
   - 这个方法用来**停止事件的传播**，即**阻止事件冒泡**（从目标元素到根元素）或**捕获**（从根元素到目标元素）。
   - 这意味着事件不会继续向上传播到父元素，也不会传递给其他监听该事件的元素。

   **例子**:

   ```javascript
   document.querySelector('button').addEventListener('click', function(event) {
     event.stopPropagation();  // 阻止事件冒泡
     alert('按钮被点击');
   });
   
   document.querySelector('div').addEventListener('click', function() {
     alert('DIV 被点击');
   });
   ```

   在这个例子中，点击按钮时，只会显示 `"按钮被点击"`，而不会触发 `div` 的点击事件。
