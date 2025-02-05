---
title: JavaScript 中 LHS 和 RHS 的区别
date: 2025-02-05 17:29:07
tags: [JavaScript]
categories:
- [计算机, JavaScript]
---

在 JavaScript 中，**LHS（Left-Hand Side）** 和 **RHS（Right-Hand Side）** 主要用于描述变量查询的类型，通常出现在 **作用域解析（Scope Resolution）** 过程中。它们的概念来自于 JavaScript 引擎在执行代码时查找变量的方式。

---

## **LHS 查询**
LHS（左侧查询）用于查找**变量的赋值目标**，即**变量出现在赋值运算符的左侧**。

### **示例**
```js
function test() {
    var a = 10; // 'a' 进行 LHS 查询
    b = 20; // 'b' 进行 LHS 查询（但 'b' 未声明，会变成全局变量或报错）
    a++; // 'a' 进行 LHS 查询（相当于 a = a + 1）
}
```
LHS 查询的目标是**找到变量的容器，以便对其赋值**。

### **RHS 查询**

RHS（右侧查询）用于**获取变量的值**，即**变量出现在赋值运算符的右侧或作为表达式的一部分**。

### 示例

```js
function demo() {
    var x = 5; // 'x' 进行 LHS 查询
    var y = x + 10; // 'x' 进行 RHS 查询，因为需要获取 x 的值
    console.log(y); // 'y' 进行 RHS 查询，因为 console.log 需要 y 的值
}
```
RHS 查询的目标是**找到变量的值，以便使用它**。

### **LHS vs RHS 具体示例**
```js
function example() {
    var a = 5;    // 'a' 进行 LHS 查询
    var b = a + 2; // 'a' 进行 RHS 查询，'b' 进行 LHS 查询
    console.log(b); // 'b' 进行 RHS 查询
}
```
| 代码               | LHS 查询 | RHS 查询 |
|--------------------|---------|---------|
| `var a = 5;`      | `a`     | 无      |
| `var b = a + 2;`  | `b`     | `a`     |
| `console.log(b);` | 无      | `b`     |

## **LHS 查询失败的后果**
### **1. 在非严格模式下（非 `use strict`）**
- 如果 LHS 查询的变量未声明，JavaScript 会**默认创建一个全局变量**。
```js
function test() {
    x = 100; // 'x' 进行 LHS 查询，但未声明，因此变成全局变量
}
test();
console.log(x); // 100 （x 变成了全局变量）
```

### **2. 在严格模式下（`use strict`）**
- LHS 查询找不到变量时会抛出 `ReferenceError`。
```js
"use strict";
function test() {
    y = 200; // LHS 查询失败，抛出 ReferenceError
}
test(); // Uncaught ReferenceError: y is not defined
```

---

## **RHS 查询失败的后果**
- **如果 RHS 查询的变量未找到，JavaScript 会抛出 `ReferenceError`**。
```js
function test() {
    console.log(z); // RHS 查询失败，抛出 ReferenceError
}
test(); // Uncaught ReferenceError: z is not defined
```
## **总结**
| 查询类型  | 作用                         | 失败时的行为 |
|----------|------------------------------|-------------|
| **LHS 查询** | 查找**变量的存储位置**，用于**赋值** | **非严格模式**：创建全局变量<br>**严格模式**：`ReferenceError` |
| **RHS 查询** | 查找**变量的值**，用于**读取** | `ReferenceError` |

LHS 主要用于赋值操作，而 RHS 主要用于取值操作。理解它们对于**作用域解析**和**错误调试**非常重要！ 🚀
