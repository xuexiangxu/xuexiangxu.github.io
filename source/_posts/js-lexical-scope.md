---
title: JavaScript 的词法作用域（Lexical Scope）
date: 2025-02-06 09:59:19
tags: [JavaScript]
categories:
- [JavaScript]
---

### **1. 什么是词法作用域？**
**词法作用域（Lexical Scope）** 是指 **变量的作用域** 在 **代码编写时** 就已经确定，而不是在代码运行时动态决定的。

换句话说，**作用域由代码的嵌套结构决定，而不是由函数调用方式决定**。

---

### **2. 词法作用域示例**
```javascript
function outer() {
  let a = "Hello";
  
  function inner() {
    console.log(a); // 词法作用域：inner() 可以访问 outer() 里的变量 a
  }

  inner();
}

outer(); // 输出：Hello
```
🔹 **解释：**
- `inner()` 是 **定义在 `outer()` 内部** 的，所以 `inner()` 可以访问 `outer()` 作用域中的变量 `a`。
- `a` 在 `inner()` **外部作用域定义，但仍然可以被访问**，这就是 **词法作用域**。

---

### **3. 词法作用域 vs 动态作用域**
#### **🔹 词法作用域（JavaScript 使用）**
作用域在 **定义时** 确定，与 **调用方式无关**。

```javascript
function outer() {
  let a = "Hello";

  function inner() {
    console.log(a);
  }

  return inner;
}

const fn = outer(); // `fn` 现在是 `inner` 函数
fn(); // 输出：Hello
```
即使 `fn` 在 `outer()` 执行完后才调用，它仍然能访问 `outer()` 作用域中的 `a`，因为 **作用域在定义时就已确定**。

#### **🔹 动态作用域（JavaScript 不使用）**
一些编程语言（如 Bash、Perl 早期版本）使用 **动态作用域（Dynamic Scope）**，即 **作用域取决于函数的调用位置**，而不是定义位置。但 **JavaScript 不使用动态作用域**，而是 **词法作用域**。

---

### **4. 嵌套作用域（作用域链）**
JavaScript 的作用域是 **嵌套** 的，每个函数定义时都会“记住”它创建时的作用域。

```javascript
const globalVar = "I am global";

function outer() {
  const outerVar = "I am outer";

  function inner() {
    const innerVar = "I am inner";
    console.log(globalVar); // ✅ 访问全局作用域变量
    console.log(outerVar);  // ✅ 访问外部作用域变量
    console.log(innerVar);  // ✅ 访问自身作用域变量
  }

  inner();
}

outer();
```
🔹 **作用域链查找顺序：**
1. 先在 `inner()` 内查找变量。
2. 如果找不到，就往 `outer()` 作用域查找。
3. 如果还找不到，就往 **全局作用域** 查找。
4. 找不到就报错 `ReferenceError`。

---

### **5. 块级作用域**
ES6 之前，JavaScript 只有 **函数作用域（Function Scope）**，但 ES6 引入了 `let` 和 `const`，支持 **块级作用域（Block Scope）**。

```javascript
{
  let x = 10;
  const y = 20;
}
console.log(x); // ❌ ReferenceError: x is not defined
console.log(y); // ❌ ReferenceError: y is not defined
```
🔹 **作用域类型：**
- `var`：**函数作用域**
- `let` / `const`：**块级作用域**
- `function`：**函数作用域**
- `{}`（代码块）：**块级作用域（仅 `let` 和 `const`）**

---

### **6. 闭包与词法作用域**
**闭包（Closure）** 是 **函数可以“记住”其定义时的作用域，即使函数在作用域外调用**。

```javascript
function outer() {
  let count = 0;

  return function inner() {
    count++;
    console.log(count);
  };
}

const counter = outer();
counter(); // 输出：1
counter(); // 输出：2
```
🔹 `inner()` 记住了 `outer()` 的作用域，即使 `outer()` 已经执行完毕，`count` 仍然保留在内存中。这就是 **闭包的作用**，它依赖于 **词法作用域**。

---

### **7. 词法作用域总结**
✅ **作用域在代码编写时就已确定，而不是运行时决定**。  
✅ **内部函数可以访问外部作用域的变量（作用域链）**。  
✅ **JavaScript 不是动态作用域，而是词法作用域**。  
✅ **闭包（Closure）依赖词法作用域，使得函数可以记住创建时的作用域**。  

---

### **8. 常见问题**
#### **(1) 下面代码的输出是什么？**
```javascript
var a = 10;
function test() {
  console.log(a);
  var a = 20;
}
test();
```
✅ **输出：`undefined`**（不是 `10`！）  
🔹 **原因**：  
- JavaScript 变量提升（Hoisting）：`var a` 被提升到 `test()` 作用域顶部，默认为 `undefined`，但赋值 `20` 发生在 `console.log(a)` 之后。

等价于：
```javascript
function test() {
  var a; // 变量提升，默认值 `undefined`
  console.log(a); // undefined
  a = 20;
}
```

#### **(2) 如何让 `setTimeout` 循环正确打印 `0, 1, 2, 3, 4`？**
```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 1000);
}
```
🔴 **错误输出：`5, 5, 5, 5, 5`**
- 因为 `var` 是 **函数作用域**，循环结束后，`i` 的值是 `5`，所有 `setTimeout` 共享同一个 `i`。

✅ **解决方法：使用 `let`**
```javascript
for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 1000);
}
```
✅ **或者使用闭包**
```javascript
for (var i = 0; i < 5; i++) {
  (function (j) {
    setTimeout(() => console.log(j), 1000);
  })(i);
}
```

---

