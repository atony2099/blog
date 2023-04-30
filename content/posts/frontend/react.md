---
title:  react
date: 2023-04-27T00:13:24+08:00
lastmod: 2023-04-27T00:13:24+08:00
categories: ["frontend"]
tags: ["react"]
---

[Passing Props to a Component – React - https://react.dev/learn/passing-props-to-a-component](https://react.dev/learn/passing-props-to-a-component)


## basic concept

props:  
1. info pass from parent 
2. info pass by jsx tag 
3. read from function({xx,xxx})
4. read only









## simple example
```jsx

import React, { useEffect, useState } from "react";
import ReactDOM from "react-dom";

import "./styles.css";

function App() {
  const [users, setUsers] = useState();

  // Function to collect data
  const getApiData = async () => {
    const response = await fetch(
      "https://jsonplaceholder.typicode.com/todos/"
    ).then((response) => response.json());

    setUsers(response);
  };

  useEffect(() => {
    getApiData();
  }, []);

  return (
    <div className="app">
      {users &&
        users.map((user) => (
          <div className="item-container">
            Id:{user.id} <div className="title">Title:{user.title}</div>
          </div>
        ))}
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```


### use effect:
```jsx
useEffect(() => {
  // side effect code here

  return () => {
    // cleanup code here
  };
}, [dependencies]);
```

执行副作用

when:
1. after first render 
2. the dependencies have changed



warning: has missing dependencies:
依赖了外部 variable/function  却没有加入依赖之中

solve:
1. 添加依赖
2. 将依赖放入 effect 方法中






export  