---
title: Complete-intro-to-React-v5
date: 2019-06-17 10:22:23
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, React, Parcel]
---

## Introduction

>This course is unique as compared to other React introductions because this course attempts to teach you not only React but the ecosystem around React. When I was learning React myself, I found myself frustrated that it seemed like every tutorial started on step 14 and left out the steps 1-13 of how to set up a React project. React is nearly never used by itself so it's useful to know the tools you're using. I believe you as a developer should know how your tools works and what purpose they're serving. Many times have I taught courses similar to this one to hear people using tools and complaining about them because they don't actually know why they're using them, just that they're necessary. As such, in this course we show you how to build projects without any using tools at all and introduce the various tools, one at a time so you understand the actual problem being solved by the tool. Hopefully given the knowledge of the problem solved by the tool you'll embrace the tools despite their complexities due to the ease and power they offer you.

[github](https://github.com/btholt/complete-intro-to-react-v5)

[course note](https://btholt.github.io/complete-intro-to-react-v5/)

[sourcetree](https://www.sourcetreeapp.com/)

[Git In-depth](https://frontendmasters.com/courses/git-in-depth/)

[Front End Happy Hour](https://frontendhappyhour.com/)

<!--more-->

### Project Setup

[Emmet cheatsheet](https://docs.emmet.io/cheat-sheet/)

### A Note on the Course Font

[Dank Mono](https://dank.sh/)

[Fira Code](https://github.com/tonsky/FiraCode)

## Pure React

### Getting Started with Pure React

<!-- markdownlint-disable MD033 -->

```js
const App = () => {
      return React.createElement(
        'div',
        {},
        React.createElement('h1', {}, 'Adopt Me!')
      )
    }

ReactDOM.render(
  React.createElement(App),
  document.getElementById('root')
)
```

### createElement Arguments

Three parameters that `React.createElement` get:

1. what kind of tag is it (`h1` or `div` or a composite component)
2. All the attribute that you want to give the component ({ id: ?? })
3. Children

### Reusable Components

```js
// 创建一个Pet组件
const Pet = () => {
  return React.createElement('div', {}, [
    React.createElement('h1', {}, "Luna"),
    React.createElement('h2', {}, "Dog"),
    React.createElement('h2', {}, "Havanese")
  ])
}

const App = () => {
  return React.createElement(
    'div',
    {},
    [
      React.createElement('h1', { id: 'something' }, 'Adopt Me!'),
      // 可以重复使用多次
      React.createElement(Pet),
      React.createElement(Pet),
      React.createElement(Pet)
    ]
  )
}
```

### Passing in Component Props

```js
const Pet = (props) => {
  return React.createElement('div', {}, [
    React.createElement('h1', {}, props.name),
    React.createElement('h2', {}, props.animal),
    React.createElement('h2', {}, props.breed)
  ])
}

const App = () => {
  return React.createElement(
    'div',
    {},
    [
      React.createElement('h1', { id: 'something' }, 'Adopt Me!'),
      React.createElement(Pet, {
        name: 'Luna',
        animal: 'Dog',
        breed: 'Havanese'
      }),
      React.createElement(Pet, {
        name: 'Pepper',
        animal: 'Bird',
        breed: 'Cockatiel'
      }),
      React.createElement(Pet, {
        name: 'Doink',
        animal: 'Cat',
        breed: 'Stray'
      })
    ]
  )
}
```

### Destructuring Props

可以使用es6的对象结构赋值重新设置参数

```js
const Pet = ({ name, animal, breed }) => {
  return React.createElement('div', {}, [
    React.createElement('h1', {}, name),
    React.createElement('h2', {}, animal),
    React.createElement('h2', {}, breed)
  ])
}
```

[destructuring](https://frontendmasters.com/courses/js-recent-parts/destructuring/)

## Tools

### npm & Generating a `package.json` File

[npm](https://www.npmjs.com/)

```bash
npm init -y
```

### Prettier

```bash
npm i -D prettier
```

### npm Scripts

<details>
<summary>package.json</summary>

```json
"scripts": {
    "format": "prettier \"src/**/*.{js,html}\" --write",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

</details>

### Prettier Setup

```bash
touch .prettierrc
```

<details>
<summary>.prettierrc</summary>

```json
{
  "semi": false,
  "singleQuote": true
}
```

</details>

[https://prettier.io/](https://prettier.io/)

### ESLint Setup

```bash
npm i -D eslint eslint-config-prettier
```

```bash
touch .eslintrc.json
```

### ESLint Confirguration

<details>
<summary>.eslintrc.json</summary>

```json
{
  "extends": [
    "eslint:recommended",
    "prettier",
    "prettier/react"
  ],
  "plugins": [],
  "parserOptions": {
    "ecmaVersion": 2019,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "env": {
    "es6": true,
    "browser": true,
    "node": true
  }
}
```

</details>

<br/>

<details>
<summary>package.json</summary>

```json
"lint": "eslint \"src/**/*.{js,jsx}\" --quiet"
```

</details>

### gitignore

```bash
touch .gitignore
```

<details>
<summary>.gitignore设置</summary>
<pre>
node_modules
.cache/
dist/
.env
.DS_Store
coverage/
.vscode/
</pre>
</details>

### Parcel

[Webpack 4 Fundamentals](https://frontendmasters.com/courses/webpack-fundamentals/)

[Complete Intro to React, v3 (feat. Redux, Router & Flow)](https://frontendmasters.com/courses/react/)

[Parcel 极速零配置Web应用打包工具](https://parceljs.org/)

```bash
npm install -D parcel-bundler
```

<details>
<summary>package.json</summary>

```json
"dev": "parcel src/index.html",
```

</details>

```bash
npm run dev
```

### Installing React & ReactDOM

```bash
npm i react react-dom
```

<details>
<summary>app.js</summary>

```js
import React from 'react'
import { render } from 'react-dom'
```

</details>

### Separate App into Modules

鼠标选中组件高亮显示，点击小灯泡(code action)，选择`move to a new file`，会将组件自动生成新文件并在当前文件进行导入

<details>
<summary>Pet.js</summary>

```js
import React from 'react'
export default function Pet({ name, animal, breed }) {
  return React.createElement('div', {}, [
    React.createElement('h1', {}, name),
    React.createElement('h2', {}, animal),
    React.createElement('h2', {}, breed)
  ])
}
```

</details>

## JSX

### Converting to JSX

[Babel 是一个 JavaScript 编译器](https://www.babeljs.cn/)

Pet.js:

```jsx
import React from 'react'
export default function Pet({ name, animal, breed }) {
  // return React.createElement('div', {}, [
  //   React.createElement('h1', {}, name),
  //   React.createElement('h2', {}, animal),
  //   React.createElement('h2', {}, breed)
  // ])

  // 下面的代码会被babel编译为上面的代码

  return (
    <div>
      <h1>{name}</h1>
      <h2>{animal}</h2>
      <h2>{breed}</h2>
    </div>
  )
}
```

### Configuring ESLint for React

```bash
npm install -D babel-eslint eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react
```

<details>
<summary>.eslintrc.json</summary>

```json
{
  "extends": [
    "eslint:recommended",
    "plugin:import/errors",
    "plugin:react/recommended",
    "plugin:jsx-a11y/recommended",
    "prettier",
    "prettier/react"
  ],
  "plugins": ["react", "import", "jsx-a11y"],
  "rules": {
    "react/prop-types": 0,
    "no-console": 1
  },
  "parserOptions": {
    "ecmaVersion": 2019,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "env": {
    "es6": true,
    "browser": true,
    "node": true
  },
  "settings": {
    "react": {
      "version": "detect"
    }
  }
}
```

</details>

### JSX Composite Components & Expressions

使用JSX语法重构App.js

<details>
<summary>App.js</summary>

```jsx
import React from 'react'
import { render } from 'react-dom'
import Pet from './Pet'

const App = () => {
  // return React.createElement('div', {}, [
  //   React.createElement('h1', {}, 'Adopt Me!'),
  //   React.createElement(Pet, {
  //     name: 'Luna',
  //     animal: 'Dog',
  //     breed: 'Havanese'
  //   }),
  //   React.createElement(Pet, {
  //     name: 'Pepper',
  //     animal: 'Bird',
  //     breed: 'Cockatiel'
  //   }),
  //   React.createElement(Pet, { name: 'Doink', animal: 'Cat', breed: 'Mix' })
  // ])

  return (
    <div>
      <h1>Adopt Me!</h1>
      <Pet name="Luna" animal="Dog" breed="Havanese" />
      <Pet name="Pepper" animal="Bird" breed="Cocktiel" />
      <Pet name="Doink" animal="Cat" breed="Mixed" />
    </div>
  )
}

render(/* React.createElement(App) */ <App />, document.getElementById('root'))

```

</details>

## Hooks

### Creating a Search Component

[notes](https://btholt.github.io/complete-intro-to-react-v5/hooks)

[Hooks in Depth](https://btholt.github.io/complete-intro-to-react-v5/hooks-in-depth)

```bash
touch ./src/SearchParams.js
```

<details>
<summary>SearchParams.js</summary>

```jsx
import React from 'react'

const SearchParams = () => {
  const location = 'Seattle, WA'

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input id="location" value={location} placeholder="location" />
        </label>
        <button>Submint</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

<details>
<summary>App.js</summary>

```jsx
import React from 'react'
import { render } from 'react-dom'
import SearchParams from './SearchParams'

const App = () => {
  return (
    <div>
      <h1>Adopt Me!</h1>
      <SearchParams />
    </div>
  )
}

render(<App />, document.getElementById('root'))

```

</details>

### Setting State with Hooks

在更改`input`标签内容时，可以发现并没有改变，这是因为react在设计时没有选择双向数据绑定，需要用户自己操作数据的状态，而不是由框架进行处理。

此时我们需要引入`hooks`进行数据双向绑定(所有`hook`均以`use`开头)

<details>
<summary>SearchParams.js</summary>

```jsx
import React, { useState } from 'react'

const SearchParams = () => {
  const [location, setLocation] = useState('Seattle, WA')

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="location"
            onChange={e => setLocation(e.target.value)}
          />
        </label>
        <button>Submint</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

### Best Practice for Hooks

Hooks never go inside of if statements, and they never go inside for loops or anything like that.

### Configuring ESLint for Hooks

```bash
npm i -D eslint-plugin-react-hooks
```

[eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)

<details>
<summary>.eslintrc.json</summary>

```json
"plugins": ["react", "import", "jsx-a11y", "react-hooks"],
"rules": {
  "react/prop-types": 0,
  "no-console": 1,
  "react-hooks/rules-of-hooks": 2,
  "react-hooks/exhaustive-deps": 1
},
```

</details>

开启之后将`hook`放入if语句中会看到如下警告:

> React Hook "useState" is called conditionally. React Hooks must be called in the exact same order in every component render.eslint(react-hooks/rules-of-hooks)

### Calling the Pet API

`parcel`可以自动安装引用的npm包而不必手动安装

所以即可使用以下命令

```bash
npm i @frontendmasters/pet
```

也可只在js文件头部添加

```js
import { ANIMALS } from '@frontendmasters/pet'
```

<details>
<summary>此时修改SearchParams.js如下</summary>

```jsx
import React, { useState } from 'react'
import { ANIMALS } from '@frontendmasters/pet'

const SearchParams = () => {
  const [location, setLocation] = useState('Seattle, WA')
  const [animal, setAnimal] = useState('dog')

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="location"
            onChange={e => setLocation(e.target.value)}
          />
        </label>
        <label htmlFor="animal">
          Animal
          <select
            id="animal"
            value={animal}
            onChange={e => setAnimal(e.target.value)}
            onBlur={e => setAnimal(e.target.value)}
          >
            <option>All</option>
            {ANIMALS.map(animal => (
              <option value={animal}>{animal}</option>
            ))}
          </select>
        </label>
        <button>Submit</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

### Unique List Item Keys

可以看到，此时lint提示我们要添加`属性，这是为了在diff算法进行时提升性能的措施。

<details>
<summary>此时修改SearchParams.js,给`<option>`添加`key`属性如下</summary>

```jsx
import React, { useState } from 'react'
import { ANIMALS } from '@frontendmasters/pet'

const SearchParams = () => {
  const [location, setLocation] = useState('Seattle, WA')
  const [animal, setAnimal] = useState('dog')

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="location"
            onChange={e => setLocation(e.target.value)}
          />
        </label>
        <label htmlFor="animal">
          Animal
          <select
            id="animal"
            value={animal}
            onChange={e => setAnimal(e.target.value)}
            onBlur={e => setAnimal(e.target.value)}
          >
            <option>All</option>
            {ANIMALS.map(animal => (
              <option value={animal} /*这里添加了key属性*/key={animal}>
                {animal}
              </option>
            ))}
          </select>
        </label>
        <button>Submit</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

### Breed Dropdown

在form中添加breed选择标签

<details>
<summary>searchParams.js</summary>

```jsx
import React, { useState } from 'react'
import { ANIMALS } from '@frontendmasters/pet'

const SearchParams = () => {
  const [location, setLocation] = useState('Seattle, WA')
  const [animal, setAnimal] = useState('dog')
  const [breed, setBreed] = useState('')
  const [breeds, setBreeds] = useState([])

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="location"
            onChange={e => setLocation(e.target.value)}
            onBlur={e => setLocation(e.target.value)}
          />
        </label>
        <label htmlFor="animal">
          Animal
          <select
            id="animal"
            value={animal}
            onChange={e => setAnimal(e.target.value)}
            onBlur={e => setAnimal(e.target.value)}
          >
            <option>All</option>
            {ANIMALS.map(animal => (
              <option value={animal} key={animal}>
                {animal}
              </option>
            ))}
          </select>
        </label>
        <label htmlFor="breed">
          breed
          <select
            id="breed"
            value={breed}
            onChange={e => setBreed(e.target.value)}
            onBlur={e => setBreed(e.target.value)}
            disabled={breeds.length === 0}
          >
            <option>All</option>
            {breeds.map(breedString => (
              <option key={breedString} value={breedString}>
                {breedString}
              </option>
            ))}
          </select>
        </label>
        <button>Submit</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

### Custom Hooks

把animal标签和breed标签通过custom hooks抽离成公共方法

<details>
<summary>新建useDropdown.js</summary>

```jsx
import React, { useState } from 'react'

const useDropdown = (label, defaultState, options) => {
  const [state, setState] = useState(defaultState)
  const id = `use-dropdown-${label.replace('', '').toLowerCase()}`
  const Dropdown = () => (
    <label htmlFor={id}>
      {label}
      <select
        id={id}
        value={state}
        onChange={e => setState(e.target.value)}
        onBlur={e => setState(e.target.value)}
        disabled={options.length === 0}
      >
        <option>All</option>
        {options.map(item => (
          <option key={item} value={item}>
            {item}
          </option>
        ))}
      </select>
    </label>
  )

  return [state, Dropdown, setState]
}

export default useDropdown

```

</details>

<details>
<summary>修改SearchParams.js</summary>

```jsx
import React, { useState } from 'react'
import { ANIMALS } from '@frontendmasters/pet'
import useDropdown from './useDropdown'

const SearchParams = () => {
  const [location, setLocation] = useState('Seattle, WA')
  const [breeds, setBreeds] = useState([])
  // const [animal, setAnimal] = useState('dog')
  // const [breed, setBreed] = useState('')
  const [animal, AnimalDropdown] = useDropdown('Animal', 'dog', ANIMALS)
  const [breed, BreedDropdown] = useDropdown('Breed', '', breeds)

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="location"
            onChange={e => setLocation(e.target.value)}
            onBlur={e => setLocation(e.target.value)}
          />
        </label>
        {/* <label htmlFor="animal">
          Animal
          <select
            id="animal"
            value={animal}
            onChange={e => setAnimal(e.target.value)}
            onBlur={e => setAnimal(e.target.value)}
          >
            <option>All</option>
            {ANIMALS.map(animal => (
              <option value={animal} key={animal}>
                {animal}
              </option>
            ))}
          </select>
        </label>
        <label htmlFor="breed">
          breed
          <select
            id="breed"
            value={breed}
            onChange={e => setBreed(e.target.value)}
            onBlur={e => setBreed(e.target.value)}
            disabled={breeds.length === 0}
          >
            <option>All</option>
            {breeds.map(breedString => (
              <option key={breedString} value={breedString}>
                {breedString}
              </option>
            ))}
          </select>
        </label> */}
        <AnimalDropdown />
        <BreedDropdown />
        <button>Submit</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

## Effects

### useEffect

[petfinder](https://www.petfinder.com/)

<details>
<summary>SearchParams.js</summary>

```jsx
import React, { useState, useEffect } from 'react'
import pet, { ANIMALS } from '@frontendmasters/pet'
import useDropdown from './useDropdown'

const SearchParams = () => {
  const [location, setLocation] = useState('Seattle, WA')
  const [breeds, setBreeds] = useState([])
  const [animal, AnimalDropdown] = useDropdown('Animal', 'dog', ANIMALS)
  const [breed, BreedDropdown] = useDropdown('Breed', '', breeds)

  useEffect(() => {
    setBreeds([])
    setBreed('')

    pet.breeds(animal).then(({ breeds }) => {
      const breedStrings = breeds.map(({ name }) => name)
      setBreeds(breedStrings)
    }, console.error)
  })

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="location"
            onChange={e => setLocation(e.target.value)}
            onBlur={e => setLocation(e.target.value)}
          />
        </label>
        <AnimalDropdown />
        <BreedDropdown />
        <button>Submit</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

### Declaring Effect Dependancies

上述代码中，页面每次重新渲染都会从api重新获取数据，根据需求这明显没有必要，此时需要给`useEffect`添加参数，声明依赖，通过给`useEffect`传入一个数组作为第二个参数，数组的每一项为状态改变后需要从api重新获取数据的变量。

<details>
<summary>SearchParams.js</summary>

```jsx
import React, { useState, useEffect } from 'react'
import pet, { ANIMALS } from '@frontendmasters/pet'
import useDropdown from './useDropdown'

const SearchParams = () => {
  const [location, setLocation] = useState('Seattle, WA')
  const [breeds, setBreeds] = useState([])
  const [animal, AnimalDropdown] = useDropdown('Animal', 'dog', ANIMALS)
  const [breed, BreedDropdown, setBreed] = useDropdown('Breed', '', breeds)

  useEffect(() => {
    setBreeds([])
    setBreed('')

    pet.breeds(animal).then(({ breeds }) => {
      const breedStrings = breeds.map(({ name }) => name)
      setBreeds(breedStrings)
    }, console.error)
  }, [animal, setBreed, setBreeds])

  return (
    <div className="search-params">
      <form>
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="location"
            onChange={e => setLocation(e.target.value)}
            onBlur={e => setLocation(e.target.value)}
          />
        </label>
        <AnimalDropdown />
        <BreedDropdown />
        <button>Submit</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

### Effect Lifecycle Walkthrough

在`SeacrhParmas.js`中，`useEffect`是一个异步操作，在首次渲染中并不会执行，以提高首次渲染的速度。

在页面首次渲染完成后，会调用`useEffect`的第一个参数，也就是更新`breed`和`breeds`的函数。在执行完毕后，会监听`animal`,`setBreed`和`setBreeds`，也就是`useEffect`第二个参数的数组中的每一项的数据状态有没有改变，只有当参数数组中的数据状态改变时，才会重新调用第一个参数函数更新`breed`和`breeds`的数据。

### Run Only Once

如果只想在首次加载时执行`useEffect`并只执行一次，可以将`useEffect`第二个参数设置为一个空数组。如果想在每次重渲染后执行，只设置一个参数即可（此时会一直调用api获取数据，不要这样做）。

## Dev Tools

### Environment Variables & Strict Mode

#### `NODE_ENV=development`

> React already has a lot of developer conveniences built into it out of the box. What's better is that they automatically strip it out when you compile your code for production.
> So how do you get the debugging conveniences then? Well, if you're using Parcel.js, it will compile your development server with an environment variable of `NODE_ENV=development` and then when you run `parcel build <entry point>` it will automatically change that to `NODE_ENV=production` which is how all the extra weight gets stripped out.
> Why is it important that we strip the debug stuff out? The dev bundle of React is quite a bit bigger and quite a bit slower than the production build. Make sure you're compiling with the correct environmental variables or your users will suffer.

#### Strict Mode

> React has a new strict mode. If you wrap your app in `<React.Strict></React.Strict>` it will give you additional warnings about things you shouldn't be doing. I'm not teaching you anything that would trip warnings from `React.Strict` but it's good to keep your team in line and not using legacy features or things that will be sooned be deprecated.

#### React Browser Dev Tools

> React has wonderful dev tools that the core team maintains. They're available for both Chromium-based browsers and Firefox. They let you do several things like explore your React app like a DOM tree, modify state and props on the fly to test things out, tease out performance problems, and programtically manipulate components. Definitely worth downloading now.

[Firefox React Dev Tools](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)

[Chrome React Dev Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)

## Async & Routing

### Asynchronous API Requests

[notes](https://btholt.github.io/complete-intro-to-react-v5/async)

[course async await](https://frontendmasters.com/courses/js-recent-parts/async-functions/)

<details>
<summary>SearchParams.js</summary>

```jsx
import React, { useState, useEffect } from 'react'
import pet, { ANIMALS } from '@frontendmasters/pet'
import useDropdown from './useDropdown'

const SearchParams = () => {
  const [location, setLocation] = useState('Seattle, WA')
  const [breeds, setBreeds] = useState([])
  const [animal, AnimalDropdown] = useDropdown('Animal', 'dog', ANIMALS)
  const [breed, BreedDropdown, setBreed] = useDropdown('Breed', '', breeds)
  const [pets, setPets] = useState([]) // add this
  
  // add this
  async function requestPets() {
    const { animals } = await pet.animals({
      location,
      breed,
      type: animal
    })

    setPets(animals || [])
  }

  useEffect(() => {
    setBreeds([])
    setBreed('')

    pet.breeds(animal).then(({ breeds }) => {
      const breedStrings = breeds.map(({ name }) => name)
      setBreeds(breedStrings)
    }, console.error)
  }, [animal, setBreed, setBreeds])

  return (
    <div className="search-params">
      <form
        onSubmit={e => { //add this
          e.preventDefault()
          requestPets()
        }}
      >
        <label htmlFor="location">
          Location
          <input
            id="location"
            value={location}
            placeholder="location"
            onChange={e => setLocation(e.target.value)}
            onBlur={e => setLocation(e.target.value)}
          />
        </label>
        <AnimalDropdown />
        <BreedDropdown />
        <button>Submit</button>
      </form>
    </div>
  )
}

export default SearchParams

```

</details>

<details>
<summary>在package.json添加浏览器支持</summary>

```json
{
  "name": "adopt-me",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "parcel src/index.html",
    "format": "prettier \"src/**/*.{js,html}\" --write",
    "lint": "eslint \"src/**/*.{js,jsx}\" --quiet",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-eslint": "^10.0.1",
    "eslint": "^5.16.0",
    "eslint-config-prettier": "^4.3.0",
    "eslint-plugin-import": "^2.17.3",
    "eslint-plugin-jsx-a11y": "^6.2.1",
    "eslint-plugin-react": "^7.13.0",
    "eslint-plugin-react-hooks": "^1.6.0",
    "parcel-bundler": "^1.12.3",
    "prettier": "^1.17.1"
  },
  "dependencies": {
    "@frontendmasters/pet": "^1.0.3",
    "react": "^16.8.6",
    "react-dom": "^16.8.6"
  },
  "browserslist": [
    "last 2 Chrome versions",
    "last 2 ChromeAndroid versions",
    "last 2 Firefox versions",
    "last 2 FirefoxAndroid versions",
    "last 2 Safari versions",
    "last 2 iOS versions",
    "last 2 Edge versions",
    "last 2 Opera versions",
    "last 2 OperaMobile versions"
  ]
}

```

</details>

### Use the Fallback Mock API

之前的数据都是通过互联网请求获取的，下面安装mock API，从本地获取数据

首先安装npm包

```bash
npm i -D cross-env
```

给package.json添加如下指令

```json
"dev:mock": "cross-env PET_MOCK=mock npm run dev"
```

### One-Way Data Flow

新建`Results`组件，更改`SearchParams.js`如下

```js
// at top
import Results from "./Results";

// under </form>, still inside the div
<Results pets={pets} />;
```

React的数据会从父组件传递到子组件(One-Way Data Flow)，反之并不成立。

<details>
<summary>新建`Results.js`</summary>

```jsx
import React from 'react'
import Pet from './Pet'

const Results = ({ pets }) => {
  return (
    <div className="search">
      {pets.length === 0 ? (
        <h1>No Pets Found</h1>
      ) : (
        pets.map(pet => (
          <Pet
            animal={pet.type}
            key={pet.id}
            name={pet.name}
            breed={pet.breeds.primary}
            media={pet.photos}
            location={`${pet.contact.address.city},${
              pet.contact.address.state
            }`}
            id={pet.id}
          />
        ))
      )}
    </div>
  )
}

export default Results

```

</details>

### Reformatting the Pet Component

<details>
<summary>Pet.js</summary>

```jsx
import React from 'react'
export default function Pet({ name, animal, breed, media, location, id }) {
  let hero = 'http://placecorgi.com/300/300'
  if (media.length) {
    hero = media[0].small
  }

  return (
    <a href={`/details/${id}`} className="pet">
      <div className="image-container">
        <img src={hero} alt={name} />
      </div>
      <div className="info">
        <h1>{name}</h1>
        <h2>{`${animal} - ${breed} - ${location}`}</h2>
      </div>
    </a>
  )
}

```

</details>

### Reach Router

[Navi-Declarative, asynchronous routing for React.](https://frontarm.com/navi/en/)

[react-router](https://reacttraining.com/react-router/)

[reach/router](https://github.com/reach/router)

```bash
touch ./src/Details.js
```

<details>
<summary>Details.js</summary>

```jsx
import React from 'react'

const Details = () => {
  return <h1>Details</h1>
}

export default Details

```

</details>

<details>
<summary>App.js</summary>

```jsx
// at top
import { Router } from "@reach/router";
import Details from "./Details";

// replace <Results />
<Router>
  <SearchParams path="/" />
  <Details path="/details/:id" />
</Router>;
```

</details>

### Debugging & Reach Router Link

可以在`Details.js`中，使用`<pre>`和`<code>`标签包裹`Json.stringify()`在页面显示变量对象，也可以在DevTools中查看

```jsx
const Details = props => {
  return (
    <pre>
      <code>{JSON.stringify(props, null, 2)}</code>
    </pre>
  )
}
```

更改`app.js`，添加`<link>`

```jsx
// import Link too
import { Router, Link } from "@reach/router";

// replace h1
<header>
  <Link to="/">Adopt Me!</Link>
</header>;
```

## Class Components

### React Class Components

<details>
<summary>Details.js</summary>

```jsx
import React from 'react'
import pet from '@frontendmasters/pet'

class Details extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      loading: true
    }
  }
  // 不能在class中使用hooks
  componentDidMount() {
    pet.animal(this.props.id).then(({ animal }) => { // 为了保持this的指向，这里最好使用箭头函数
      this.setState({
        name: animal.name,
        animal: animal.type,
        location: `${animal.contact.address.city} , ${
          animal.contact.address.state
        }`,
        description: animal.description,
        media: animal.photos,
        breed: animal.breeds.primary,
        loading: false
      })
    })
  }
  render() {
    //每个class component都必须有一个render方法
    return
  }
}

export default Details

```

</details>

### Rendering the Component

<details>
<summary>Details.js</summary>

```jsx
import React from 'react'
import pet from '@frontendmasters/pet'

class Details extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      loading: true
    }
  }
  // 不能在class中使用hooks
  componentDidMount() {
    pet
      .animal(this.props.id)
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
          loading: false
        })
      })
      .catch(err => this.setState({ error: err }))
  }
  render() {
    //每个class component都必须有一个render方法
    if (this.state.loading) {
      return <h1>loading … </h1>
    }

    const { animal, breed, location, description, name } = this.state

    return (
      <div className="details">
        <div>
          <h1>{name}</h1>
          <h2>{`${animal} — ${breed} — ${location}`}</h2>
          <button>Adopt {name}</button>
          <p>{description}</p>
        </div>
      </div>
    )
  }
}

export default Details

```

</details>

### Configuring Babel for Parcel

更具js的class语法，在class component组件中要加入如下代码：

```jsx
class Details extends React.component {
  constructor(props) {
    super(props)

    this.state = {
      loading: true
    }
  }
}

```

毫无疑问，这会加重我们的心智负担，我们希望将上述代码直接改写如下：

```jsx
class Details extends React.component {
  state = {
    loading: true
  }
}
```

直接运行会报错

```bash
Support for the experimental syntax 'classProperties' isn't currently enabled (5:9)
```

所以要启用babel和parcel的一些设置。

```bash
npm install -D babel-eslint @babel/core @babel/preset-env @babel/plugin-proposal-class-properties @babel/preset-react
```

```bash
touch ./.babelrc
```

<details>
<summary>.babelrc</summary>

```json
{
  "presets": ["@babel/preset-react", "@babel/preset-env"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

</details>

在`.eslintrc.json`中添加`"parser": "babel-eslint",`

上述步骤完成后，就可以启用这个新语法了。

### Creating an Image Carousel

<details>
<summary>Carousel.js</summary>

```jsx
import React from 'react'

class Carousel extends React.Component {
  state = {
    photos: [],
    active: 0
  }

  static getDerivedStateFromProps({ media }) {
    let photos = ['http://placecorgi.com/600/600']
    if (media.length) {
      photos = media.map(({ large }) => large)
    }
    return { photos }
  }
  render() {
    const { photos, active } = this.state

    return (
      <div className="carousel">
        <img src={photos[active]} alt="an animal" />
        <div className="carousel-smaller">
          {photos.map((photo, index) => (
            // eslint-disable-next-line
            <img
              key={photo}
              onClick={this.handelIndexClick}
              data-index={index}
              src={photo}
              className={index === active ? 'active' : ''}
              alt="animal thumbnail"
            />
          ))}
        </div>
      </div>
    )
  }
}

export default Carousel

```

</details>

### Context Problem

在`Carousel.js`中，如果我们直接将`handelIndexClick`方法写为如下代码：

```jsx
class Carousel extends React.Component {
  state = {
    photos: [],
    active: 0
  }
  handelIndexClick(event) {
    this.setState({ // 这里的this指向的不是Carousel组件，而是其他的什么东西
      active: event.target.dataset.index // 这里Dom的dataset返回的属性会是字符串，需要转换为数字
    })
  }
}
```

会有如上两个问题。

所以可以将如上代码改写为

```jsx
class Carousel extends React.Component {
  constructor (props) {
    super(props)
    this.state = {
      photos: [],
      active: 0
    }
    this.handelIndexClick = this.handelIndexClick.bind(this) // 显式绑定this指向
  }
  handelIndexClick(event) {
    this.setState({
      active: +event.target.dataset.index // 通过+运算符，强制类型转换为数字
    })
  }
}
```

我们可以用更为简洁的写法

```jsx
class Carousel extends React.Component {
  state = {
    photos: [],
    active: 0
  }
  handelIndexClick = event => {
    this.setState({
      active: +event.target.dataset.index
    })
  }
}
```

因为箭头函数并不会创造新的执行上下文，而是会继承上级作用域函数的执行上下文，所以这里的this指向了Carousel

*所以当传入函数进入子函数或者进行事件监听时，使用箭头函数，来避免this指向为其他对象*。

在React中，`componentDidMount`和`render`方法的`this`指向的执行上下文已经为当前组件。

### Carousel Implementation

将Carousel组件添加到Detail页面：

```jsx
// import at top
import Carousel from "./Carousel";

// first component inside div.details
<Carousel media={media} />;
```

### Error Boundaries

[错误边界（Error Boundaries）](https://zh-hans.reactjs.org/docs/error-boundaries.html#introducing-error-boundaries)

```bash
touch ./src/ErrorBoundary.js
```

<details>
<summary>ErrorBoundary.js</summary>

```jsx
// mostly code from https://zh-hans.reactjs.org/docs/error-boundaries.html

import React from 'react'
import { Link } from '@reach/router'

class ErrorBoundary extends React.Component {
  state = {
    hasError: false
  }

  static getDerivedStateFromError() {
    // Update state so the next render will show the fallback UI.
    return { hasError: true }
  }

  componentDidCatch(error, info) {
    // You can also log the error to an error reporting service
    console.error('ErrorBoundary caught an error', error, info)
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return (
        <h1>
          There was an error with this listing. <Link to="/">Click here</Link>{' '}
          to back to the home page or wait five seconds.
        </h1>
      )
    }

    return this.props.children
  }
}

export default ErrorBoundary

```

</details>

### Error Boundary Middleware

Details.js:

```jsx
// add import

// replace export
export default function DetailsWithErrorBoundary(props) {
  return (
    <ErrorBoundary>
     {/* 使用扩展运算符传递props对象参数 */}
      <Details {...props} />
    </ErrorBoundary>
  );
}
```

### 404 Page Redirect

```jsx
// top
import { Link, Redirect } from "@reach/router";

// add redirect
this.state = { hasError: false, redirect: false };

// under componentDidCatch
componentDidUpdate() {
  if (this.state.hasError) {
    setTimeout(() => this.setState({ redirect: true }), 5000);
  }
}

// first thing inside render
if (this.state.redirect) {
  return <Redirect to="/" />;
}
```

## Context

### React Context

[Notes](https://btholt.github.io/complete-intro-to-react-v5/context)

> What is context? Context is like state, but instead of being confined to a component, it's global to your application. It's application-level state. This is dangerous. Avoid using context until you have to use it. One of React's primary benefit is it makes the flow of data obvious by being explicit. This can make it cumbersome at times but it's worth it because your code stays legible and understandable. Things like context obscure it.
> Context (mostly) replaces Redux. Well, typically. It fills the same need as Redux. I really can't see why you would need to use both. Use one or the other.

[redux](https://redux.js.org/)

[Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。](https://zh-hans.reactjs.org/docs/context.html)

```bash
touch ./src/ThemeContext.js
```

<details>
<summary>ThemeContext.js</summary>

```jsx
import { createContext } from 'react'

const ThemeContext = createContext(['green', () => {}])

export default ThemeContext

```

</details>

<details>
<summary>App.js</summary>

```jsx
// import useState and ThemeContext
import React, { useState } from "react";
import ThemeContext from "./ThemeContext";

// top of App function body
const theme = useState("darkblue");

// wrap the rest of the app
<ThemeContext.Provider value={theme}>
  […]
</ThemeContext>
```

</details>

### Context with Hooks

<details>
<summary>SearchParams.js</summary>

```jsx
// import at top
import ThemeContext from "./ThemeContext";

// top of SearchParams function body
const [theme] = useContext(ThemeContext);

// replace button
<button style={{ backgroundColor: theme }}>Submit</button>;
```

</details>

### Context with Classes

<details>
<summary>Details.js</summary>

```jsx
// import
import ThemeContext from "./ThemeContext";

// replace button
<ThemeContext.Consumer>
  {([theme]) => (
    <button style={{ backgroundColor: theme }} onClick={this.toggleModal}>
      Adopt {name}
    </button>
  )}
</ThemeContext.Consumer>;
```

</details>

### Persisting State with Context Hooks

<details>
<summary>SearchParams.js</summary>

```jsx
// also grab setTheme
const [theme, setTheme] = useContext(ThemeContext);

// below BreedDropdown
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
</label>;
```

</details>

同时要修改`Pet.js`，把`<a>`链接更改为`<link>`，不然会重新打开网页，无法保存状态。

## Portals

### Modal Dialog with Portals

在index.html中添加一个单独的挂载点

```html
<!-- above #root -->
<div id="modal"></div>
```

创建一个Modal.js

<details>
<summary>Modal.js</summary>

```jsx
import React, { useEffect, useRef } from 'react'
import { createPortal } from 'react-dom'

const Modal = ({ children }) => {
  const elRef = useRef(null)

  if (!elRef.current) {
    elRef.current = document.createElement('div')
  }

  useEffect(() => {
    const modalRoot = document.getElementById('modal')
    modalRoot.appendChild(elRef.current)

    return () => modalRoot.removeChild(elRef.current)
  }, [])

  return createPortal(<div>{children}</div>, elRef.current)
}

export default Modal

```

</details>

### Displaying the Modal

<details>
<summary>修改Details.js</summary>

```jsx
// at the top
import { navigate } from "@reach/router";
import Modal from "./Modal";

// add showModal
state = { loading: true, showModal: false };

// add setState inside componentDidMount
url: animal.url,
  // above render
  (toggleModal = () => this.setState({ showModal: !this.state.showModal }));
adopt = () => navigate(this.state.url);

// add showModal
const {
  media,
  animal,
  breed,
  location,
  description,
  name,
  url,
  showModal
} = this.state;

// below description
{
  showModal ? (
    <Modal>
      <div>
        <h1>Would you like to adopt {name}?</h1>
        <div className="buttons">
          <button onClick={this.adopt}>Yes</button>
          <button onClick={this.toggleModal}>No</button>
        </div>
      </div>
    </Modal>
  ) : null;
}
```

</details>
