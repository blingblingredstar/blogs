---
title: Intermediate React v2
date: 2019-08-08 16:46:08
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, React, Jest, TypeScript, Hooks, Redux, SSR]
---
## Introduction

[note](https://btholt.github.io/complete-intro-to-react-v5/)

clone the [repo](https://github.com/btholt/complete-intro-to-react-v5)

### Course Repository Tour

`cd ./complete-intro-to-react-v5/`

`npm install`

`npm run dev`
<!-- more -->
## Basic Hooks

### useState

[hooks in depth](https://btholt.github.io/complete-intro-to-react-v5/hooks-in-depth/)

Here's a link to this [Codesandbox](https://codesandbox.io/s/zr90v4jorp)
<!-- markdownlint-disable md033 -->

`useState` allows us to make our components stateful. Whereas this previously required using a class component, hooks give us the ability to write it using just functions. It allows us to have more flexible components. In our example component, everytime you click on the h1 (bad a11y, by the way) it'll change colors. It's doing this by keeping that bit of state in a hook which is being fed in a new every render so it always has the latest state.

<details>
<summary>State.js</summary>

```jsx
import React, { useState } from "react";

const StateComponent = () => {
  const [isGreen, setIsGreen] = useState(true);

  return (
    <h1
      onClick={() => setIsGreen(!isGreen)}
      style={{ color: isGreen ? "limegreen" : "crimson" }}
    >
      useState Example
    </h1>
  );
};

export default StateComponent;
```

</details>

### useEffect

Effects are how you recreate `componentDidMount`, `componentDidUpdate`, and `componentDidUnmount` from React. Inside `useEffect`, you can do any sort of sidd-effect type action that you would have previously done in one of React's lifecycle method. You can do things like fire AJAX requests, integrate with third party libraries (like a jQuery plugin), fire off some telemetry, or anything else that need to happen on the side for your component.

In our case, we want our component to continually update to show the time so we use `setTimeout` inside our effect. After the timeout calls the callback, it updates the state. After that render happens, it schedules another effect to happen, hence why it continues to update. You could provide a second parameter of [] to `useEffect` (after the function) which would make it only update once. This second array is a list of dependencies: only re-run this effect if one of these parameters changed. In our case, we want to run after every render so we don't give it this second parameter.

<details>
<summary>Effect.js</summary>

```jsx
import React, { useState, useEffect } from "react";

const EffectComponent = () => {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    const timer = setTimeout(setTime(new Date()), 1000);
    return () => clearTimeout(timer);
  });

  return <h1>useEffect Example: {time.toLocaleTimeString()}</h1>;
};

export default EffectComponent;
```

</details>

### useContext

An early problem with the React problem is called "data tunneling" or "prop drilling". This is when you have a top level component (in our case the parent component) and a child component way down in the hierarchy that need the same data (like the user object.) We could pass that data down, parent-to-child, for each of the intermediary components but that sucks because now each of LevelTwo, LevelThree, and LevelFour all have to know about the user object even when they themselves don't need it, just their children. This is prop drilling: passing down this data in unnecessary intermediaries.

Enter context. Context allows you to create a wormhole where stuff goes in and a wormhole in a child component where that same data comes out and the stuff in the middle doesn't know it's there. Now that data is available anywhere inside of the UserContext.Provider. useContext just pulls that data out when given a Context object as a parameter. You don't have to use useState and useContext together (the data can be any shape, not just useState-shaped) but I find it convenient when child components need to be able to update the context as well.

In general, context adds a decent amount of complexity to an app. A bit of prop drilling is fine. Only put things in context that are truly application-wide state like user information or auth keys and then use local state for the rest.

Often you'll use context instead of Redux or another state store. You could get fancy and use useReducer and useContext together to get a pretty great approximation of Redux-like features.

<details>
<summary>Context.js</summary>

```jsx
import React, { useState, useContext, createContext } from "react";

const UserContext = createContext([
  {
    firstName: "Bob",
    lastName: "Bobberson",
    suffix: 1,
    email: "bobbobberson@example.com"
  },
  obj => obj
]);

const LevelFive = () => {
  const [user, setUser] = useContext(UserContext);

  return (
    <div>
      <h5>{`${user.firstName} ${user.lastName} the ${user.suffix} born`}</h5>
      <button
        onClick={() => {
          setUser(Object.assign({}, user, { suffix: user.suffix + 1 }));
        }}
      >
        Increment
      </button>
    </div>
  );
};

const LevelFour = () => (
  <div>
    <h4>fourth level</h4>
    <LevelFive />
  </div>
);

const LevelThree = () => (
  <div>
    <h3>third level</h3>
    <LevelFour />
  </div>
);

const LevelTwo = () => (
  <div>
    <h2>second level</h2>
    <LevelThree />
  </div>
);

const ContextComponent = () => {
  const userState = useState({
    firstName: "James",
    lastName: "Jameson",
    suffix: 1,
    email: "jamesjameson@example.com"
  });

  return (
    <UserContext.Provider value={userState}>
      <h1>first level</h1>
      <LevelTwo />
    </UserContext.Provider>
  );
};

export default ContextComponent;
```

</details>

## Hooks in-Depth

### useRef

Refs are useful for several things, we'll explore two of the main reasons in these examples. I want to show you the first use case: how to emulate instance variables from React.

In order to understand why refs are useful, you need to understand how closures work. In our component, when a user clicks, it sets a timeout to log both the state and the ref's number after a second. One thing to keep in mind that the state and the ref's number are always the same. They are never out of lockstep since they're updated at the same time. However, since we delay the logging for a second, when it alerts the new values, it will capture what the state was when we first called the timeout (since it's held on to by the closure) but it will always log the current value since that ref is on an object that React consistently gives the same object back to you. Because it's the same object and the number is a property on the object, it will always be up to date and not subject to the closure's scope.

Why is this useful? It can be useful for things like holding on to setInterval and setTimeout IDs so they can be cleared later. Or any bit of statefulness that could change but you don't want it to cause a re-render when it does.

It's also useful for referencing DOM nodes directly and we'll see that a bit later in this section.

<details>
<summary>Ref.js</summary>

```jsx
import React, { useState, useEffect, useRef } from "react";

const RefComponent = () => {
  const [stateNumber, setStateNumber] = useState(0);
  const numRef = useRef(0);

  function incrementAndDelayLogging() {
    setStateNumber(stateNumber + 1);
    numRef.current++;
    setTimeout(
      () => alert(`state: ${stateNumber} | ref: ${numRef.current}`),
      1000
    );
  }

  return (
    <div>
      <h1>useRef Example</h1>
      <button onClick={incrementAndDelayLogging}>delay logging</button>
      <h4>state: {stateNumber}</h4>
      <h4>ref: {numRef.current}</h4>
    </div>
  );
};

export default RefComponent;
```

</details>

### useReducer

I'm going to assume you're familiar with Redux. If not, there's a brief section on it [here](https://btholt.github.io/complete-intro-to-react-v5/redux). useReducer allows us to do Redux-style reducers but inside a hook. Here, instead of having a bunch of functions to update our various properties, we have one reducer that handles all the updates based on an action type. This is a preferable approach if you have complex state updates or if you have a situation like this: all of the state updates are very similar so it makes sense to contain all of them in one function.

<details>
<summary>Reducer.js</summary>

```jsx
import React, { useReducer } from "react";

// fancy logic to make sure the number is between 0 and 255
const limitRGB = num => (num < 0 ? 0 : num > 255 ? 255 : num);

const step = 50;

const reducer = (state, action) => {
  switch (action.type) {
    case "INCREMENT_R":
      return Object.assign({}, state, { r: limitRGB(state.r + step) });
    case "DECREMENT_R":
      return Object.assign({}, state, { r: limitRGB(state.r - step) });
    case "INCREMENT_G":
      return Object.assign({}, state, { g: limitRGB(state.g + step) });
    case "DECREMENT_G":
      return Object.assign({}, state, { g: limitRGB(state.g - step) });
    case "INCREMENT_B":
      return Object.assign({}, state, { b: limitRGB(state.b + step) });
    case "DECREMENT_B":
      return Object.assign({}, state, { b: limitRGB(state.b - step) });
    default:
      return state;
  }
};

const ReducerComponent = () => {
  const [{ r, g, b }, dispatch] = useReducer(reducer, { r: 0, g: 0, b: 0 });

  return (
    <div>
      <h1 style={{ color: `rgb(${r}, ${g}, ${b})` }}>useReducer Example</h1>
      <div>
        <span>r</span>
        <button onClick={() => dispatch({ type: "INCREMENT_R" })}>➕</button>
        <button onClick={() => dispatch({ type: "DECREMENT_R" })}>➖</button>
      </div>
      <div>
        <span>g</span>
        <button onClick={() => dispatch({ type: "INCREMENT_G" })}>➕</button>
        <button onClick={() => dispatch({ type: "DECREMENT_G" })}>➖</button>
      </div>
      <div>
        <span>b</span>
        <button onClick={() => dispatch({ type: "INCREMENT_B" })}>➕</button>
        <button onClick={() => dispatch({ type: "DECREMENT_B" })}>➖</button>
      </div>
    </div>
  );
};

export default ReducerComponent;
```

</details>

### useMemo

`useMemo` and `useCallback` are performance optimizations. Use them only when you already have a performance problem instead of pre-emptively. It adds unnecessary complexity otherwise.

`useMemo` memoizes expensive function calls so they only are re-evaluated when needed. I put in the fibonacci sequence in its recursive style to simulate this. All you need to know is that once you're calling `fibonacci` with 30+ it gets quite computationally expensive and not something you want to do unnecessarily as it will cause pauses and jank. It will now only call `fibonacci` if count changes and will just the previous, memoized answer if it hasn't changed.

If we didn't have the `useMemo` call, everytime I clicked on the title to cause the color to change from red to green or vice versa it'd unnecessarily recalculate the answer of `fibonacci` but because we did use `useMemo` it will only calculate it when num has changed.

Feel try to remove `useMemo`, get num to 40 or so, and then click the h1. It'll be slow.

<details>
<summary>Memo.js</summary>

```jsx
import React, { useState, useMemo } from "react";

const fibonacci = n => {
  if (n <= 1) {
    return 1;
  }

  return fibonacci(n - 1) + fibonacci(n - 2);
};

const MemoComponent = () => {
  const [num, setNum] = useState(1);
  const [isGreen, setIsGreen] = useState(true);
  const fib = useMemo(() => fibonacci(num), [num]);

  return (
    <div>
      <h1
        onClick={() => setIsGreen(!isGreen)}
        style={{ color: isGreen ? "limegreen" : "crimson" }}
      >
        useMemo Example
      </h1>
      <h2>
        Fibonacci of {num} is {fib}
      </h2>
      <button onClick={() => setNum(num + 1)}>➕</button>
    </div>
  );
};

export default MemoComponent;
```

</details>

### useCallback

`useCallback` is quite similar and indeed it's implemented with the same mechanisms as `useMemo`. Our goal is that `ExpensiveComputationComponent` only re-renders whenever it absolutely must. Typically whenever React detects a change higher-up in an app, it re-renders everything underneath it. This normally isn't a big deal because React is quite fast at normal things. However you can run into performance issues sometimes where some components are bad to re-render without reason.

In this case, we're using a new feature of React called `React.memo`. This is similar to `PureComponent` where a component will do a simple check on its props to see if they've changed and if not it will not re-render this component (or its children, which can bite you.) `React.memo` provides this functionality for function components. Given that, we need to make sure that the function itself given to `ExpensiveComputationComponent` is the same function every time. We can use `useCallback` to make sure that React is handing the same fibonacci to `ExpensiveComputationComponent` every time so it passes its `React.memo` check every single time. Now it's only if count changes will it actually re-render (as evidenced by the time.)

Try removing the `useCallback` call and see if you get the the count to 40+ that the page crawls as it updates every second.

<details>
<summary>Callback.js</summary>

```jsx
import React, { useState, useEffect, useCallback, memo } from "react";

const ExpensiveComputationComponent = memo(({ compute, count }) => {
  return (
    <div>
      <h1>computed: {compute(count)}</h1>
      <h4>last re-render {new Date().toLocaleTimeString()}</h4>
    </div>
  );
});

const CallbackComponent = () => {
  const [time, setTime] = useState(new Date());
  const [count, setCount] = useState(1);
  useEffect(() => {
    const timer = setTimeout(setTime(new Date()), 1000);
    return () => clearTimeout(timer);
  });

  const fibonacci = n => {
    if (n <= 1) {
      return 1;
    }

    return fibonacci(n - 1) + fibonacci(n - 2);
  };

  return (
    <div>
      <h1>useCallback Example {time.toLocaleTimeString()}</h1>
      <button onClick={() => setCount(count + 1)}>
        current count: {count}
      </button>
      <ExpensiveComputationComponent
        compute={useCallback(fibonacci, [])}
        count={count}
      />
    </div>
  );
};

export default CallbackComponent;
```

</details>

### useLayoutEffect

`useLayoutEffect` is almost the same as `useEffect` except that it's synchronous to render as opposed to scheduled like `useEffect` is. If you're migrating from a class component to a hooks-using function component, this can be helpful too because `useLayout` runs at the same time as componentDidMount and componentDidUpdate whereas `useEffect` is scheduled after. This should be a temporary fix.

The only time you should be using `useLayoutEffect` is to measure DOM nodes for things like animations. In the example, I measure the textarea after every time you click on it (the onClick is to force a re-render.) This means you're running render twice but it's also necessary to be able to capture the correct measurments.

<details>
<summary>LayoutEffects.js</summary>

```jsx
import React, { useState, useLayoutEffect, useRef } from "react";

const LayoutEffectComponent = () => {
  const [width, setWidth] = useState(0);
  const [height, setHeight] = useState(0);
  const el = useRef();

  useLayoutEffect(() => {
    setWidth(el.current.clientWidth);
    setHeight(el.current.clientHeight);
  });

  return (
    <div>
      <h1>useLayoutEffect Example</h1>
      <h2>textarea width: {width}px</h2>
      <h2>textarea height: {height}px</h2>
      <textarea
        onClick={() => {
          setWidth(0);
        }}
        ref={el}
      />
    </div>
  );
};

export default LayoutEffectComponent;
```

</details>

### useImperativeHandle

Here's one you will likely never directly use but you may use libraries that use it for you. We're going to use it in conjunction with another feature called `forwardRef` that again, you probably won't use but libraries will use on your behalf. Let's explain first what it does using the example and then we'll explain the moving parts.

In the example above, whenever you have an invalid form, it will immediately focus the the first field that's invalid. If you look at the code, `ElaborateInput` is a child element so the parent component shouldn't have any access to the input contained inside the component. Those components are black boxes to their parents. All they can do is pass in props. So how do we accomplish it then?

The first thing we use is `useImperativeHandle`. This allows us to customize methods on an object that is made available to the parents via the `useRef` API. Inside `ElaborateInput` we have two refs: one thate is the one that will be provided by the parent, forwarded through by wrapping the `ElaborateInput` component in a forwardRef call which will ten provide that second ref parameter in the function call, and then the inputRef which is being used to directly access the DOM so we can call focus on the DOM node directly.

From the parent, we assign via `useRef` a ref to each of the ElaborateInputs which is then forwarded on each on via the forwardRef. Now, on these refs inside the parent component we have those methods that we made inside the child so we can call them when we need to. In this case, we'll calling the focus when the parent knows that the child has an error.

Again, you probably use this directly but it's good to know it exists. Normally it's better to not use this hook and try to accomplish the same thing via props but sometimes it may be useful to break this one out.

<details>
<summary>ImperativeHandle.js</summary>

```jsx
import React, {
  useState,
  useRef,
  useImperativeHandle,
  forwardRef
} from "react";

const ElaborateInput = forwardRef(
  ({ hasError, placeholder, value, update }, ref) => {
    const inputRef = useRef();
    useImperativeHandle(ref, () => {
      return {
        focus() {
          inputRef.current.focus();
        }
      };
    });
    return (
      <input
        ref={inputRef}
        value={value}
        onChange={e => update(e.target.value)}
        placeholder={placeholder}
        style={{
          padding: "5px 15px",
          borderWidth: "3px",
          borderStyle: "solid",
          borderColor: hasError ? "crimson" : "#999",
          borderRadius: "5px",
          margin: "0 10px",
          textAlign: "center"
        }}
      />
    );
  }
);

const ImperativeHandleComponent = () => {
  const [city, setCity] = useState("Seattle");
  const [state, setState] = useState("WA");
  const [error, setError] = useState("");
  const cityEl = useRef();
  const stateEl = useRef();

  function validate() {
    // lol I found it on StackOverflow : https://stackoverflow.com/a/25677072
    if (
      !/^([a-zA-Z\u0080-\u024F]+(?:. |-| |'))*[a-zA-Z\u0080-\u024F]+$/.test(
        city
      )
    ) {
      setError("city");
      cityEl.current.focus();
      return;
    }

    if (!/^[A-Z]{2}$/.test(state)) {
      setError("state");
      stateEl.current.focus();
      return;
    }

    setError("");
    alert("valid form!");
  }

  return (
    <div>
      <h1>useImperativeHandle Example</h1>
      <ElaborateInput
        hasError={error === "city"}
        placeholder={"City"}
        value={city}
        update={setCity}
        ref={cityEl}
      />
      <ElaborateInput
        hasError={error === "state"}
        placeholder={"State"}
        value={state}
        update={setState}
        ref={stateEl}
      />
      <button onClick={validate}>Validate Form</button>
    </div>
  );
};

export default ImperativeHandleComponent;
```

</details>

## CSS in JS

### Emotion Setup & Nav Bar

[emotion](https://emotion.sh/docs/introduction)

`npm install @emotion/core @emotion/babel-preset-css-prop`

`touch ./src/NavBar.js`

<details>
<summary>NavBar.js</summary>

```jsx
import React from "react";
import { Link } from "@reach/router";
import { css } from "@emotion/core";

const NavBar = () => (
  <header
    css={css`
      background-color: #333;
      padding: 15px;
    `}
  >
    <Link to="/">Adopt Me!</Link>
    <span aria-label="logo" role="img">
      🐈
    </span>
  </header>
);

export default NavBar;
```

</details>

这里为了语法高亮与自动提示，需要安装`vscode-styled-component`插件

<details>
<summary>修改`.babelrc`</summary>

```json
{
  "presets": [
    "@babel/preset-react",
    "@babel/preset-env",
    [
      "@emotion/babel-preset-css-prop",
      {
        "sourceMap": false
      }
    ]
  ],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

</details>

### Template Literals & Hooks

因为是在js中书写的css，并且使用了模板字符串，所以我们可以设置变量来修改css属性值。

也可以结合Hooks，设置状态来修改css值。

除了上述做法带来的便利之外，直接在js中引入css可以使代码更加耦合，这样在删除组件时也就删除了对应的css代码，使得组件更加模块化。

### Design Systems & Compound Selectors

我们还可以把样式设置文件单独打包为一个模块，在各个子模块中进行引用，这样更改样式模块就可以更改全部css属性。

### Animations

同样可以设置变量来保存动画设置，在css中使用模板字符串引入设置好的动画变量

## Code Splitting

[note](https://btholt.github.io/complete-intro-to-react-v5/code-splitting)

`git reset HEAD --hard`将项目文件恢复至初始状态

`git clean -f`删除所有未被git跟踪的文件（`NavBar.js`)

`npm i`

`npm run dev`

当我们构建一个复杂的单页应用时，我们希望先加载首屏所需组件，在需要时再加载其他组件，这就是code splitting。

<details>
<summary>修改App.js如下</summary>

```jsx
// import from React
import React, { useState, lazy, Suspense } from "react";
// delete Details & Search params imports

// above const App =
const Details = lazy(() => import("./Details"));
const SearchParams = lazy(() => import("./SearchParams"));

// replace Router
<Suspense fallback={<h1>loading route …</h1>}>
  <Router>
    <SearchParams path="/" />
    <Details path="/details/:id" />
  </Router>
</Suspense>;
```

</details>

### Code Splitting Libraries & Child Components

如果在懒加载的组件中引入了第三方库，可以看到第三方库随同懒加载的组件一同打包在了一起，如果库文件体积较大，无疑可以提升首屏加载速度。

## Sever Side Rendering

### SSR Rationale & Initial Setup

`git reset HEAD --hard`将项目文件恢复至初始状态

`git clean -f`删除所有未被git跟踪的文件

`npm i`

`npm run dev`

当我们此时查看源代码是可以看到html文件仅有几个节点，我们希望在用户加载时能够拥有一些渲染好的DOM节点，这就需要服务端渲染。

`touch ./src/ClientApp.js`

<details>
<summary>ClientApp.js</summary>

```jsx
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.hydrate(<App />, document.getElementById("root"));
```

</details>

<details>
<summary>App.js</summary>

```jsx
// remove react-dom import

// replace render at bottom
export default App;
```

</details>

<details>
<summary>Modal.js</summary>

```jsx
// replace modalRoot assignment
let modalRoot;

// in function
modalRoot = modalRoot ? modalRoot : document.getElementById("modal");
```

</details>

`npm install babel-cli express`

<details>
<summary>Now in your package.json, add the following to your "scripts"</summary>

```json
"build": "parcel build --public-url ./dist/ src/index.html",
"start": "npm -s run build && babel-node server/index.js"
```

</details>

### Server Side Rendering to Strings

`mkdir ./server`

`touch ./server/index.js`

<details>
<summary>index.js</summary>

```jsx
import express from "express";
import React from "react";
import { renderToString } from "react-dom/server";
import { ServerLocation } from "@reach/router";
import fs from "fs";
import App from "../src/App";

const PORT = process.env.PORT || 3000;
const html = fs.readFileSync("dist/index.html").toString();

const parts = html.split("not rendered");

const app = express();

app.use("/dist", express.static("dist"));
app.use((req, res) => {
  const reactMarkup = (
    <ServerLocation url={req.url}>
      <App />
    </ServerLocation>
  );

  res.send(parts[0] + renderToString(reactMarkup) + parts[1]);
  res.end();
});

console.log("listening on " + PORT);

app.listen(PORT);
```

</details>

`npm run start`

此时查看`localhost:3000`可以看到已经生成了HTML结构代码。

### Server Side Rendering to Node Stream

之前我们使用了`react-dom/server`的`renderToString`方法将全部文件转换为字符串后一起发送给客户端，我们可以已使用stream边转换边发送以提高发送的速度。

<details>
<summary>index.js</summary>

```jsx
// change react-dom import
import { renderToNodeStream } from "react-dom/server";

// replace app.use call
app.use((req, res) => {
  res.write(parts[0]);
  const reactMarkup = (
    <ServerLocation url={req.url}>
      <App />
    </ServerLocation>
  );

  const stream = renderToNodeStream(reactMarkup);
  stream.pipe(
    res,
    { end: false }
  );
  stream.on("end", () => {
    res.write(parts[1]);
    res.end();
  });
});
```

</details>

## TypeScript with React

### TypeScript Introduction

`git reset HEAD --hard`将项目文件恢复至初始状态

`git clean -f`删除所有未被git跟踪的文件

`npm i`

`npm run dev`

这里主要介绍了使用ts可以在vscode中获得自动的类型检查与代码提示，极大地提升了编码体验。

### TypeScript Configuration for React

`npm i -D typescript`

`npx tsc --init`

Open your new tsconfig.json file and uncomment the jsx field. Replace preserve with react. This lets TypeScript that you're writing React. Then update the target to be ES2018 so that you can use async / await and promises.

`npm install -D @types/react @types/react-dom @types/reach__router`

### Typing the Modal Component

<details>
<summary>将Modal.js修改为Modal.tsx</summary>

```tsx
import React, { useEffect, useRef, FunctionComponent } from "react";
import { createPortal } from "react-dom";

const modalRoot = document.getElementById("modal");

const Modal: FunctionComponent = ({ children }) => {
  const elRef = useRef(document.createElement("div"));

  useEffect(() => {
    if (!modalRoot) {
      return;
    }
    modalRoot.appendChild(elRef.current);
    return () => {
      modalRoot.removeChild(elRef.current);
    };
  }, []);

  return createPortal(<div>{children}</div>, elRef.current);
};

export default Modal;
```

</details>

### Migrating to TSLint

`npm uninstall eslint babel-eslint eslint-config-prettier eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-react-hooks`

`npm install -D tslint tslint-react tslint-config-prettier`

修改package.json中的scripts

`"lint": "tslint --project",`

`touch tslint.json`

```json
{
  "extends": ["tslint: recommended", "tslint-react", "tslint-config-prettier"],
  "rules": {
    "ordered-imports": false,
    "object-literal-sort-keys": false,
    "no-console": false,
    "jsx-no-lambda": false,
    "member-ordering": false
  }
}
```

然后可以安装tslint插件以便在vscode中提升体验

### Typing Context

<details>
<summary>修改ThemeContext.js为ThemeContext.tsx</summary>

```tsx
import { createContext } from "react";

const ThemeContext = createContext<[string, (theme: string) => void]>([
  "green",
  () => {}
]);

export default ThemeContext;
```

</details>

### Typing a Class Component

<details>
<summary>修改Details.js为Details.tsx</summary>

```tsx
import React from "react";
import pet, { Photo } from "@frontendmasters/pet";
import { navigate, RouteComponentProps } from "@reach/router";
import Carousel from "./Carousel";
import Modal from "./Modal";
import ErrorBoundary from "./ErrorBoundary";
import ThemeContext from "./ThemeContext";

class Details extends React.Component<RouteComponentProps<{ id: string }>> {
  public state = {
    loading: true,
    showModal: false,
    name: "",
    animal: "",
    breed: "",
    location: "",
    description: "",
    media: [] as Photo[],
    url: ""
  };
  public componentDidMount() {
    if (!this.props.id) {
      navigate("/");
      return;
    }
    pet
      .animal(+this.props.id)
      .then(({ animal }) => {
        this.setState({
          name: animal.name,
          animal: animal.type,
          location: `${animal.contact.address.city}, ${
            animal.contact.address.state
          }`,
          description: animal.description,
          media: animal.photos,
          breed: animal.breeds.primary,
          url: animal.url,
          loading: false
        });
      })
      .catch((err: Error) => this.setState({ error: err }));
  }
  public toggleModal = () =>
    this.setState({ showModal: !this.state.showModal });
  public adopt = () => navigate(this.state.url);
  public render() {
    if (this.state.loading) {
      return <h1>loading … </h1>;
    }

    const {
      animal,
      breed,
      location,
      description,
      media,
      name,
      showModal
    } = this.state;

    return (
      <div className="details">
        <Carousel media={media} />
        <div>
          <h1>{name}</h1>
          <h2>{`${animal} — ${breed} — ${location}`}</h2>
          <ThemeContext.Consumer>
            {([theme]) => (
              <button
                style={{ backgroundColor: theme }}
                onClick={this.toggleModal}
              >
                Adopt {name}
              </button>
            )}
          </ThemeContext.Consumer>
          <p>{description}</p>
          {showModal ? (
            <Modal>
              <h1>Would you like to adopt {name}?</h1>
              <div className="buttons">
                <button onClick={this.adopt}>Yes</button>
                <button onClick={this.toggleModal}>No</button>
              </div>
            </Modal>
          ) : null}
        </div>
      </div>
    );
  }
}

export default function DetailsErrorBoundary(
  props: RouteComponentProps<{ id: string }>
) {
  return (
    <ErrorBoundary>
      <Details {...props} />
    </ErrorBoundary>
  );
}
```

</details>

### Typing an Error Bundary

<details>
<summary>ErrorBundary.tsx</summary>

```tsx
// mostly code from reactjs.org/docs/error-boundaries.html
import React, { Component, ErrorInfo } from "react";
import { Link, Redirect } from "@reach/router";

class ErrorBoundary extends Component {
  public state = { hasError: false, redirect: false };
  public static getDerivedStateFromError() {
    return { hasError: true };
  }
  public componentDidCatch(error: Error, info: ErrorInfo) {
    console.error("ErrorBoundary caught an error", error, info);
  }
  public componentDidUpdate() {
    if (this.state.hasError) {
      setTimeout(() => this.setState({ redirect: true }), 5000);
    }
  }
  public render() {
    if (this.state.redirect) {
      return <Redirect to="/" />;
    }

    if (this.state.hasError) {
      return (
        <h1>
          There was an error with this listing. <Link to="/">Click here</Link>{" "}
          to back to the home page or wait five seconds
        </h1>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

</details>

### Props & State Interface

<details>
<summary>Carousel.tsx</summary>

```tsx
import React from "react";
import { Photo } from "@frontendmasters/pet";

interface IProps {
  media: Photo[];
}

interface IState {
  active: number;
  photos: string[];
}

class Carousel extends React.Component<IProps, IState> {
  public state = {
    photos: [],
    active: 0
  };
  public static getDerivedStateFromProps({ media }: IProps) {
    let photos = ["http://placecorgi.com/600/600"];

    if (media.length) {
      photos = media.map(({ large }) => large);
    }

    return { photos };
  }
  public handleIndexClick = (event: React.MouseEvent<HTMLElement>) => {
    if (!(event.target instanceof HTMLElement)) {
      return;
    }
    if (event.target.dataset.index) {
      this.setState({
        active: +event.target.dataset.index
      });
    }
  };
  public render() {
    const { photos, active } = this.state;
    return (
      <div className="carousel">
        <img src={photos[active]} alt="animal" />
        <div className="carousel-smaller">
          {photos.map((photo, index) => (
            <img
              key={photo}
              onClick={this.handleIndexClick}
              data-index={index}
              src={photo}
              className={index === active ? "active" : ""}
              alt="animal thumbnail"
            />
          ))}
        </div>
      </div>
    );
  }
}

export default Carousel;
```

</details>

<details>
<summary>Pet.tsx</summary>

```tsx
import React, { FunctionComponent } from "react";
import { Link } from "@reach/router";
import { Photo } from "@frontendmasters/pet";

interface IProps {
  name: string;
  animal: string;
  breed: string;
  media: Photo[];
  location: string;
  id: number;
}

const Pet: FunctionComponent<IProps> = props => {
  const { name, animal, breed, media, location, id } = props;

  let hero = "http://placecorgi.com/300/300";
  if (media.length) {
    hero = media[0].small;
  }

  return (
    <Link to={`/details/${id}`} className="pet">
      <div className="image-container">
        <img src={hero} alt={name} />
      </div>
      <div className="info">
        <h1>{name}</h1>
        <h2>{`${animal} — ${breed} — ${location}`}</h2>
      </div>
    </Link>
  );
};

export default Pet;
```

</details>

### Heterogenous Arrays & Hooks

<details>
<summary>useDropdown.tsx</summary>

```tsx
import React, { useState, FunctionComponent } from "react";

const useDropdown = (
  label: string,
  defaultState: string,
  options: string[]
) => {
  const [state, updateState] = useState(defaultState);
  const id = `use-dropdown-${label.replace(" ", "").toLowerCase()}`;
  const Dropdown: FunctionComponent = () => (
    <label htmlFor={id}>
      {label}
      <select
        id={id}
        value={state}
        onChange={e => updateState(e.target.value)}
        onBlur={e => updateState(e.target.value)}
        disabled={!options.length}
      >
        <option />
        {options.map(item => (
          <option key={item} value={item}>
            {item}
          </option>
        ))}
      </select>
    </label>
  );
  return [state, Dropdown, updateState] as [
    string,
    FunctionComponent,
    Dispatch<string>
  ];
};

export default useDropdown;
```

</details>

<details>
<summary>SearchParams.tsx</summary>

```tsx
import React, {
  useState,
  useEffect,
  useContext,
  FunctionComponent
} from "react";
import pet, { ANIMALS, Animal } from "@frontendmasters/pet";
import useDropdown from "./useDropdown";
import Results from "./Results";
import ThemeContext from "./ThemeContext";
import { RouteComponentProps } from "@reach/router";

const SearchParams: FunctionComponent<RouteComponentProps> = () => {
  const [theme, setTheme] = useContext(ThemeContext);
  const [location, updateLocation] = useState("Seattle, WA");
  const [breeds, updateBreeds] = useState([] as string[]);
  const [pets, setPets] = useState([] as Animal[]);
  const [animal, AnimalDropdown] = useDropdown("Animal", "dog", ANIMALS);
  const [breed, BreedDropdown, updateBreed] = useDropdown("Breed", "", breeds);

  async function requestPets() {
    const { animals } = await pet.animals({
      location,
      breed,
      type: animal
    });

    console.log("animals", animals);

    setPets(animals || []);
  }

  useEffect(() => {
    updateBreeds([]);
    updateBreed("");

    pet.breeds(animal).then(({ breeds }) => {
      const breedStrings = breeds.map(({ name }) => name);
      updateBreeds(breedStrings);
    }, console.error);
  }, [animal]);

  return (
    <div className="search-params">
      <form
        onSubmit={e => {
          e.preventDefault();
          requestPets();
        }}
      >
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="Location"
            onChange={e => updateLocation(e.target.value)}
          />
        </label>
        <AnimalDropdown />
        <BreedDropdown />
        <label htmlFor="location">
          Theme
          <select
            value={theme}
            onChange={e => setTheme(e.target.value)}
            onBlur={e => setTheme(e.target.value)}
          >
            <option value="peru">Peru</option>
            <option value="darkblue">Dark Blue</option>
            <option value="chartreuse">Chartreuse</option>
            <option value="mediumorchid">Medium Orchid</option>
          </select>
        </label>
        <button style={{ backgroundColor: theme }}>Submit</button>
      </form>
      <Results pets={pets} />
    </div>
  );
};

export default SearchParams;
```

</details>

<details>
<summary>Results.tsx</summary>

```tsx
import React, { FunctionComponent } from "react";
import Pet from "./Pet";
import { Animal } from "@frontendmasters/pet";

interface IProps {
  pets: Animal[];
}

const Results: FunctionComponent<IProps> = ({ pets }) => {
  return (
    <div className="search">
      {!pets.length ? (
        <h1>No Pets Found</h1>
      ) : (
        pets.map(pet => {
          return (
            <Pet
              animal={pet.type}
              key={pet.id}
              name={pet.name}
              breed={pet.breeds.primary}
              media={pet.photos}
              location={`${pet.contact.address.city}, ${
                pet.contact.address.state
              }`}
              id={pet.id}
            />
          );
        })
      )}
    </div>
  );
};

export default Results;
```

</details>

### TypeScript Review and Q&A

直接将App.js修改为App.tsx，可以看到无需进行修改。修改index.html的script的src为App.tsx。

`npm run dev`可以看到网站正常运行

在package.json中添加script命令`"typecheck": "tsc --noEmit"`

`npm run typecheck`

可以看到没有任何提示，说明通过了类型检查

## Redux

### Redux Introduction

[note](https://btholt.github.io/complete-intro-to-react-v5/redux)

现在通过Hooks的useContext和useReducer可以模拟出redux的功能

`git reset HEAD --hard`将项目文件恢复至初始状态

`git clean -f`删除所有未被git跟踪的文件

`npm i`

`npm i redux react-redux`

### Creating a Store

`touch ./src/store.js`

<details>
<summary>store.js</summary>

```js
import { createStore } from "redux";
import reducer from "./reducers";

const store = createStore(
  reducer,
  typeof window === "object" &&
    typeof window.__REDUX_DEVTOOLS_EXTENSION__ !== "undefined"
    ? window.__REDUX_DEVTOOLS_EXTENSION__()
    : f => f
);

export default store;
```

</details>

### Creating a Reducer

`mkdir ./src/reducers`

`touch ./src/reducers/index.js`

<details>
<summary>index.js</summary>

```js
import { combineReducers } from "redux";
import location from "./location";
import theme from "./theme";

export default combineReducers({
  location,
  theme
});
```

</details>

`touch ./src/reducers/location.js`

<details>
<summary>location.js</summary>

```js
export default function theme(state = "darkblue", action) {
  if (action.type === "CHANGE_THEME") {
    return action.payload;
  } else {
    return state;
  }
}
```

</details>

`./src/reducers/theme.js`

<details>
<summary>theme.js</summary>

```js
export default function theme(state = "darkblue", action) {
  if (action.type === "CHANGE_THEME") {
    return action.payload;
  } else {
    return state;
  }
}
```

</details>

### Creating Actions

`mkdir ./src/actionCreators`

`touch ./src/actionCreators/changeLocation.js`

<details>
<summary>changeLocation.js</summary>

```js
export default function changeLocation(location) {
  return {
    type: "CHANGE_LOCATION",
    payload: location
  };
}
```

</details>

`touch ./src/actionCreators/changeTheme.js`

<details>
<summary>changeTheme.js</summary>

```js
export default function changeTheme(theme) {
  return {
    type: "CHANGE_THEME",
    payload: theme
  };
}
```

</details>

### Connecting Redux to the Application

<details>
<summary>App.js</summary>

```js
// delete ThemeContext, useState import

// import
import { Provider } from "react-redux";
import store from "./store";

// delete useState call

// wrap app with
<Provider store={store}>[…]</Provider>;
```

</details>

<details>
<summary>SearchParams.js</summary>

```js
// replace ThemeContext import
import { connect } from "react-redux";

// replace context references
location: this.props.location,
animal: this.props.animal,
breed: this.props.breed,

// replace export
const mapStateToProps = ({ theme, location }) => ({
  theme,
  location
});

export default connect(mapStateToProps)(SearchParams);
```

</details>

### Redux Devtools

[redux dev tools - chrome](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en)

### Completing the Redux Writing

<details>
<summary>Details.js</summary>

```js
// replace ThemeContext import
import { connect } from "react-redux";

// remove all the ThemeContext stuff and the interior function
// replace `context.theme` with just `this.props.theme` for the backgroundColor

// bottom
const mapStateToProps = ({ theme }) => ({ theme });

const WrappedDetails = connect(mapStateToProps)(Details);

// replace <Details />
<WrappedDetails {...props} />;
```

</details>

## Testing React

### Jest & React Testing Library

`git reset HEAD --hard`

`git clean -f`

`npm i`

`npm i -D jest react-testing-library`

`mkdir ./src/__test__`

`touch ./src/__test__/SearchParams.test.js`

### API Mocks

`mkdir ./__mocks__`

`mkdir ./__mocks__/frontendmasters`

`touch ./__mocks__/frontendmasters/pet.js`

<details>
<summary>pet.js</summary>

```js
import { readFileSync } from "fs";
import path from "path";
import { act } from "react-testing-library";

const breeds = [
  { name: "Bichon Frise" },
  { name: "Bolognese" },
  { name: "Bolonka" },
  { name: "Coton de Tulear" },
  { name: "Havanese" },
  { name: "Lowchen" },
  { name: "Maltese" }
];

const doggos = JSON.parse(
  readFileSync(path.join(__dirname, "res.json")).toString()
);

export const ANIMALS = ["dog", "cat", "bird"];
export const _breeds = breeds;
export const _dogs = doggos.animals;

const mock = {
  breeds: jest.fn(() => {
    return {
      then: callback => act(() => callback({ breeds }))
    };
  }),
  animals: jest.fn(() => {
    return {
      then: callback => act(() => callback(doggos))
    };
  })
};

export default mock;
```

</details>

`touch ./__mocks__/frontendmasters/res.json`

[go copy and paste this fixture](https://raw.githubusercontent.com/btholt/complete-intro-to-react-v5/testing/__mocks__/@frontendmasters/res.json)

### Component Tests

<details>
<summary>SearchParams.test.js</summary>

```js
import React from "react";
import { render, cleanup } from "@testing-library/react";
import pet, { _breeds, _dogs, ANIMALS } from "@frontendmasters/pet";
import SearchParams from "../SearchParams";

afterEach(cleanup);

test("SearchParams", async () => {
  const { getByTestId } = render(<SearchParams />);

  const animalDropdown = getByTestId("use-dropdown-animal");
  expect(animalDropdown.children.length).toEqual(ANIMALS.length + 1);
});
```

</details>

<details>
<summary>useDropdown.js </summary>

```jsx
 <select
  data-testid={id}
  […]
>
```

</details>

### Snapshots, Watch Mode & Test Coverage

Here we're doing a Jest test in which we're doing a snapshot test. As soon as you run this test the first time, it'll run and capture the output in a snapshot template string (you'll see it after you run it successfully the first time.) Every time afterwards when you run it it will compare the output with this snapshot. If it changes, it'll fail the test. If you mean to change it, you just run jest -u and it will update the snapshots. Cool, right? You can also have it write to an external file instead of inline in the code with toMatchSnapshot. I like that everything is in one file. It's up to you.

Add that your package.json: `"test:update": "jest -u",`

Now your snapshot test should pass. Check out that it created a `__snapshots__` directory with your snapshot in it (if you did it toMatchSnapshot). You should commit this file as everyone should get the same output as you.

Let's talk about code coverage. Luckily has it built into jest because it can be a bit of a pain to set up. It's a tool called istanbul. Istanbul generates a report of how much of you code is covered by tests. It's a useful metric to track you're generally adding tests when you add new features but by no means does a 100% test-covered project means that those tests are good. It's easy to write garbage tests, and garbage tests hurt more than help.

In any case, run `npx jest --coverage` to try it out. It'll show you an outline of the results in the CLI and then generate a report in a new coverage directory (don't check this in to git.) Open coverage/lcov-report/index.html to see a nice web page outlining your test coverage. Add this to your package.json: `"test:coverage": "jest --coverage",`.

One more useful thing about Jest: watch mode. You can run your tests interactively and on file-save. It'll only re-run tests that could have possibly been changed and previously failed so it's a fast feedback cycle to fix tests. Add this as well to your package.json: `"test:watch": "jest --watch",`.
