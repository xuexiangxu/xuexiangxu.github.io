---
title: JavaScript 防抖（Debounce）和节流（Throttle）
date: 2025-02-12 14:04:28
tags: [JavaScript]
categories:
- [JavaScript]
---

**防抖（Debounce）** 和 **节流（Throttle）** 是两种优化高频执行函数的技术，主要用于**减少函数调用频率，提高性能**，比如处理：

- **窗口 `resize` 事件**
- **搜索框输入**
- **按钮点击**
- **滚动事件 `scroll`**
- **鼠标移动 `mousemove`**

---

## **1. 防抖（Debounce）**

**概念：**  
> **防抖（Debounce）** 使得某个函数在**一段时间内**（如 500ms）**没有再次调用**时才会执行。如果在等待时间内又触发了函数，则重新计时。

**适用场景：**  

- **搜索输入框**：只在用户**停止输入**后才发送请求。
- **窗口 `resize` 事件**：用户停止调整窗口大小后执行计算。

### **🔹 防抖实现**

```javascript
function debounce(fn, delay) {
    let timer; // 计时器
    return function (...args) {
        clearTimeout(timer); // 清除之前的定时器
        timer = setTimeout(() => {
            fn.apply(this, args); // 在 delay 时间后执行
        }, delay);
    };
}
```

### **🔹 使用防抖**

```javascript
function onSearchInput(e) {
    console.log("搜索内容：", e.target.value);
}

const debouncedSearch = debounce(onSearchInput, 500);

document.getElementById("search").addEventListener("input", debouncedSearch);
```

> 用户**连续输入时不会触发** `onSearchInput`，**只有停止输入 500ms 后才执行**。

---

## **2. 节流（Throttle）**

**概念：**  
> **节流（Throttle）** 使得某个函数**在一定时间间隔内**（如 500ms）**只执行一次**，无论它在这段时间内被调用了多少次。

**适用场景：**  

- **按钮点击**：防止**用户连点**按钮造成重复请求。
- **滚动事件**：防止**高频触发 `scroll`** 影响性能。
- **鼠标移动 `mousemove`**：防止触发次数过多影响页面表现。

### **🔹 节流实现**

```javascript
function throttle(fn, interval) {
    let lastTime = 0; // 记录上次执行时间
    return function (...args) {
        const now = Date.now();
        if (now - lastTime >= interval) {
            fn.apply(this, args);
            lastTime = now; // 更新上次执行时间
        }
    };
}
```

### **🔹 使用节流**

```javascript
function onScroll() {
    console.log("页面滚动中...", Date.now());
}

const throttledScroll = throttle(onScroll, 1000);

window.addEventListener("scroll", throttledScroll);
```

> 即使滚动事件**触发上百次**，`onScroll` **每隔 1000ms 只执行一次**。

---

## **3. 防抖 vs. 节流**

| 特性 | 防抖（Debounce） | 节流（Throttle） |
|------|----------------|----------------|
| 触发时机 | **延迟执行**（最后一次触发后执行） | **固定间隔执行**（间隔时间内最多执行一次） |
| 适用场景 | 搜索框输入、表单验证、调整窗口大小 | 按钮点击、滚动事件、鼠标移动 |
| 控制方式 | 清除 `setTimeout` 重新计时 | 记录时间戳 |

---

## **4. 进阶：节流的 `setTimeout` 版本**

除了 `Date.now()` 版本，我们还可以用 `setTimeout` 实现节流：

```javascript
function throttle(fn, interval) {
    let timer = null;
    return function (...args) {
        if (!timer) {
            timer = setTimeout(() => {
                fn.apply(this, args);
                timer = null; // 清空计时器
            }, interval);
        }
    };
}
```

> **区别**：  

- `Date.now()` 版本**立即执行**，适合**立即响应**的场景。
- `setTimeout` 版本**延迟执行**，适合**事件结束后响应**的场景。

---

## **5. 结合防抖和节流**

有些情况我们希望：

- **短时间内的高频触发不要执行（防抖）**。
- **但保证一定时间内至少执行一次（节流）**。

可以结合两者：

```javascript
function throttleAndDebounce(fn, interval) {
    let lastTime = 0;
    let timer = null;

    return function (...args) {
        const now = Date.now();

        if (now - lastTime >= interval) {
            fn.apply(this, args);
            lastTime = now;
        } else {
            clearTimeout(timer);
            timer = setTimeout(() => {
                fn.apply(this, args);
                lastTime = Date.now();
            }, interval);
        }
    };
}
```

---

## **6. 总结**

- **防抖（Debounce）**：多次触发只执行**最后一次**，适用于**搜索、窗口 `resize`** 等场景。
- **节流（Throttle）**：固定间隔执行一次，适用于**滚动事件、鼠标移动、按钮点击**。
- **可以结合防抖和节流**，保证**短时间内不执行，但一定时间内至少执行一次**。

---

🔥 **推荐使用场景**

| 适用场景 | 方案 |
|---------|------|
| 搜索框输入 | **防抖（Debounce）** |
| 按钮防止多次点击 | **节流（Throttle）** |
| 页面 `resize` | **防抖（Debounce）** |
| 滚动 `scroll` 事件 | **节流（Throttle）** |
| 鼠标 `mousemove` | **节流（Throttle）** |
