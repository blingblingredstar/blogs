---
title: 安装Vue
date: 2019-12-12 20:03:36
categories: [学习笔记]
tags: [饥人谷, Vue]
---
Vue是目前前端所流行的三大框架之一(其余为React和Angular)，Vue的主页中声明Vue是一个渐进式JavaScript框架，拥有易用、灵活以及高效的特点。

Vue成为渐进式框架的原因是既可以将Vue作为应用的一部分替换原有的服务端应用，也可以结合Vue的核心库和其他生态系统来构建前端为主的应用程序。

作为使用Vue的第一步，首先就是安装Vue。

一下我将结合[Vue官方中文文档](https://cn.vuejs.org/v2/guide/)以及我自己的总结来说明如何安装Vue。
<!-- more -->
## Vue的几个版本

使用Vue最简单的办法就是直接使用script标签加载CDN上的资源，这里我将使用[BootCDN](https://www.bootcdn.cn/)。

在[BootCDN的Vue页面](https://www.bootcdn.cn/vue/)中我们可以看到每个版本都有多个资源。再使用script标签引入资源时，我们只需关注以下四个资源:

1. vue.js
2. vue.min.js
3. vue.runtime.js
4. vue.runtime.min.js

其中，添加min中缀的为生产环境版本，压缩了js代码，去掉了代码注释，减少了代码体积，其他两个为开发环境版本。

在这里我们使用开发环境版本，在生产部署中，应将其替换为生产版本，减少文件体积。

那么，vue.runtime.js与vue.js又有什么区别呢？

首先就是代码体积，在本博客书写时，Vue版本为2.6.10，加载时vue.js文件体积为333kb，而vue.runtime.js文件体积为233kb。可见，vue.runtime.js体积约为vue.js体积的70%。

文件体积的缩减必然是因为代码量的缩减，那么vue.runtime.js相比vue.js减少了哪些代码呢？

官方文档将vue.js称为完整版，vue.runtime.js称为运行时。

完整版包含编译器和运行时，可见相比运行时版本，完整版所增加的代码体积为编译器。

那么，编译器又有何作用呢？

通过官方文档也可知，编译器可用来将模板字符串编译为JavaScript渲染函数的代码。

举例说明的话，我们可以使用官方文档中hello world的代码:

```html
<div id="app">
  {{ message }}
</div>
<script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
<script>
  var app = new Vue({
    el: "#app",
    data: {
      message: "Hello Vue!"
    }
  });
</script>
```

此时页面正常输出"Hello Vue!"。

但我们将script引入的包替换为运行时版本的话，可以看见页面并没有内容，控制台也会报错提示使用包含编译器的完整版或者预先编译。

那么，如果我们要使用运行时版本实现上面的功能，我们应该书写如下:

```html
<div id="app"></div>
<script src="https://cdn.bootcss.com/vue/2.6.10/vue.runtime.js"></script>
<script>
  var app = new Vue({
    el: "#app",
    data: {
      message: "Hello Vue!"
    },
    render(createElement) {
      return createElement("div", this.message);
    }
  });
</script>
```

可见，我们相比完整版直接书写HTML语法的代码，使用运行时，我们需要在Vue实例对象的render方法中书写js语法代码，书写较为不便且不直观。

但为了优化应用性能，减少首屏加载时间，我们又希望在生产环境中使用体积更小的运行时版本。

此时我们可以结合webpack、parcel等打包工具在开发时使用HTML语法，经过打包工具的编译后，生成js格式语法的代码，部署到生产环境中。

所以在webpack等打包工具中，默认使用运行时版本，如需使用完整版，需要额外配置，具体见[官方文档](https://cn.vuejs.org/v2/guide/installation.html#%E8%BF%90%E8%A1%8C%E6%97%B6-%E7%BC%96%E8%AF%91%E5%99%A8-vs-%E5%8F%AA%E5%8C%85%E5%90%AB%E8%BF%90%E8%A1%8C%E6%97%B6)。

## template与render的用法

我们现在实现一个点击按钮数字加一的功能。

当我们创建一个Vue的实例对象时，我们可以传入一个template参数，该参数将会插入到Vue所挂载的html元素中，代码如下

```html
<div id="app"></div>
<script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
<script>
  var app = new Vue({
    el: "#app",
    template: `<div>{{ number }} <button @click="incrementByOne">+1</button></div>`,
    data: {
      number: 0
    },
    methods: {
      incrementByOne() {
        this.number++;
      }
    }
  });
</script>
```

或者我们可以使用运行时版本，用render参数实现上述功能，代码入下：

```html
<div id="app"></div>
<script src="https://cdn.bootcss.com/vue/2.6.10/vue.runtime.js"></script>
<script>
  var app = new Vue({
    el: "#app",
    data: {
      number: 0
    },
    methods: {
      incrementByOne() {
        this.number++;
      }
    },
    render(createElement) {
      return createElement("div", [
        this.number,
        createElement(
          "button",
          { on: { click: this.incrementByOne } },
          "+1"
        )
      ]);
    }
  });
</script>
```

## 在线环境

我们可以使用[CodeSandbox](https://codesandbox.io/)来制作在线实例，具体使用方式可见官网。
