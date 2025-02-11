---
title: JavaScript中的闭包
date: 2025-02-10 16:26:37
tags: [JavaScript]
categories:
- [JavaScript]
---
### 闭包（Closure）简介

闭包（Closure）是 JavaScript 中的一个非常重要的概念，它指的是一个函数和它的词法环境（即函数定义时的作用域）绑定在一起的现象。换句话说，闭包使得一个函数可以访问其外部函数的变量，即使外部函数已经执行完毕。

### 闭包的形成

闭包的形成需要满足两个条件：

1. **函数内部定义了另一个函数**。
2. **内层函数引用了外层函数的变量**。

### 闭包的特点

- **访问外部变量**：闭包允许内层函数访问外层函数的变量，即使外层函数已经执行完毕。
- **持有外部环境的引用**：内层函数持有外层函数作用域的引用，所以即使外层函数已经返回，外层函数的变量依然会存在。

### 举个例子

  ```javascript
  function outer() {
    let count = 0;  // 外部函数的局部变量

    return function inner() {  // 返回一个内部函数
      count++;  // 内部函数可以访问外部函数的局部变量
      console.log(count);
    };
  }

  const counter = outer();  // 调用外部函数，返回内部函数
  counter();  // 输出 1
  counter();  // 输出 2
  ```

在这个例子中，`inner` 函数是 `outer` 函数的闭包，它能够访问 `outer` 函数中的 `count` 变量。即使 `outer` 函数已经执行结束，`count` 变量依然存在，因为 `inner` 函数仍然持有对 `count` 的引用。

### 闭包的常见用途

1. **数据封装**：使用闭包可以创建私有变量，使得外部代码无法直接访问和修改这些变量，只能通过闭包提供的方法访问或修改。

  ```javascript
  function createCounter() {
    let count = 0;  // 私有变量
    return {
      increment: function() {
        count++;
        return count;
      },
      decrement: function() {
        count--;
        return count;
      },
      getCount: function() {
        return count;
      }
    };
  }

  const counter = createCounter();
  console.log(counter.increment());  // 输出 1
  console.log(counter.getCount());   // 输出 1
  ```

2. **延迟执行**：闭包常用于异步编程，如回调函数或事件处理程序中。由于闭包可以保持对外部环境的引用，它能够确保在未来某个时刻访问这些变量时，值不会丢失。
