title: react hooks
author: Steady
date: 2020-08-25 15:25:12
tags:
---
# 1.userContext123

关于React Hooks中使用useContext 进行父子组件传值（父子组件不在同一个文件中）
1、封装的公共文件
// createContext.js文件
```js
import { createContext } from "react";
const myContext = createContext(null);
export default myContext;
```

2、父组件
```js
import React, { useState} from "react";
import Counter from './Counter'
import myContext from './createContext'

function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h4>我是父组件</h4>
      <p>点击了 {count} 次!</p>
      <button
        onClick={() => {
          setCount(count + 1);
        }}
      >
        点我
      </button>

      {/* 关键代码 */}
      {/* 提供器 */}
      <myContext.Provider value={count}>
        <Counter />
      </myContext.Provider>
    </div>
  );
}
export default App;
```

3、子组件
```js
import React, { useContext} from 'react';
import myContext from './createContext'

// 关键代码
function Counter() {
    const count = useContext(myContext);  // 得到父组件传的值
    return (
        <div>
            <h4>我是子组件</h4>
            <p>这是父组件传过来的值：{count}</p>
        </div>
    )
}

export default Counter;
```

# 2.useCallback
它会返回相同的引用，避免子组件进行无意义的重复渲染：
```js
function Foo() {
  const [count, setCount] = useState(0);

  const memoizedHandleClick = useCallback(
    () => console.log(`Click happened with dependency: ${count}`), [count],
  ); 
  return <Button onClick={memoizedHandleClick}>Click Me</Button>;
}
```

# 3.useMemo 、 React.memo
useCallback缓存的是方法的引用，而useMemo缓存的则是方法的返回值。使用场景是减少不必要的子组件渲染：
```js
function Parent({ a, b }) {
  // 当 a 改变时才会重新渲染
  const child1 = useMemo(() => <Child1 a={a} />, [a]);
  // 当 b 改变时才会重新渲染
  const child2 = useMemo(() => <Child2 b={b} />, [b]);
  return (
    <>
      {child1}
      {child2}
    </>
  )
}
```

如果想实现Class Component的shouldComponentUpdate方法，可以使用React.memo方法，区别是它只能比较 props，不会比较 state：
```js
const Parent = React.memo(({ a, b }) => {
  // 当 a 改变时才会重新渲染
  const child1 = useMemo(() => <Child1 a={a} />, [a]);
  // 当 b 改变时才会重新渲染
  const child2 = useMemo(() => <Child2 b={b} />, [b]);
  return (
    <>
      {child1}
      {child2}
    </>
  )
});
```

# 封装自定义 usePrevious

```js
const usePrevious = (value) => {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  }, [value]);
  return ref.current;
}

function Counter() {
  const [count, setCount] = useState(0);

  const prevCountRef = usePrevious(count);
  console.log(0);
  // useEffect(() => {
  //   console.log(1);
  //   prevCountRef.current = count;
  // });
  const prevCount = prevCountRef;

  return (
    <div>
      <h1>
        Now: {count}, before: {prevCount}
      </h1>
      <button onClick={() => setCount(count + 1)}>click here</button>
    </div>
  );
}
```