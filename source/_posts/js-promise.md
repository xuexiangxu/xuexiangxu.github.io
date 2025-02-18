---
title: 手写Promise
date: 2025-02-18 15:12:42
tags: [JavaScript]
categories:
- [JavaScript]
---

## 需求

- `promise`有三个状态：`pending`，`fulfilled`，`rejected`
- `new Promise`时， 需要传递一个`executor()`执行器，执行器立即执行；
- `executor`接受两个参数，分别是`resolve`和`reject`；
- `promise`的默认状态是`pending`；
- `promise`有一个`value`保存成功状态的值，可以是undefined/thenable/promise
- `promise`有一个`reason`保存失败状态的值
- `promise`只能从`pending`到`rejected`, 或者从`pending`到`fulfilled`，状态一旦确认，就不会再改变；
- `promise`必须有一个`then`方法，`then`接收两个参数，分别是`promise`成功的回调`onFulfilled`, 和 `promise` 失败的回调`onRejected`
- 如果调用`then`时，`promise` 已经成功，则执行`onFulfilled`，参数是`promise`的`value`；
- 如果调用`then`时，`promise` 已经失败，那么执行`onRejected`, 参数是`promise`的`reason`；
- 如果`then`中抛出了异常，那么就会把这个异常作为参数，传递给下一个`then`的失败的回调`onRejected`；

## 实现

```js
class MyPromise {
  constructor(executor) {
    this.state = 'pending';
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledCallbacks = [];
    this.onRejectedCallbacks = [];

    const resolve = (value) => {
      if (this.state === 'pending') {
        this.state = 'fulfilled';
        this.value = value;
        this.onFulfilledCallbacks.forEach((cb) => cb(value));
      }
    };

    const reject = (reason) => {
      if (this.state === 'pending') {
        this.state = 'rejected';
        this.reason = reason;
        this.onRejectedCallbacks.forEach((cb) => cb(reason));
      }
    };

    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }

  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : (v) => v;
    onRejected = typeof onRejected === 'function' ? onRejected : (err) => { throw err; };

    return new MyPromise((resolve, reject) => {
      if (this.state === 'fulfilled') {
        queueMicrotask(() => {
          try {
            const x = onFulfilled(this.value);
            resolvePromise(x, resolve, reject);
          } catch (error) {
            reject(error);
          }
        });
      } else if (this.state === 'rejected') {
        queueMicrotask(() => {
          try {
            const x = onRejected(this.reason);
            resolvePromise(x, resolve, reject);
          } catch (error) {
            reject(error);
          }
        });
      } else {
        this.onFulfilledCallbacks.push(() => {
          queueMicrotask(() => {
            try {
              const x = onFulfilled(this.value);
              resolvePromise(x, resolve, reject);
            } catch (error) {
              reject(error);
            }
          });
        });
        this.onRejectedCallbacks.push(() => {
          queueMicrotask(() => {
            try {
              const x = onRejected(this.reason);
              resolvePromise(x, resolve, reject);
            } catch (error) {
              reject(error);
            }
          });
        });
      }
    });
  }

  catch(onRejected) {
    return this.then(null, onRejected);
  }

  static resolve(value) {
    return new MyPromise((resolve) => resolve(value));
  }

  static reject(reason) {
    return new MyPromise((_, reject) => reject(reason));
  }
}

function resolvePromise(x, resolve, reject) {
  if (x instanceof MyPromise) {
    x.then(resolve, reject);
  } else {
    resolve(x);
  }
}
```

## 测试用例

```js
const promise = new MyPromise((resolve, reject) => {
  setTimeout(() => resolve("Hello, Promise!"), 1000);
});

promise.then((value) => {
  console.log(value);
  return "Next Step";
}).then((value) => {
  console.log(value);
});
```
