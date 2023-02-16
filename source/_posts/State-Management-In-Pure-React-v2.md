---
title: 'State Management In Pure React, v2'
date: 2020-03-07 14:51:11
categories: [学习笔记]
tags: [FrontEndMasters, React, React Hooks]
---
## Introduction

+ This is a course for keeping your state manageable when it's no longer a toy application.

+ In this course, we'll be working with pure React.

+ So, what are we going to do today?
  + Think deeply about what "state" even means in a React application.
  + Learn a bit about the inner workings of `this.setState`.
  + How class-based component state and hooks differ.
  + Explore APIs for navigating around prop-drilling.
  + Use reducers for advanced state management.
  + Write our own custom hooks for managing state.
  + Store state in Local Storage.
  + Store state in the URL using query parameters.
  + Fetch state from a server-because that's a thing.
<!-- more -->
+ And now... Understanding State

+ The main job of React is to take your application state and turn it into DOM nodes.
  + 在之前，我们可能通过JQuery来获取页面上UI所保存的数据并通过对其操作来构建应用状态管理。
  + 在一些现代框架中，我们通过一些JS的数据结构来保存应用转态并将其映射到UI中。
  + rules(data) => ui

### Type of State

+ There are many kinds of state.
  + Model data: The nouns in your application.
  + View/UI state: Are those nouns sorted in ascending or descending order?
  + Session state: Is the user even logged in?
  + Communication: Are we in the process of fetching the nouns from the server?
  + Location: Where are we in the application? Which nouns are we looking at?

+ Or, it might make sense to think about state relative to time.
  + Model state: This is likely the data in your application. This could be the items in a given list.
  + Ephemeral state: Stuff like the value of an input field that will be wiped away when you hit "enter". This could be the order in which a given list is sorted.

+ Spoiler alert: There is no silver bullet.

## Class-Based State

### setState & Class

+ And now... An Uncomfortably Close Look at React Component State

+ Let's start with the world's simplest React component.

+ `https://github.com/stevekinney/simple-counter`

+ Oh, wow -  it looks like it's time for a pop quiz, already

```js
class Counter extends Component {
  constructor() {
    this.state = {
      count: 0
    }
  }

  render() { ... }
}
```

### setState & Asynchronous

```js
this.setState({ count: this.state.count + 1});
this.setState({ count: this.state.count + 1});
this.setState({ count: this.state.count + 1});

console.log(this.state.count);
```

+ 如果我们在一个方法中多次执行setState对同一个state进行操作，那么打印出来的值是什么？
+ 答案是 0

+ `this.setState()` is asynchronous.

+ React is trying to avoid unnecessary re-renders.

```js
export default class Counter extends Component {
  constructor() { ... }

  increment() {
    this.setState({ count: this.state.count + 1});
    this.setState({ count: this.state.count + 1});
    this.setState({ count: this.state.count + 1});
  }
  
  render() { ... }
}
```

+ What will the count be after the user's clicks the "Increment" button?
  + We may expect get "3" but actually get "1"

+ Effectively, you're queuing up state changes.
+ React will batch them up, figure out the result and then efficiently make that change.

+ 实际上述过程可能类似如下代码

```js
Object.assign(
  {},
  firstCallToSetState,
  secondCallToSetState,
  thirdCallToSetState, // 最后一个参数对象的属性值会覆盖之前的属性值
)
```

### setState & Function

+ There is actually a bit more to `this.setState()`

```js
export default class Counter extends Component {
  constructor() { ... }

  increment() {
    this.setState((state) => { return { count: state.count + 1 } })
    this.setState((state) => { return { count: state.count + 1 } })
    this.setState((state) => { return { count: state.count + 1 } })
  }

  render() { ... }
}
```

+ By use a function in setState, we can get "3" this time.

+ When you pass functions to `this.setState()`, it plays through each of them.

+ 因为传递的是一个回调函数，所以我们可以在函数内进行一些判断或其他操作

```js
export default class Counter extends Component {
  constructor() { ... }

  increment() {
    this.setState(state => {
      if(state.count >= 5) return;
      return { count: state.count + 1 }
    })
  }

  render() { ... }
}
```

+ 我们还可以通过回调的第二个参数获取当前组件的参数对象，所以我们可以进行如下操作

```js
increment() {
  this.setState((state, props) => {
    const {max, step} = props
    const {count} = state

    if(count >= max) return;
    return { count: count + step }
  })
}
```

+ 又因为回调函数只是一个普通函数，所以我们也可以将其抽离出来以便进行测试。

### setState & Callback

+ `this.setState`不仅可以接受一个参数，还可以接受第二个参数作为改变state之后的回调函数

```js
increment() {
  this.setState(({ count }) => {
    return { count: count + 1 }
  }, () => {
    console.log(this.state.count) // 异步执行，打印值为1
  })
  console.log(this.state.count) // 同步执行，打印值为0
}
```

### setState & Helper Function

```js
const getStateFromLocalStorage = () => {
  const storage = localStorage.getItem('counterState');
  if (storage) return JSON.parse(storage);
  return { count: 0 };
};

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = getStateFromLocalStorage()
  }

  increment = () => {
    this.setState(
      ({ count }) => ({ count: count + 1 }),
      () => {
        localStorage.setItem('counterState', JSON.stringify(this.state))
      },
    );
  };


  render() { ... }
}
```

### setState Patterns

+ Patterns and anti-patterns
  + When we're working with props, we have PropTypes. That's not the case with state.*

```js
function shouldIKeepSomethingInReactState() {
  if(canICalculateItFromProps()) {
    // Don't duplicate data from props in state
    // Calculate what you can in render() method
    return false
  }
  if(!amIUsingItInRenderMethod()) {
    // Don't keep something in the state
    // If you don't use it for rendering.
    // For example, API subscriptions are
    // better off as custom private fields
    // or variables in external modules.
    return false
  }
  // You can use React state for this!
  return true
}
```

+ Don't use `this.state` for derivations of props.

+ Bad example

  ```js
  class User extends Component {
    constructor(props) {
      super(props)
      this.state = {
        fullName: props.firstName + ' ' + props.lastName
      }
    }
  }
  ```

+ Don't do this. Instead, derive computed properties directly from the props themselves.

+ Good
  
  ```js
  class User extends Component {
    render() {
      const { firstName, lastName } = this.props
      const fullName = firstName + ' ' + lastName
      return (
        <h1>{fullName}</h1>
      )
    }
  }
  ```

+ Alternatively

  ```js
  class User extends Component {
    get fullName() {
      const { firstName, lastName } = this.props
      return firstName + ' ' + lastName
    }

    render() {
      return (
        <h1>{this.fullName}</h1>
      )
    }
  }
  ```

+ You don't need to shove everything into your render method.

+ You can break things out into helper methods
  
  ```js
  class UserList extends Component {
    render() {
      const { users } = this.props
      return (
        <section>
          <VeryImportantUserControls />
          { users.map(user => (
            <UserProfile
              key={user.id}
              photograph={user.mugshot}
              onLayoff={handleLayoff}
            />
          )) }
          <SomeSpecialFooter>
        </section>
      )
    }
  }
  ```

  + to

  ```js
  class UserList extends Component {
    renderUserProfile(user) {
      return (
        <UserProfile
          key={user.id}
          photograph={user.mugshot}
          onLayoff={handleLayoff}
        />
      )
    }

    render() {
      const { users } = this.props
      return (
        <section>
          <VeryImportantUserControls />
          { users.map(this.renderUserProfile) }
          <SomeSpecialFooter>
        </section>
      )
    }
  }
  ```

  + or

  ```js
  const renderUserProfile = user => {
    return (
      <UserProfile
        key={user.id}
        photograph={user.mugshot}
        onLayoff={handleLayoff}
      />
    )
  }

  const UserList = ({users}) => {
    return (
      <section>
        <VeryImportantUserControls />
        { users.map(this.renderUserProfile) }
        <SomeSpecialFooter>
      </section>
    )
  }
  ```

+ Don't use state for things you're not going to render.

+ Use sensible defaults.

## Hooks State

### Refactoring & Hooks

+ And now...
+ An Equally Uncomfortably Close Look at React Hooks

```js
const [count, setCount] = React.useState(0)

const increment = () => setCount(count + 1)
const decrement = () => setCount(count - 1)
const reset = () => setCount(0)
```

```js
const increment = () => setCount(c => c + 1)
```

+ 这里与this.setState类似同样可以传入一个函数，注意与class component不同的是必须要有返回值，否则state会变为undefined.

### useEffect & Dependencies

```js
useEffect(() => {
  document.title = count;
}, [count]); // count变化时执行前面的回调
```

### Refactoring & Custom Hook

```js
const useLocalStorage = (initialState, key) => {
  const get = () => {
    const storage = localStorage.getItem(key) // count: {value: 1}
    if(storage) return JSON.parse(storage).value
    return initialState
  }

  const [value, setValue] = useState(get())

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify({ value }))
  }, [])

  return [value, setValue]
}
```

### Persisting State & useRef

+ 如果我们在一个类组件的`componentDidUpdate()`生命周期中延时三秒打印`this.state.count`, 并同时连续点击按钮使count自增，
+ 那么结果会打印出什么？
+ 如果点击七次，那么会输出七次7
+ 说明我们虽然改变了this.state.count的值，但是仍保持了同一个引用

+ 如果我们使用函数组件的useState保存状态，同样在useEffect内延时三秒打印，这次会输出什么？
+ 答案是点击时的数据

+ 这其实是基于JS的基本原理而不是React的独创，使用类组件我们在this.state这一个对象中保存数据，所以this.state永远指向同一对象，那么获取属性时会获取这个对象属性的最新值。而hooks变量保存状态，所以每次改变时都指向不同的值。

+ 那么有时我们需要获取到当前状态和之前状态，例如进行比较或者撤销，显然useState是无法做到的

```js
const Counter = () => {
  const [count, setCount] = useState(0)
  const countRef = React.useRef()

  // 此时我们可以比较当前与之前状态的大小
  const message = countRef.current < count ? 'Higher' : 'Lower'

  // 在此之前countRef.current保存的仍是上一个count的值
  countRef.current = count
}
```

### useEffect & Cleanup

+ 在类组件中我们有componentWillUnmount来清理一些副作用，类似计时器或者websocket链接
+ 在函数组件中，我们可以使用useEffect的第一个参数也就是回调函数的返回值来清理副作用

```js
useEffect(() => {
  setInterval(() => {
    console.log(`Count: ${count}`)
  }, 1000)
}. [count])
// 如果我们此时更改了count的值，回调会重新执行，也就是注册一个新的计时器
// 控制台一秒内会同时打印多个值
```

+ 我们可以清理副作用

```js
useEffect(() => {
  const id = setInterval(() => {
    console.log(`Count: ${count}`)
  }, 1000)

  // 返回一个函数清理副作用
  return () => {
    clearInterval(id)
  }
}, [count])
```

### Reducers

+ And now...
+ The joy of useReducer

+ 由于js对于Object类型传递的为引用而不是具体值，所以如果我们只是单纯的mutate一个数组(原生API push等方法)，setState无法侦测到state的变化，也就无法进行重新渲染，所以我们要使用一些immutable的方法，创建新的引用。

+ 例如以下代码

```js
const Application = () => {
  const [grudges, setGrudges] = useState(initialState);

  const addGrudge = grudge => {
    grudge.id = id();
    grudge.forgiven = false;
    setGrudges([grudge, ...grudges]);
  };

  const toggleForgiveness = id => {
    setGrudges(
      grudges.map(grudge => {
        if (grudge.id !== id) return grudge;
        return { ...grudge, forgiven: !grudge.forgiven };
      })
    );
  };

  return (
    <div className="Application">
      <NewGrudge onSubmit={addGrudge} />
      <Grudges grudges={grudges} onForgive={toggleForgiveness} />
    </div>
  );
};
```

+ 当单个grudge改变时，这个列表会重新渲染，这显然不是我们想要的。

### Reducer Action & State

+ Reducer Function

+ Reducer的概念本身非常简单，就是一个函数，接收两个参数，一个是当前state，一个是action，最终返回新的state。
+ 当与React结合起来时，就是每当新的state产生，就通过重新传递props的方式来通知React重新渲染子组件

+ 一个简单的reducer可能如下

```js
const reducer = (state, action) => {
  return state
}
```

+ 可见reducer只是一个普通函数，我们可以与useReducer结合使用，同时也因为其只是一个简单函数，我们可以方便的编写测试用例进行测试

```js
const [grudges, dispatch] = useReducer(reducer, initialState)
```

### Reducer Action Keys & dispatch

+ 因为reducer的action使用字符串作为属性，推荐将字符串保存为常量，以提供更加好的代码提示与纠错

+ 我们可以使用useReducer将上述功能修改为如下

```js
const GRUDGE_ADD = 'GRUDGE_ADD';
const GRUDGE_FORGIVE = 'GRUDGE_FORGIVE';

const reducer = (state, action) => {
  if (action.type === GRUDGE_ADD) {
    return [action.payload, ...state];
  }
  if (action.type === GRUDGE_FORGIVE) {
    return state.map(grudge => {
      if (grudge.id !== action.payload.id) return grudge;
      return { ...grudge, forgiven: !grudge.forgiven };
    });
  }
  return state;
};

// const [grudges, setGrudges] = useState(initialState);
// 使用useReducer替代useState
const [grudges, dispatch] = useReducer(reducer, initialState);

const addGrudge = ({ person, reason }) => {
  dispatch({
    type: GRUDGE_ADD,
    payload: {
      person,
      reason,
      forgiven: false,
      id: id()
    }
  });
};

const toggleForgiveness = id => {
  dispatch({
    type: GRUDGE_FORGIVE,
    payload: {
      id
    }
  });
};
```

### React.memo & useCallback

+ 我们希望如果当前属性值与上次相同，那么就不在重新渲染依赖属性值的子组件。React提供了几种方式
  + React.memo()
  + useCallback
  + useMemo

## Context

### Prop Drilling & Context API

+ And now... The Perils Prop Drilling

+ Prop drilling occurs when you have deep component trees

+ And now... The Context API

### Creating a Context Provider

+ Context provides a way to pass data through the component tree without having to pass props down manually at every level

+ react.createContext() => provider + consumer

```js
import React from  'react'

const SuperCoolContext = React.createContext()

SuperCoolContext.Provider
SuperCoolContext.Consumer
```

```html
<CountContext.Provider value={0}>
  <CountContext.Consumer>
      { value => <p>{value}</p>}
  </CountContext.Consumer>
</CountContext.Provider>
```

+ Example

```js
const CountContext = createContext()

class CountProvider extends Component {
  state = { count: 0 }

  increment = () => this.setState(({ count }) => ({ count: count + 1}))
  decrement = () => this.setState(({ count }) => ({ count: count - 1}))

  render() {
    const { increment, decrement } = this
    const { count } = this.state
    const value = { count, increment, decrement }

    return (
      <CountContext.Provider value={value}>
        {this.props.children}
      </CountContext.Provider>
    )
  }
}
```
<!-- markdownlint-disable md033 -->
<details>
<summary>GrudgeContext</summary>

```js
import React, { createContext } from 'react';
import id from 'uuid/v4';
import initialState from './initialState';

export const GrudgeContext = createContext();

const GRUDGE_ADD = 'GRUDGE_ADD';
const GRUDGE_FORGIVE = 'GRUDGE_FORGIVE';

const reducer = (state, action) => {
  if (action.type === GRUDGE_ADD) {
    return [action.payload, ...state];
  }
  if (action.type === GRUDGE_FORGIVE) {
    return state.map(grudge => {
      if (grudge.id !== action.payload.id) return grudge;
      return { ...grudge, forgiven: !grudge.forgiven };
    });
  }
  return state;
};

export const GrudgeProvider = ({ children }) => {
  const [grudges, dispatch] = useReducer(reducer, initialState);

  const addGrudge = ({ person, reason }) => {
    dispatch({
      type: GRUDGE_ADD,
      payload: {
        person,
        reason,
        forgiven: false,
        id: id()
      }
    });
  };

  const toggleForgiveness = id => {
    dispatch({
      type: GRUDGE_FORGIVE,
      payload: {
        id
      }
    });
  };

  const value = {
    grudges,
    addGrudge,
    toggleForgiveness
  };

  return (
    <GrudgeContext.Provider value={value}>{children}</GrudgeContext.Provider>
  );
};
```

</details>

### Context & useContext Hook

当使用context时，优点是我们不必像之前一样到传递状态并担心状态在传递中是否正确。因此我们可以随意移动我们的UI组件并不必担心状态传递的问题。

+ Some Testing Notes
  + We lost all of our performance optimizations when moving to the Context API.
  + What's the right answer? It's a trade off.
  + Grudge List might seem like a toy application, but it could also represent a smaller part of a larger system.
  + Could you use the Context API to get things all of the way down to this level and then use the approach we had previously?

## Data Fetching

### Data Fetching & useEffect Hook

+ And now... What about fetching data?

+ useEffect is your friend

+ useEffect第二个参数一般来说不可省略，否则组件会不停重复加载

```js
const useFetch = url => {
  const [response, setResponse] = useState(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)

  useEffect(() => {
    setLoading(true)
    setResponse(null)
    setError(null)

    fetch(url)
      .then(response => response.json())
      .then(response => {
        setLoading(false)
        setResponse(response)
      })
      .catch(error => {
        setLoading(false)
        setError(error)
      })
  }, [])

  return [response, loading, error]
}
```

### Refactoring to a Custom Reducer

```js
const LOADING = "LOADING";
const RESPONSE_COMPLETE = "RESPONSE_COMPLETE";
const ERROR = "ERROR";

const initialDetailState = {
  result: null,
  loading: true,
  error: null
};

const fetchDetailReducer = (state, action) => {
  if (action.type === LOADING) {
    return {
      result: null,
      loading: true,
      error: null
    };
  }

  if (action.type === RESPONSE_COMPLETE) {
    return {
      result: action.payload.response,
      loading: false,
      error: null
    };
  }

  if (action.type === ERROR) {
    return {
      result: null,
      loading: false,
      error: action.payload.error
    };
  }

  return state;
};

const useFetchDetail = ({ url }) => {
  const [state, dispatch] = useReducer(fetchDetailReducer, initialDetailState);

  useEffect(() => {
    dispatch({ type: LOADING });

    const fetchUrl = async () => {
      try {
        const response = await fetch(url);
        const data = await response.json();
        dispatch({ type: RESPONSE_COMPLETE, payload: { response: data } });
      } catch (error) {
        dispatch({ type: ERROR, payload: { error } });
      }
    };

    fetchUrl();
  }, [url]);
};

return [state.result, state.loading, state.error];
```

## Thunks

### What is a Thunk

+ **thunk(noun):** a function returned from another function

```js
function definitelyNotAThunk() { // 顾名思义，这个函数不是thunk
  return function aThunk() { // 这个返回值才是thunk
    console.log('Hello, I am a thunk.')
  }
}
```

+ But, why is this useful?

+ The major idea behind a thunk is that it is code to be executed later

```js
const useThunkReducer = (reducer, initialState) => {
  const [state, dispatch] = useReducer(reducer, initialState);

  const enhancedDispatch = useCallback(action => {
    if (isFunction(action)) {
      action(dispatch);
    } else {
      dispatch(action);
    }
  });

  return [state, enhancedDispatch];
};
```

```js
const fetchCharacters = dispatch => {
  dispatch({ type: 'LOADING' })
  fetch(someUrl)
    .then(response => response.json)
    .then(response =>
      dispatch({
        type: 'RESPONSE_COMPLETE',
        payload: { characters: response.characters }
      })
    ).catch(error => {
      dispatch({
        type: 'Error',
        payload: { error }
      })
    })
}
```

### Routing & Thunks

### Implementing Undo & Redo

+ And now... Advanced Patterns: Implementing Undo & Redo

```js
{
  past: [allPastStates],
  present: currentStateOfTheWorld,
  future: [anyAndAllFutureStates]
}
```

```js
const useUndoReducer = (reducer, initialState) => {
  const undoState = {
    past: [],
    present: initialState,
    future: []
  }

  const undoReducer = (state, action) => {
    const newPresent = reducer(state.present, action)

    if(action.type === 'UNDO') {
      const [newPresent, ...newPast] = state.past
      return {
        past: newPast,
        present: newPresent,
        future: [state.present, ...state.future]
      }
    }

    if(action.type === 'REDO') {
      const [newPresent, ...newFuture] = state.future
      return {
        past: [state.present, ...state.past],
        present: newPresent,
        future: newFuture
      }
    }

    return {
      past: [state.present, ...state.past],
      present: newPresent,
      future: []
    }
  }

  return useReducer(undoReducer, undoState)
}
```

+ 这里说了句俏皮话
  + A week of coding save us hours of planning.

### Managing State in a Form

+ 当我们使useState来维护一个表单组件时，可能我们要维护多个state。我们可以编写一个useSetState来解决这个问题

```js
const reducer = (previousState = {}, updateState = {}) => {
  return {...previousState, ...updateState}
}

const useSetState = (initialState = {}) => {
  const [state, dispatch] = useReducer(reducer, initialState)

  const setState = updatedState => {
    dispatch(updatedState)
  }

  return [state, setState]
}
```

```js
const initialState = {
  userName: '',
  email: '',
  password: '',
  passwordConfirm: ''
}

const UserSignUp = () => {
  const [state, setState] = useSetState(initialState)

  const handleSubmit = (e) => {
    e.preventDefault()
    clear()
  }

  const handleChange = (e) => {
    setState({
      [e.target.name]: e.target.value
    })
  }

  const clear = () => {
    setState(initialState)
  }
}
```

## Wrapping Up

+ And now... The Ecosystem of Third-Party Hooks

[https://nikgraf.github.io/react-hooks/](https://nikgraf.github.io/react-hooks/)
