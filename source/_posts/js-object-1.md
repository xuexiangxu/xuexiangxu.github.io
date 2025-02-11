---
title: JavaScript中的对象
date: 2025-02-11 18:55:59
tags: [JavaScript]
categories:
- [JavaScript]
---

## 1. 语法

JavaScript 中的对象可以通过两种方式定义：声明形式和构造形式。

**声明形式：**

```js
var myObj = {
  key: value
  // ...
};
```

**构造形式：**

```js
var myObj = new Object();
myObj.key = value;
```

## 2. 类型

JavaScript 中的主要数据类型包括：

- `string`
- `number`
- `boolean`
- `null`
- `undefined`
- `object`

**注意：**

- **基本类型**（string、number、boolean、null、undefined）本身不是对象。
- `null` 不是对象，但 `typeof null` 返回 "object"，这是 JavaScript 的历史遗留 bug。
- JavaScript 中还有许多特殊的对象子类型，可以称为**复杂基本类型**。

### 内置对象

JavaScript 提供了一些内置对象，它们类似于其他语言中的类（class），但实际上只是构造函数：

- `String`
- `Number`
- `Boolean`
- `Object`
- `Function`
- `Array`
- `Date`
- `RegExp`
- `Error`

## 3. 对象的内容

对象由**属性**组成，每个属性都是一个键值对，键是字符串，值可以是任何类型。

```js
var myObject = {
  a: 2
};

console.log(myObject.a); // 2
console.log(myObject["a"]); // 2
```

- `.` 访问方式称为**属性访问**。
- `[]` 访问方式称为**键访问**。

### 3.1 可计算属性名

ES6 允许在对象字面量中使用计算属性名。

```js
var key = "name";
var myObject = {
  [key]: "John"
};

console.log(myObject.name); // "John"
```

### 3.2 属性与方法

对象的属性可以存储函数，但函数本身仍然只是一个值。

```js
var myObject = {
  greet: function() {
    return "Hello!";
  }
};

console.log(myObject.greet()); // "Hello!"
```

### 3.3 数组

数组也是对象，虽然索引通常是整数，但仍然可以添加自定义属性。

```js
var arr = [1, 2, 3];
arr.customProperty = "Hello";

console.log(arr.length); // 3
console.log(arr.customProperty); // "Hello"
```

**注意：**
如果向数组添加一个看起来像数字的属性，它会被转换为数值下标，而不是普通属性。

```js
var arr = [];
arr["2"] = "hello";
console.log(arr[2]); // "hello" (变成了数组索引)
```

### 3.4 复制对象

对象的复制可以分为**浅复制**和**深复制**。

#### **浅复制**

浅复制只复制对象的第一层属性，嵌套对象仍然共享引用。

```js
var obj1 = { a: 1, b: { c: 2 } };
var obj2 = Object.assign({}, obj1);

obj2.b.c = 42;
console.log(obj1.b.c); // 42
```

#### **深复制**

深复制创建对象的完全独立副本。

**方法 1：使用 JSON**（不适用于包含函数的对象）

```js
var obj1 = { a: 1, b: { c: 2 } };
var obj2 = JSON.parse(JSON.stringify(obj1));

obj2.b.c = 42;
console.log(obj1.b.c); // 2
```

**方法 2：使用递归手动复制**

```js
function deepClone(obj) {
  if (typeof obj !== "object" || obj === null) return obj;
  let newObj = Array.isArray(obj) ? [] : {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      newObj[key] = deepClone(obj[key]);
    }
  }
  return newObj;
}
```

**方法 3：使用 `structuredClone`**（现代浏览器支持）

```js
var obj1 = { a: 1, b: { c: 2 } };
var obj2 = structuredClone(obj1);

obj2.b.c = 42;
console.log(obj1.b.c); // 2
```

### 3.5 属性描述符

JavaScript 允许通过 `Object.defineProperty` 控制对象的属性行为。

- **Writable**：属性是否可以被修改。
- **Configurable**：属性是否可以被删除或重新定义。
- **Enumerable**：属性是否可被枚举（遍历）。

```js
var obj = {};
Object.defineProperty(obj, "a", {
  value: 42,
  writable: false,
  configurable: false,
  enumerable: true
});
console.log(obj.a); // 42
```

### 3.6 不变性

JavaScript 提供了不同级别的对象不变性：

- **对象常量**：使用 `const` 变量名不能变，但对象内容可变。
- **禁止扩展**：`Object.preventExtensions(obj)` 阻止新增属性。
- **密封**：`Object.seal(obj)` 使对象不可扩展，且现有属性不可删除。
- **冻结**：`Object.freeze(obj)` 使对象不可扩展，属性不可修改或删除。

### 3.7 Getter 和 Setter

对象默认的`[[Put]]`和`[[Get]]`操作分别可以控制属性值的设置和获取。
在ES5中可以使用`getter`和`setter`部分改写默认操作，但是只能应用在单个属性上，无法应用在整个对象上

```js
var obj = {
  _name: "John",
  get name() {
    return this._name;
  },
  set name(value) {
    this._name = value;
  }
};
console.log(obj.name); // "John"
obj.name = "Doe";
console.log(obj.name); // "Doe"
```

### 3.8 存在性

检查对象是否具有某个属性：

```js
var obj = { a: 1 };
console.log("a" in obj); // true
console.log(obj.hasOwnProperty("a")); // true
console.log(obj.a !== undefined); // true
```

