title: React 16 新特性
author: John Doe
date: 2019-12-17 08:28:14
tags:
---
###  useEffect, useState, useCallback,Suspense

```js
import React, { useState, useCallback, Suspense } from "react";
import ReactDOM from "react-dom";
import "./styles.css";

import EmployeesList from "./EmployeesList";

import debounce from "debounce";
import { employeesResource } from "./fake-resource";

const initialResource = employeesResource("");

function EmployeesPage({ resource }) {
  return (
    <Suspense fallback={<div>Fetching employees....</div>}>
      <EmployeesFetch resource={resource} />
    </Suspense>
  );
}

function EmployeesFetch({ resource }) {
  const employees = resource.employees.read();
  return <EmployeesList employees={employees} />;
}

function App() {
  const [resource, setResource] = useState(initialResource);
  const setResourceByQuery = useCallback(
    debounce(query => setResource(employeesResource(query)), 500),
    []
  );
  return (
    <div className="App">
      <h2>Employees list</h2>
      <label>Filter:</label>
      <input
        type="text"
        onChange={event => setResourceByQuery(event.target.value)}
        placeholder="Type query..."
      />
      <EmployeesPage resource={resource} />
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

```
总结：

useEffect的执行时机都是每次渲染后触发，无论是首次渲染还是更新渲染。
useEffect只会有当然组件是函数组件才会执行，不能再非函数组件中使用。
useEffect可以在同一函数组件中使用多次，按调用顺序执行，这样我们可以将生命周期中需要做的事情更小粒度的去编写代码。
在useEffect传入的函数中，return一个函数，用作函数组件卸载时需要执行的操作。
控制useEffect什么时候执行可以传入第二参数，而且第二个参数必须是数组！react会对这次传入的数组中的每一项和上一次数组中的每一项进行对比，当发现不一样时会做对应记录，在渲染后就会触发对应符合触发的useEffect函数。
useEffect的触发是采用异步方式执行的。因为有可能存在多个useEffect的函数，如果像class组件那样在commit阶段最后触发的话，很容易导致阻塞线程。所以React利用setTimeout的方式，将useEffect异步执行。