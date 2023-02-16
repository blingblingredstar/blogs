---
title: Webpack 4 Fundamentals
date: 2019-07-16 10:18:18
categories: 学习笔记
tags: [FrontEndMasters, commonJS, Webpack, ESM]
---

## Why Webpack

[slides](https://docs.google.com/presentation/d/1hFtMCMo62DgOIc-9OwgaVwPZHwv1cgMELArHcMbXlSI/edit)

### Problems with Script Loading

There is only two ways that you can actually make JS to work in borwser.

1. Adding a script tag for passing a source.
2. Write your job description to HTML.

Problems:

+ Doesn't scale
+ Too many scripts
  每个浏览器都有不同的同时请求数量的限制（针对HTTP1.1，HTTP2等协议除外）。
+ Unmaintainable scripts
+ Scope
+ Size
+ Readability
+ Fragility
+ Monolith files

Solution:

+ IIFE(Immediately-Invoked-Function-Expression 立即执行函数)
+ Module(Treat Each File as IIFE)
  + We can “safely” combine files without concern of scope collision!*
  + Make, Grunt, Gulp, Broccoli, Brunch, StealJS
<!-- more -->
Problem:

+ Full rebuilds everywhere
+ Dead code - Concat doesn't help tie usages across files
+ Lots of IIFE's are slow
+ Can't dynamic loading

### History of Modules

The birth of JavaScript module happened kind of around this early period thanks to **Node.js**.

+ common.js(Modules 1.0)
  + Use syntax called `require`, which allows you to inject other pieces of module into the current module.
  + Static analysis
  + NPM + Node + Modules

Problems:

+ **No browser support** for common.js
+ No live bindings - Problems with circular references
+ Synchronous module resolution, loader(Slow)

Solution:

+ Bundlers / Linkers
  + browserify(static)
  + requireJS(loader)
  + systemJS(loader)

Problems:

commonJS syntax:

```js
// loading module
const _ = require('lodash')

// declaring module
module.export = someValue
```

+ No static async / lazy loading(all bundles up front)
+ commonJS bloat too dynamic
+ Not everyone was shipping commonJS

AMD syntax:

```js
define('myAwesomeLib',['ladash', 'someDep'], function(_, someDep) {
  return {...}
})
```

AMD + commonJS:

```js
define(function(require, exports, module) {
  const _ = require('lodash')

  // do things...
  module.exports = someLib
})
```

Problems:

+ Too dynamic of lazy loading(momentJS)
+ Awkward now standard syntax(No real module system)

### EcmaScript Modules(ESM)

```js
import {uniq, forOf, bar} from 'lodash-es'
import * as utils from 'utils'

export const uniqConst = uniq([1, 2, 2, 4])
```

Benefit:

+ Reusable
+ Encapsulated
+ Organized
+ Convenient

Problems:

+ ESM for node?
+ How does they work in the browser?(Incredibly slow)

### Introducing Webpack

Every library is different... Library authors use the module types that they like and choose.

And this is just for JavaScript... Each and every other filetype until now has had to  have specific ways to process it.

Wouldn't it be nice...

Webpack is a module bundler

+ Lets you write **any** module format(**mixed**!), compiles then for *the borwser*.
+ Supports static async bundling
+ Rich, vast, ecosystem
+ The most performant way to ship JavaScript today

### Configuring Webpack

Webpack - How to use it?

1. Config - (webpack.config.js) Yes, it's a module too!

   ```js
   module.exports = {
     entry: {
       vendor: './src/vendor.ts',
       main: './src/main.browser.ts'
     },
     output: {
       path: 'dist/',
       filename: '[name].bundle.js'
     }
   }
   ```

2. Webpack CLI

   ```bash
   webpack <entry,js> <results.js> --colors --progress

   webpack-dev-server --port=9000
   ```

3. Node API

   ```js
   const webpack = require('webpack')

   // returns a conpiler instance
   webpack({
     // configuration object here!
   }, function(err, stats) {
     //...
     // compilerCallback
     console.log(err)
   })
   ```

## Webpack from Scratch

### Using Webpack for the First Time

[Webpack Academy Courses for Frontend Masters](https://github.com/thelarkinn/webpack-workshop-2018)

`git checkout feature/01-fem-first-script`

`npm i`

`npm run webpack`

### Adding npm Scripts for Environment Builds

Webpack by default, before Webpack 4, you really were only required to specify two properties, your input and your output.

Webpack look for entry property, we default to `./src/index.js`.

直接使用默认的设置`npm run webpack`会提示设置模式，

下面在`package.json`中`scripts`中添加命令

<!-- markdownlint-disable md033 -->
<details>
<summary>package.json</summary>

```js
{
  "scripts": {
    "webpack": "webpack",
    "dev": "npm run webpack -- --mode development",
    "prod": "npm run webpack -- --mode production"
  }
}
```

</details>

开发模式`npm run dev`

生产模式`npm run prod`

在node中进行debug：

```bash
git checkout feature/03-fem-debug-script --force
```

### Setting Up Debugging

可以在`package.json`中的`scripts`字段添加以下命令

`"debugthis": "node --inspect --inspect-brk ./src/index.js"`

### Coding Your First Module

`touch ./src/nav.js`

在nav.js导出模块

```js
export default 'nav'
```

在index.js引入模块

```js
import nav from './nav'

console.log(nav)
```

`npm run prod`

`node ./dist/main.js`

可以看到控制台输出`nav`

### Adding Watch Mode

在package.json中的scripts中添加

`"dev": "npm run webpack -- --mode development --watch",`

`npm run dev`

修改nav.js如下

```js
export default () => 'nav'
```

index.js

```js
import nav from './nav'

console.log(nav())
```

可以看到控制台显示了webpack进行了重新编译

### ES Module Syntax

`touch ./src/footer.js`

footer.js:

```js
export const top = 'top'
export const bottom = 'bottom'
```

index.js:

```js
import nav from './nav'
import { top, bottom } from './footer'

console.log(nav(), top, bottom)
```

`npm run prod`

`node ./dist/main.js`可以看到输出`nav top bottom`

### CommonJS Export

`touch ./src/button.js`

button.js:

```js
// take a str, the button label and return a element
module.exports = buttonName => {
  return `Button: ${buttonName}`
}
```

index.js:

```js
import nav from './nav'
import { top, bottom } from './footer'
import makeButton from './button'

console.log(nav(), top, bottom, makeButton('My first button!'))
// nav top bottom Button: My first button!
```

### CommonJS Named Exports

`touch ./src/button-style.js`

可以修改footer.js模块导出语法如下：

```js
const top = 'top'
const bottom = 'bottom'

export { top, bottom } // ESM语法
```

button-style.js:

```js
const red = 'color: red',
  blue = 'color: bule',
  makeColorSytle = color => `Color: ${color}`

exports.red = red
exports.blue = blue
exports.makeColorSytle = makeColorSytle // commonJS语法
```

button.js:

```js
// 添加JSDoc文档
/**
 * @输入字符串返回dom元素
 * @param {string} buttonName
 * @returns {Element}
 */
// take a str, the button label and return a element
const makeButton = buttonName => {
  return `Button: ${buttonName}`
}

module.exports = makeButton
```

index.js:

```js
import nav from './nav'
import { top, bottom } from './footer'
import makeButton from './button'
import { makeColorSytle } from './button-style'

console.log(
  nav(),
  top,
  bottom,
  makeButton('My first button!'),
  makeColorSytle('cyan')
)
```

### Tree Shaking

`npm run prod`

在导出的main.js中，可以发现并没有red或者blue字符串。

这叫做dead code elimination或者tree shaking.

webpack只会在最终打包使用的模块。

`touch ./webpack.config.js`

### Webpack Bundle Walkthrough

通过观察导出的main.js我们可以看到最终生成了一个IIFEs，其中IIFE的参数为一个数组，参数数组的每一个元素都是一个函数，

## Webpack Core Concepts

### Webpack Entry

The first JavaScript file to load to "Kick-off" your app.

Webpack uses this as the starting point.

Entry tells webpack what(files) to load for the browser; Compliments the output property.

```js
//webpack.config.js
module.exports = {
  entry: ‘./browser.main.ts’,
  //...
}

//browser.main.ts
import {Component} from ‘@angular/core’;

import {App} from ‘./app.component’;

bootstrap(App,[]);

//app.component.ts
@Component({...})
export class App {};
```

### Output & Loaders

#### Output

Tells webpack where and how to discribute bundles(compilations). Work with Entry.

```js
//webpack.config.js
module.exports = {
  entry: ‘./browser.main.ts’,
  output: {
    path: ‘./dist’,
    filename: ‘./bundle.js’,
  },
  //...
}

//Generates bundle.js
```

#### Loaders + Rules

Loaders tells webpack how to modify files before its added to dependency graph

Loaders are also JavaScript modules(functions) that takes the source file, and returns it in a [modified] state.

```js

module: {
  rules: [
    {test: /\.ts$/, use: ‘ts-loader’},
    {test: /\.js$/, use: ‘babel-loader’},
    {test: /\.css$/, use: ‘css-loader’}
  ],
}
```

```js
module: {
  rules: [
    {
      test: regex,
      use: (Array|String|Function)
      include: RegExp[],
      exclude: RegExp[],
      issuer: (RegExp|String)[],
      enforce: “pre”|”post”
    },
  ],
}
```

+ test
  + A regular expression that instructs the compiler which files to run the loader against.
+ use
  + An array/string/function that returns loader objects.
+ enforce
  + Can be “pre” or “post”, tells webpack to run this rule before or after all other rules.
+ include
  + An array of regular expression that instruct the compiler which folders/files to include. Will only search paths provided with the include.
+ exclude
  + An array of regular expression that instructs the compiler which folders/files to ignore.

```js
module: {
  rules: [
    {
      test: /\.ts$/,
      use: [
        ‘awesome-typescript-loader’,
        ‘ng2-asset-loader`
      ],
      include: /some_dir_name/,
      exclude: [/\.(spec|e2e)\.ts$/],
    }
  ]
}
```

### Chainning Loaders

```js
rules: [
  {
  test: /\.less$/,
  use:[’style’,’css’,’less’]
  }
]
```

这里参数数组传入的顺序为从右到左，可以理解为`style(css(less()))`，既函数从内至外执行。

#### Loaders

Tells webpack how to interpret and translate files. Transformed on a per-file basis before adding to the dependency graph

### Webpack Plugins

+ Objects(with an 'apply' property)
+ Allow you to hook into the entire compilation lifecycle
+ webpack has a variety of built in plugins

Basic plugin example:

```js
function BellOnBundlerErrorPlugin () { }

BellOnBundlerErrorPlugin.prototype.apply = function(compiler) {
  if (typeof(process) !== 'undefined') {

    // Compiler events that are emitted and handled
    compiler.plugin('done', function(stats) {
      if (stats.hasErrors()) {
        process.stderr.write('\x07');
      }
    });

    compiler.plugin('failed', function(err) {
      process.stderr.write('\x07');
    });
  
  }
}

module.exports = BellOnBundlerErrorPlugin;
```

A plugin is an ES5 ‘class’ which implements an apply function.

The compiler uses it to emit events.

How to use Plugins

```js
// require() from node_modules or webpack or local file
var BellOnBundlerErrorPlugin = require(‘bell-on-error’);
var webpack = require(‘webpack’);

module.exports = {
  //...
  plugins: [
  new BellOnBundlerErrorPlugin(),
  
  // Just a few of the built in plugins
  new webpack.optimize.CommonsChunkPlugin(‘vendors’),
  new webpack.optimize.UglifyJsPlugin()
  ]
  //...
}
```

require() plugin from node_modules into config.

add new instance of plugin to plugins key in config object.

provide additional info for arguments

#### Plugins

Adds additional functionality to Compilations(optimaized bundled modules).

More powerful w/ more access to CompilerAPI. Does everything else you'd ever want to in webpack.

### Webpack Config

`git checkout feature/0310-add-first-config-mode-none -f`

```js
module.exports = () => ({
  output: {
    filename: 'bundle.js'
  }
})
```

### Passing Varible to Webpack Config

修改package.json中scripts，在mode前加`env.`：

```json
 "scripts": {
    "webpack": "webpack",
    "debug": "node --inspect --inspect-brk ./node_modules/webpack/bin/webpack.js",
    "prod": "npm run webpack -- --env.mode production",
    "dev": "npm run webpack -- --env.mode development --watch",
    "prod:debug": "npm run debug -- --env.mode production",
    "dev:debug": "npm run debug -- --env.mode development"
  },
```

修改webpack.config.js，添加env参数

```js
module.exports = env => {
  console.log(env) // { mode: 'production' }
  return {
    output: {
      filename: 'bundle.js'
    }
  }
}
```

```js
module.exports = env => {
  return {
    mode: env.mode,
    output: {
      filename: 'bundle.js'
    }
  }
}
```

也可以使用对象解构赋值写为如下：

```js
module.exports = ({ mode }) => {
  console.log(env)
  return {
    mode,
    output: {
      filename: 'bundle.js'
    }
  }
}
```

### Adding Webpack Plugins

`npm i html-webpack-plugin -D`

`mkdir ./build-utils`

```js
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = ({ mode }) => {
  return {
    mode,
    output: {
      filename: 'bundle.js'
    },
    plugins: [new HtmlWebpackPlugin(), new webpack.ProgressPlugin()]
  }
}
```

`npm run prod`

可以看到生成了index.html

### Setting Up a Local Development Server

`git checkout feature/0311-add-first-plugins -f`

`npm i webpack-dev-server -D`

在package.json的scripts中添加

`"webpack-dev-server": "webpack-dev-server",`

修改

`"dev": "npm run webpack-dev-server -- --env.mode development",`

修改button.js

```js
// take a str, the button label and return a element
/**
 *
 * @param {string} buttonName
 * @returns {Element}
 */
const makeButton = buttonName => {
  const buttonLabel = `Button: ${buttonName}`

  const button = document.createElement('button')
  button.innerText = buttonLabel

  return button
}

module.exports = makeButton
```

修改index.js

```js
import nav from './nav'
import { top, bottom } from './footer'
import makeButton from './button'
import { makeColorStyle } from './button-styles'

const button = makeButton('Yay, a button')
document.body.appendChild(button)
```

可以看到页面上热更新了按钮。

### Starting to Code with Webpack

`git checkout feature/0312-webpack-dev-server -f`

`npm run dev`

index.js:

```js
import nav from './nav'
import { top, bottom } from './footer'
import makeButton from './button'
import { makeColorStyle } from './button-styles'

const button = makeButton('Yay a button!!!!')
button.style = makeColorStyle('blue') // 添加后可看到页面按钮字体颜色更改为蓝色

document.body.appendChild(button)
```

footer.js:

```js
import { red, blue } from './button-styles'

const top = document.createElement('div'),
  bottom = document.createElement('div'),
  footer = document.createElement('footer')

top.innerText = 'Top of footer'
top.style = red
bottom.innerText = 'Bottom of footer'
bottom.style = blue

footer.appendChild(top)
footer.appendChild(bottom)

export { top, bottom, footer }
```

index.js:

```js
import nav from './nav'
import { footer } from './footer'
import makeButton from './button'
import { makeColorStyle } from './button-styles'

const button = makeButton('Yay a button!!!!')
button.style = makeColorStyle('blue')

document.body.appendChild(button)
document.body.appendChild(footer)
```

### Splitting Environment Config Files

修改webpack.config.js, 添加modeConfig模块，给module.exports设置默认参数（es6新语法）

```js
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const modeConfig = env => require(`./build-utils/webpack.${env}`)(env)

module.exports = ({ mode, presets } = { mode: 'production', presets: [] }) => {
  console.log(mode)
  return {
    mode,
    output: {
      filename: 'bundle.js'
    },
    plugins: [new HtmlWebpackPlugin(), new webpack.ProgressPlugin()]
  }
}
```

`npm i webpack-merge -D`

`touch ./build-utils/webpack.production.js`
`touch ./build-utils/webpack.development.js`

./build-utils/webpack.production.js:

```js
module.exports = () => ({
  output: {
    filename: '[chunkhash].js'
  }
})
```

./build-utils/webpack.development.js:

```js
module.exports = () => ({})
```

webpack.config.js:

```js
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const webpackMerge = require('webpack-merge')
const modeConfig = env => require(`./build-utils/webpack.${env}`)(env)

module.exports = ({ mode, presets } = { mode: 'production', presets: [] }) => {
  return webpackMerge(
    {
      mode,
      output: {
        filename: 'bundle.js'
      },
      plugins: [new HtmlWebpackPlugin(), new webpack.ProgressPlugin()]
    },
    modeConfig(mode)
  )
}
```

`npm run prod`

之后可以看到在production模式生成了一个hash值为名的js文件。

[multipage-webpack-plugin](https://github.com/zorigitano/multipage-webpack-plugin)

## Using Plugins

### Using CSS with Webpack

`git checkout feature/04010-composing-configs-webpack-merge -f`

`touch ./src/footer.css`

footer.css:

```css
footer {
  height: 100px;
  width: 100%;
  background: #333;
}
```

footer.js:

```js
import './footer.css'
```

webpack.development.js:

```js
module.exports = () => ({
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
})
```

添加style-loader和css-loader后可以看到导入的css文件已经作用在开发模式下。

### Hot Module Replacement with CSS

`git checkout feature/040101-add-style-loader -f`

修改package.json中scripts:

`"dev": "npm run webpack-dev-server -- --env.mode development --hot",`

可以看到浏览器无需刷新即可更新最新的状态。

`npm i mini-css-extract-plugin -D`

更改webpack.production.js:

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = () => ({
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      }
    ]
  },
  plugins: [new MiniCssExtractPlugin()]
})
```

`npm run prod` 可以看到导出了`main.css`

可以看到，引入的其他css文件最终也会被打包到main.css中。

### File Loader & Url Loader

`git checkout feature/04011-adding-styles-css -f`

`npm i file-loader url-loader -D`

webpack.config.js:

```js
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const webpackMerge = require('webpack-merge')

const modeConfig = env => require(`./build-utils/webpack.${env}`)(env)

module.exports = ({ mode, presets } = { mode: 'production', presets: [] }) => {
  return webpackMerge(
    {
      mode,
      module: {
        rules: [
          {
            test: /\.jpe?g$/,
            use: ['url-loader']
          }
        ]
      },
      output: {
        filename: 'bundle.js'
      },
      plugins: [new HtmlWebpackPlugin(), new webpack.ProgressPlugin()]
    },
    modeConfig(mode)
  )
}
```

### Loading Images with JavaScript

在index.js中

```js
import image from './someImage.jpg'

console.log(image)
```

可以看到控制台输出了base 64格式的图片代码。

### Limit Filesize Option in URL Loader

webpack.config.js

```js
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const webpackMerge = require('webpack-merge')

const modeConfig = env => require(`./build-utils/webpack.${env}`)(env)

module.exports = ({ mode, presets } = { mode: 'production', presets: [] }) => {
  return webpackMerge(
    {
      mode,
      module: {
        rules: [
          {
            test: /\.jpe?g$/,
            use: [
              {
                loader: 'url-loader',
                options: {
                  limit: 5000
                }
              }
            ]
          }
        ]
      },
      output: {
        filename: 'bundle.js'
      },
      plugins: [new HtmlWebpackPlugin(), new webpack.ProgressPlugin()]
    },
    modeConfig(mode)
  )
}
```

`git checkout feature/04012-adding-images -f`

`npm run dev`

### Implementing Presets

`touch ./build-utils/presets/loadPresets.js`

loadPresets.js:

```js
const webpackMerge = require('webpack-merge')

module.exports = env => {
  const { presets } = env
  const mergedPresets = [].concat(...[presets])
  const mergedConfigs = mergedPresets.map(
    presetName => require(`./presets/webpack.${presetName}`)(env) // call the preset and pass env also
  )

  return webpackMerge({}, ...mergedConfigs)
}
```

webpack.config.js

```js
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const webpackMerge = require('webpack-merge')

const modeConfig = env => require(`./build-utils/webpack.${env}`)(env)
const presetConfig = require('./build-utils/presets/loadPresets') // add this

module.exports = ({ mode, presets } = { mode: 'production', presets: [] }) => {
  return webpackMerge(
    {
      mode,
      module: {
        rules: [
          {
            test: /\.jpe?g$/,
            use: [
              {
                loader: 'url-loader',
                options: {
                  limit: 5000
                }
              }
            ]
          }
        ]
      },
      output: {
        filename: 'bundle.js'
      },
      plugins: [new HtmlWebpackPlugin(), new webpack.ProgressPlugin()]
    },
    modeConfig(mode),
    presetConfig({ mode, presets }) // add this
  )
}
```

`git checkout feature/04013-adding-presets -f`

`touch ./build-utils/webpack.typescript.js`

```js
module.exports = () => ({
  module: {
    rules: [
      {
        test: /\.ts$/,
        use: 'ts-loader'
      }
    ]
  }
})
```

`npm i ts-loader typescript@next -D`

package.json

`"prod:typescript": "npm run prod -- --env.presets typescript",`

`git checkout feature/04014-typescript-preset -f`

`npm run prod:typescript`

### Bundle Analyzer Preset

`git checkout feature/04013-adding-presets -f`

`npm i webpack-bundle-analyzer -D`

package.json:

`"prod:analyze": "npm run prod -- --env.presets analyze",`

`touch ./build-utils/presets/webpack.analyze.js`

webpack.analyze.js:

```js
const WebpackBundleAnalyzer = require('webpack-bundle-analyzer')
  .BundleAnalyzerPlugin

module.exports = () => ({
  plugins: [new WebpackBundleAnalyzer()]
})
```

`npm run prod:analyze`

可以看到打开了一个新的网页，显示了各个依赖模块及其大小

### Compression Plugin

`git checkout feature/040131-bundle-analyze -f`

`npm i compression-webpack-plugin -D`

`touch ./build-utils/presets/webpack.compress.js`

webpack.compress.js:

```js
const CompressionWebpackPlugin = require('compression-webpack-plugin')

module.exports = () => ({
  plugins: [new CompressionWebpackPlugin()]
})
```

package.json:

`"prod:compress": "npm run prod -- --env.presets compress",`

`npm run prod:compress`

`npm run prod:compress -- --env.presets analyze`

### Source Map

webpack.production.js:

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = () => ({
  devtool: 'source-map',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      }
    ]
  },
  plugins: [new MiniCssExtractPlugin()]
})
```

`npm run prod -- --env.presets compress`

webpack.production.js:

```js
module.exports = () => ({
  devtool: 'source-map',
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
})
```

`npm run dev -- --env.presets compress`

### Lazy Loading

index.js:

```js
// import footer from './footer'
const loadFooter = () => import('./footer') // 通过回调加载模块

button.addEventListener('click', e => { // 在点击事件回调中异步加载模块
  loadFooter().then(m => { // 返回一个promise，在回调中加载模块
    document.body.appendChild(m.footer) // 模块懒加载
  })
})
```

### Resources

[webpack-contrib](https://github.com/webpack-contrib)
