---
title: Webpack Plugin System
date: 2019-12-16 15:20:12
categories: 学习笔记
tags: [FrontEndMasters, Webpack]
---

## Plugin System

### Tapable Plugin System

webpack本身可以看做一个custom plugin，由许多其他plugin组成。

+ What is Tapable?
  + 200 line plugin library
  + the backbone of the plugin system
  + Tapbale instance
    + A class/object that extends Tapable(a.k.a something you can plug into)
<!-- more -->
webpack 4之前，api工作原理类似如下

```js
Compiler.prototype.compile = function(callback) {
  var self = this;
  var params = self.newCompilationParams();
  self.applyPluginAsync("before-compile", params, function(err) {
    if (err) return callback(err);

    self.applyPlugins("compile", params);

    var compilation = self.newCompilation(params);

    self.applyPluginsParallel("make", compilation, function(err) {
      if (err) return callback(err);

      compilation.finish();
       
      compilation.seal(function(err) {
        if (err) return callback(err);

        self.applyPluginAsync("after-compile", compilation, function(err) {
          if (err) return callback(err);

          return callback(null, compilation);
        });
      });
    });
  });
};
```

在webpack 3中，我们可以如下编写plugin

```js
class BasicPlugin {
  constructor() {}

  apply(compiler) {
    compiler.plugin('make', (compilation) => {
      console.log("I now have access to the compilation!!!")
    })
  }
}

module.exports = BasicPlugin
```

### Compiler & Compilation

+ 7ish Tapable Instances(aka classes)

最重要的一个就是complier

webpack4的源代码中，compiler最重要的属性就是hooks。

+ Compiler
  + Exosed via Node API
  + Central dispatch
  + Start/Stop

+ Compilation
  + AKA: The dependency graph! Like human brain.
  + Created by the compiler
  + Contains dep graph traversal algo.

### Resolver & Module Factories

+ Resolvers
  + 给定一个路径，确保引入的模块存在

+ Module Factories
  + Create Module instance
  + Take successfully resolved requests
  + Collect source for that file
  + Create a Module obj

### Parser & Templates

+ Parser
  + Take a string and converts to an AST
  + Take a module Object, turns into AST to parse
  + Find all requires/imports, create Dependency's

+ Templates
  + Data binding for your modules
  + Create the code you see in your bundles

webpack的工作流程可以概述为以下三个阶段

1. build the graph
2. optimize the graph
3. render the graph to the bundles

## Creating Plugins

### Creating a Webpack Plugin

```js
class MyFirstWebpackPlugin {
  apply(compiler) {
    compiler.hooks.done.tapAsync("MyFirstWebpackPlugin", (status, cb) => {
      const assetNames = Object.keys(status.compilation.assets);
      console.log(assetNames.join("\n"));
      cb();
    });
  }
}

module.exports = MyFirstWebpackPlugin;
```

### Plugin Instance Hooks

```js
class MyFirstWebpackPlugin {
  apply(compiler) {
    compiler.hooks.done.tapAsync("MyFirstWebpackPlugin", (status, cb) => {
      const assetNames = Object.keys(status.compilation.assets);
      console.log(assetNames.join("\n"));
      cb();
    });

    compiler.hooks.compilation.tap(
      "MyFirstWebpackPlugin",
      (compilation, params) => {
        new MyFirstWebpackCompilationPlugin().apply(compilation);
      }
    );
  }
}

class MyFirstWebpackCompilationPlugin {
  apply() {
    compilation.hooks.seal.tap("MyFirstWebpackCompilationPlugin", () => {
      debugger;
    });
  }
}

module.exports = MyFirstWebpackPlugin;
```

## Config, Loaders & Babel

### Creating a Custom Loader

首先配置如下：

```js
module.exports = () => ({
  resolveLoader: {
    alias: {
      "my-loader": require.resolve("./my-loader.js")
    }
  },
  module: {
    rules: [{ test: /\.js/, use: "my-loader" }]
  }
});
```

```js
function myLoader(source) {
    debugger;
    if (this.resource === "/Users/wecomm/seanlarkin/webpack-workshop-2018/src/index.js") {
        source+="; console.log('ilovebanananas');"
    }
    return source;
} 

module.exports = myLoader;
```

### Configuring Babel for Webpack

推荐将babel的presets中env中的modules属性设置为false。

### Webpack Dev Kit & Wrap Up

[webpack-developer-kit](https://github.com/TheLarkInn/webpack-developer-kit)
