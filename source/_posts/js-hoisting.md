---
title: JavaScript 提升（Hoisting）
date: 2025-02-10 13:56:02
tags: [JavaScript]
categories:
- [JavaScript]
---
## 1. 什么是提升？

**提升（Hoisting）** 是指 JavaScript 在执行代码之前，会**将变量和函数的声明提升到当前作用域的最顶端**。但变量的**赋值不会被提升**，这可能会导致一些意想不到的行为。

---

## 2. JavaScript 代码执行过程

JavaScript 代码执行分为两个阶段：

1. **编译阶段（Parsing & Compilation）**
   - 变量和函数声明被存入内存。
   - `var` 声明的变量会被默认初始化为 `undefined`。
   - 函数声明会完整提升，允许在定义前调用。

2. **执行阶段（Execution）**
   - 代码按照书写顺序执行。
   - 变量赋值发生在此阶段。

---

## 3. 变量提升

### 3.1 `var` 变量的提升

```js
console.log(a); // undefined（变量提升，但未赋值）
var a = 10;
console.log(a); // 10
```

#### 3.1.1 编译器如何处理

- **编译阶段**

```js
// JavaScript 解释器看到 `var a = 10;`，会将 `var a;` 提升到顶部：
var a;  // 变量声明被提升，但不会赋值
```

- **执行阶段**

```js
console.log(a); // undefined（变量已经存在，但未赋值）
a = 10;         // 赋值
console.log(a); // 10
```

#### 3.1.2 结论

- `var`声明的变量会被**提升**，但不会提升赋值部分，所以会得到`undefined`

### 3.2 `let` 和 `const` 的提升

```js
console.log(b); // ❌ ReferenceError: Cannot access 'b' before initialization
let b = 20;
console.log(b); // 20
```

#### 3.2.1 编译器如何处理

- **编译阶段**

```js
let b;  // 变量 b 被存储，但不会初始化
```

- **执行阶段**

```js
console.log(b); // ❌ 访问 b 时会报错（进入 "暂时性死区"）
b = 20; // 赋值
console.log(b); // 20
```

#### 3.2.2 结论

- `let` 和 `const` 也会被提升，但不会初始化。
- 在变量声明之前访问 `let` 或 `const` 变量会触发 **"暂时性死区（Temporal Dead Zone, TDZ）"**，导致 `ReferenceError`。

---

## 4. 函数提升

### **4.1 函数声明（Function Declaration）**

```js
foo(); // ✅ 输出 "Hello"
function foo() {
    console.log("Hello");
}
```

#### 4.1.1 编译器如何处理

- **编译阶段**

```js
function foo() {
    console.log("Hello");
} // 函数整个被提升
```

- **执行阶段**

```js
foo(); // "Hello" ✅ 可以在定义前调用
```

#### 4.1.2 结论

- 函数声明会被**完整提升**，包括函数体，因此可以在**定义前调用**。

### **4.2 函数表达式（Function Expression）**

```js
bar(); // ❌ TypeError: bar is not a function
var bar = function() {
    console.log("Hi");
};
```

#### **4.2.1 编译器如何处理**

- **编译阶段**

```js
var bar; // 变量 bar 被提升，但未赋值
```

- **执行阶段**

```js
bar(); // ❌ 这里 bar 还是 undefined，所以会报错
bar = function() {
    console.log("Hi");
};
```

#### 4.2.2 结论

- 只有 `var` 声明的变量部分会被提升，而函数赋值不会被提升。
- 访问 `var` 声明的函数表达式变量时，值是 `undefined`，调用时会触发 `TypeError`。

---

## 5. 关键总结

| 变量/函数类型  | 是否提升？ | 是否初始化？ | 是否可提前使用？ |
|--------------|--------|---------|------------|
| `var` 变量  | ✅ 是  | ✅ `undefined` | ⚠️ 只能访问 `undefined` |
| `let` 变量  | ✅ 是  | ❌ 否（TDZ） | ❌ 访问会报错 |
| `const` 变量 | ✅ 是  | ❌ 否（TDZ） | ❌ 访问会报错 |
| 函数声明 `function foo() {}` | ✅ 是 | ✅ 是 | ✅ 可以提前调用 |
| 函数表达式 `var bar = function() {}` | ✅ 是（`var bar` 提升） | ❌ 否 | ❌ 访问 `undefined`，调用时报错 |

---

## 6. 最佳实践

1. **使用 `let` 和 `const` 代替 `var`**，避免变量提升带来的问题。
2. **变量声明尽量放在作用域的顶部**，减少意外行为。
3. **在调用前定义函数**，提高代码可读性和可维护性。
4. **避免在块级作用域外访问 `let` 或 `const` 变量**，防止 `TDZ` 错误。

