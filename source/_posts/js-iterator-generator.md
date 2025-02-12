---
title: JavaScript中的迭代器和生成器
date: 2025-02-12 12:11:17
tags: [JavaScript]
categories:
- [JavaScript]
---

在 JavaScript 中，**迭代器（Iterator）** 和 **生成器（Generator）** 是用于 **遍历数据结构** 和 **实现惰性计算** 的强大工具。它们与 `for...of`、`Symbol.iterator` 以及 `yield` 关键字密切相关，可以帮助我们更高效地处理 **流数据**、**自定义遍历** 和 **异步任务**。  

---

## **1. 迭代器（Iterator）**

### **1.1 什么是迭代器？**

迭代器（Iterator）是一种 **按顺序访问集合元素** 的方式，但不暴露其底层实现。它提供了一个 `next()` 方法，每次调用 `next()` 都会返回一个对象：

- `{ value: 数据, done: false }` —— **表示当前值和未完成的状态**
- `{ value: undefined, done: true }` —— **表示迭代结束**

### **1.2 迭代器的基本实现**

迭代器本质上是一个 **实现了 `next()` 方法的对象**，例如：

```javascript
function createIterator(array) {
    let index = 0;
    return {
        next: function() {
            if (index < array.length) {
                return { value: array[index++], done: false };
            } else {
                return { value: undefined, done: true };
            }
        }
    };
}

// 使用迭代器
const iterator = createIterator(['apple', 'banana', 'cherry']);
console.log(iterator.next()); // { value: 'apple', done: false }
console.log(iterator.next()); // { value: 'banana', done: false }
console.log(iterator.next()); // { value: 'cherry', done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

**特点**

- 迭代器不会自动迭代，而是需要手动调用 `next()`。
- 需要手动维护 `index` 变量。

---

### **1.3 `Symbol.iterator` 和可迭代对象**

如果对象 **实现了 `Symbol.iterator` 方法**，它就是 **可迭代对象（Iterable）**，可以被 `for...of`、`Array.from()` 这样的迭代方法使用。

#### **1.3.1 自定义可迭代对象**

```javascript
const iterable = {
    data: ['one', 'two', 'three'],
    [Symbol.iterator]: function() {
        let index = 0;
        return {
            next: () => {
                return index < this.data.length
                    ? { value: this.data[index++], done: false }
                    : { value: undefined, done: true };
            }
        };
    }
};

// 通过 for...of 进行迭代
for (const item of iterable) {
    console.log(item); // one, two, three
}
```

**注意**

- `Symbol.iterator` 返回的是一个 **迭代器对象**，这个对象必须实现 `next()` 方法。
- `for...of` 会自动调用 `next()`，无需手动调用。

---

### **1.4 JavaScript 内置可迭代对象**

**默认实现 `Symbol.iterator` 的数据结构**

- **数组（Array）**
- **字符串（String）**
- **Map 和 Set**
- **TypedArray**
- **函数参数对象（arguments）**
- **NodeList（部分浏览器实现）**

```javascript
const str = "Hello";
const iter = str[Symbol.iterator]();

console.log(iter.next()); // { value: 'H', done: false }
console.log(iter.next()); // { value: 'e', done: false }
```

---

## **2. 生成器（Generator）**

### **2.1 什么是生成器？**

生成器（Generator）是一种特殊的函数，它可以 **暂停和恢复执行**，使用 `function*` 关键字定义，并通过 `yield` 关键字返回多个值。

**特点**

- **调用生成器函数不会立即执行，而是返回一个迭代器**。
- **调用 `next()` 时，执行到 `yield` 语句并暂停**，下次调用时从暂停的地方继续。
- **`yield` 返回的是一个值，而不是一个完整的对象**。

### **2.2 生成器的基本使用**

```javascript
function* myGenerator() {
    yield 'A';
    yield 'B';
    yield 'C';
}

const gen = myGenerator();
console.log(gen.next()); // { value: 'A', done: false }
console.log(gen.next()); // { value: 'B', done: false }
console.log(gen.next()); // { value: 'C', done: false }
console.log(gen.next()); // { value: undefined, done: true }
```

**特点**

- `yield` 使函数暂停执行，下次 `next()` 继续执行。
- 生成器返回的是一个 **迭代器对象**。

---

### **2.3 `for...of` 遍历生成器**

生成器本质上是一个 **可迭代对象**，可以用 `for...of` 遍历：

```javascript
function* countUp() {
    yield 1;
    yield 2;
    yield 3;
}

for (const value of countUp()) {
    console.log(value); // 1, 2, 3
}
```

**优点**

- `for...of` **自动处理 `done` 状态**，不需要手动调用 `next()`。

---

### **2.4 生成器的应用**

#### **2.4.1 生成无限序列**

```javascript
function* infiniteNumbers() {
    let num = 1;
    while (true) {
        yield num++;
    }
}

const infiniteGen = infiniteNumbers();
console.log(infiniteGen.next().value); // 1
console.log(infiniteGen.next().value); // 2
console.log(infiniteGen.next().value); // 3
```

**适用场景**

- 生成 **无限序列**（如斐波那契数列）。
- 按需生成数据，节省内存。

#### **2.4.2 斐波那契数列**

```javascript
function* fibonacci() {
    let [prev, curr] = [0, 1];
    while (true) {
        yield curr;
        [prev, curr] = [curr, prev + curr];
    }
}

const fib = fibonacci();
console.log(fib.next().value); // 1
console.log(fib.next().value); // 1
console.log(fib.next().value); // 2
console.log(fib.next().value); // 3
```

---

## **3. 迭代器 vs 生成器**

| **特点**      | **迭代器（Iterator）** | **生成器（Generator）** |
|--------------|----------------------|----------------------|
| **定义方式**  | 手动实现 `next()` | 使用 `function*` |
| **状态维护**  | 需要手动维护索引 | `yield` 自动管理状态 |
| **惰性计算**  | 需要手动控制 | 默认支持 |
| **代码简洁度** | 代码较繁琐 | 代码更简洁 |

---

## **4. 结论**

- **迭代器适用于** 自定义遍历逻辑，需要手动维护 `next()` 。
- **生成器适用于** 惰性计算、数据流处理，代码更简洁。
- **`Symbol.iterator` 使对象可迭代**，结合 `for...of` 提供更优雅的遍历方式。
