---
title: JavaScript 中的 this
date: 2025-02-11 16:50:57
tags: [JavaScript]
categories:
- [JavaScript]
---
JavaScript 中的 `this` 是一个关键字，它的值取决于**函数的调用方式**，而不是它的定义方式。许多开发者对 `this` 存在误解，尤其是在不同的调用场景下它的指向可能会变化。

---

## 🌟 **关于 `this` 的常见误解**

1. **“`this` 指向函数的作用域”** ❌  
   → **实际上** `this` 并不指向词法作用域，而是取决于**调用方式**。

2. **“`this` 总是指向调用它的对象”** ❌  
   → **不完全正确**，在严格模式、独立函数调用等情况下，它可能是 `undefined` 或 `window`（浏览器）/`global`（Node.js）。

3. **“箭头函数的 `this` 和普通函数一样”** ❌  
   → **错误**，箭头函数的 `this` 绑定到**定义时的词法作用域**，不会因为调用方式而改变。

---

## 🎯 **`this` 的不同绑定规则**

### 1️⃣ **默认绑定（非严格模式 vs 严格模式）**

**独立调用时**（不通过对象调用）：  

- **非严格模式**：`this` 指向全局对象（浏览器是 `window`，Node.js 是 `global`）。
- **严格模式**：`this` 为 `undefined`。

```js
function foo() {
  console.log(this);
}
foo(); // 非严格模式下: window / global
       // 严格模式下: undefined
```

---

### 2️⃣ **隐式绑定**

如果函数作为**对象的方法**调用，`this` 绑定到该对象。

```js
const obj = {
  name: "JavaScript",
  showName() {
    console.log(this.name);
  }
};
obj.showName(); // "JavaScript"
```

🚨 **隐式丢失**（常见问题）：

```js
const fn = obj.showName;
fn(); // undefined（默认绑定：window 或 undefined）
```
这里 `fn()` 独立调用，`this` 变为默认绑定。

---

### 3️⃣ **显示绑定（call / apply / bind）**

- `call` / `apply` 直接执行函数，并显式指定 `this`。
- `bind` 返回一个新函数，绑定 `this` 但不会立即执行。

```js
function greet() {
  console.log(this.name);
}

const person = { name: "Alice" };

greet.call(person);  // "Alice"
greet.apply(person); // "Alice"

const boundGreet = greet.bind(person);
boundGreet(); // "Alice"
```

---

### 4️⃣ **`new` 绑定**

通过 `new` 关键字调用函数时：

- `this` 绑定到**新创建的对象**，并返回该对象（除非手动返回其他对象）。

```js
function Person(name) {
  this.name = name;
}

const p1 = new Person("John");
console.log(p1.name); // "John"
```

---

## ⚖ **绑定规则优先级**

当多个绑定规则冲突时，按以下优先级执行：

1. **`new` 绑定**（最高）
2. **`call` / `apply` / `bind` 绑定**
3. **隐式绑定**
4. **默认绑定**

```js
function foo() {
  console.log(this.name);
}

const obj1 = { name: "obj1", foo };
const obj2 = { name: "obj2" };

obj1.foo.call(obj2); // "obj2"（显示绑定优先于隐式绑定）

const boundFoo = foo.bind(obj1);
boundFoo.call(obj2); // "obj1"（bind 绑定无法被 call / apply 覆盖）
```

---

## 🚨 **绑定例外**

### 🔹 **绑定对象为 `undefined` 或 `null`**

在 `call`、`apply` 或 `bind` 方法中，如果显式绑定的对象是 `null` 或 `undefined`，**默认会回退到全局对象**（在浏览器中是 `window`，在 Node.js 中是 `globalThis`）。

```js
function foo() {
  console.log(this);
}

foo.call(null); // 在非严格模式下：window / globalThis
foo.call(undefined); // 在非严格模式下：window / globalThis
```

#### **严格模式下**

在 `use strict` 模式下，绑定 `null` 或 `undefined` 会保持 `this` 为 `null` 或 `undefined`，不会回退到全局对象。

```js
"use strict";
function foo() {
  console.log(this);
}

foo.call(null); // null
foo.call(undefined); // undefined
```

---

### 🔹 **间接引用（Implicit Reference）**

**间接引用**指的是当属性访问的结果是一个对象的方法时，**`this` 绑定的是该方法所属的对象**，但**如果赋值给一个变量后调用，`this` 可能会丢失**。

```js
const obj = {
  name: "Alice",
  getName() {
    console.log(this.name);
  }
};

// 简介引用：`this` 绑定到 `obj`
obj.getName(); // "Alice"

const fn = obj.getName; // `fn` 赋值后丢失 `this` 绑定
fn(); // `this` 变成默认绑定（非严格模式下是 `window`，严格模式下是 `undefined`）
```

---

### 🔹 **软绑定（Soft Binding）**

软绑定（Soft Binding）是一种在 `this` 绑定丢失时**提供默认对象**的方法，防止 `this` 变成 `undefined` 或 `window`。

通常，我们可以通过**封装一个自定义 `bind` 方法**来实现软绑定：

```js
if (!Function.prototype.softBind) {
  Function.prototype.softBind = function (obj) {
    const fn = this;
    const boundArgs = Array.prototype.slice.call(arguments, 1);
    
    return function () {
      return fn.apply(
        (!this || this === window || this === globalThis) ? obj : this,
        boundArgs.concat(Array.prototype.slice.call(arguments))
      );
    };
  };
}
```

#### **软绑定的作用**

1. 如果 `this` 被默认绑定（如独立调用），则使用提供的默认对象。
2. 如果 `this` 是一个具体的对象，则不影响。

#### **示例**

```js
function greet() {
  console.log(this.name);
}

const defaultObj = { name: "Default" };
const person = { name: "John" };

const boundGreet = greet.softBind(defaultObj);

boundGreet(); // "Default"（因为 `this` 绑定丢失）
boundGreet.call(person); // "John"（因为 `this` 绑定到 `person`）
```

#### **软绑定 vs `bind`**

- `bind()` 会创建一个**永久绑定**，无论怎么调用 `this` 都不会改变。
- 软绑定**只在 `this` 绑定丢失时**提供默认值，不会覆盖已有绑定。

---

## 🎯 **箭头函数中的 `this`（词法作用域）**

箭头函数的 `this` 取决于**定义位置**，不会因调用方式改变。

```js
function outer() {
  return () => {
    console.log(this);
  };
}

const obj = { method: outer() };
obj.method(); // outer() 的 `this` 绑定于定义时的作用域，而非 obj
```

✅ **常见用途**：避免 `this` 丢失

```js
function Timer() {
  this.seconds = 0;
  setInterval(() => {
    this.seconds++;
    console.log(this.seconds);
  }, 1000);
}

new Timer(); // `this` 绑定 Timer 实例，而不是 `setInterval` 内部
```

---

## ✅ **总结**

| 绑定方式 | `this` 指向 |
|----------|------------|
| **默认绑定** | `window`（非严格）/ `undefined`（严格模式） |
| **隐式绑定** | 调用它的对象 |
| **显示绑定**（`call` / `apply` / `bind`） | 绑定的对象 |
| **`new` 绑定** | 新创建的对象 |
| **箭头函数** | 继承外部作用域的 `this`（词法绑定） |

🔹 **优先级：`new` 绑定 > `call` / `apply` / `bind` > 隐式绑定 > 默认绑定**  
🔹 **箭头函数不绑定 `this`，它继承外层作用域的 `this`**  
