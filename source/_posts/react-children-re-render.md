---
title: React Component - 子组件什么时候会re-render
date: 2023-08-04 21:10:09
tags: [React]
categories:
- [React]
---
https://whereisthemouse.com/react-components-when-do-children-re-render

React children什么时候re-render？
答案似乎很简单：
- 它们的props/state发生改变
- 它们的parent component re-render

然而，还有更多

## 通常的情况
当我们点击ParentComponent的Button时，ChildComponent会re-render吗？
```javascript
import { useState } from 'react';

const ChildComponent = () => {
  console.log('I re-rendered');
  return <p>Child</p>;
};

const ParentComponent = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(number + 1)}>{count}</button>
      <ChildComponent />
    </div>
  );
};

export default ParentComponent;
```
当然会的！
但是，ChildComponent实在是不必要去re-render。那么该怎么做？
## 方法一 React.memo
```javascript
import { memo } from 'React';

const ChildComponent = memo(() => {
  console.log('I re-rendered');
  return <p>Child</p>;
});
```

## 方法二 children as a prop
```javascript
const ChildComponent = () => {
  console.log('I re-rendered');
  return <p>Child</p>;
};

const ParentComponent = ({ children }) => {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(number + 1)}>{number}</button>
      {children}
    </div>
  );
};

const App = () => {
  return (
    <ParentComponent>
      <ChildComponent />
    </ParentComponent>
  );
};

export default App;
```

## 应用 - React Context
假设我们有一个非常重的React subtree，我们不想经常re-render它
下面代码，在每一次点击按钮都会导致两个Child组件re-render
```javascript
import { useState, useContext, createContext } from 'react';

const Context = createContext();

const ChildWithCount = () => {
  const { count, setCount } = useContext(Context);
  console.log('ChildWithCount re-renders');
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <p>Child</p>
    </div>
  );
};

const ExpensiveChild = () => {
  console.log('ExpensiveChild re-renders');
  return <p>Expensive child</p>;
};

const CountContext = () => {
  const [count, setCount] = useState(0);
  const contextValue = { count, setCount };
  return (
    <Context.Provider value={contextValue}>
      <ChildWithCount />
      <ExpensiveChild /> // Imagine re-rendering this component is expensive
    </Context.Provider>
  );
};

const App = () => {
  return <CountContext />;
};

export default App;
```

我们可以利用之前所提到的方法改变它们的结构，把children作为一个prop，从而避免无关的同级组件re-render
```javascript
import { useState, useContext, createContext } from 'react';

const Context = createContext();

const ChildWithCount = () => {
  const { count, setCount } = useContext(Context);
  console.log('ChildWithCount re-renders');
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <p>Child</p>
    </div>
  );
};

const ExpensiveChild = () => {
  console.log('ExpensiveChild re-renders');
  return <p>Expensive child</p>;
};

const CountContext = ({ children }) => {
  const [count, setCount] = useState(0);
  const contextValue = { count, setCount };
  return <Context.Provider value={contextValue}>{children}</Context.Provider>;
};

const App = () => {
  return (
    <CountContext>
      <ChildWithCount />
      <ExpensiveChild />
    </CountContext>
  );
};

export default App;
```