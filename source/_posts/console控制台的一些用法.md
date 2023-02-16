---
title: console控制台的一些用法
date: 2019-04-15 06:53:06
categories: 学习笔记
tags: [console, 控制台]
---
## 关于console控制台的一些用法

来自Wes Bos的[JavaScript30](https://courses.wesbos.com/account/access/5be7ff21c413951cd2b18ede/view/194129876)这个视频。

<!--more-->

```JavaScript
<body>

  <p onClick="makeGreen()">×BREAK×DOWN×</p>

  <script>
    const dogs = [{ name: 'Snickers', age: 2 }, { name: 'hugo', age: 8 }];

    function makeGreen() {
      const p = document.querySelector('p');
      p.style.color = '#BADA55';
      p.style.fontSize = '50px';
    }

    // 当我们想知道哪段代码改变了页面上的DOM元素时，可以在页面上右键检查元素，然后在控制台右击控制台elements中的HTML代码部分
    // 在右击后选择Break on中的Attibute modification，然后刷新浏览器，进行改变该DOM元素的操作，就可以调到对应的js代码块了
    // 暂时可以联想为比较快捷的添加了断点，方便调试。

    // Regular
    console.log('hello')

    // Interpolated
    // 可以插入字符串
    console.log('Hello I am a %s string!','✋')
    // 也可是使用es6中的模板字符串
    console.log(`Hello I am a 👌`)
    // Styled
    // 可以插入样式
    console.log('%c I am a text','font-size:30px')
    // warning!
    console.log('I am a warning')
    // Error :|
    console.log('I am a Error')
    // Info
    console.info('I am some info')
    // Testing
    // console.assert类似if语句，传入两个参数，前一个是判断条件，下一个是执行语句
    // 通过console.assert，如下可以方便判断是否有一个元素或者一些其他用法。
    const p = document.querySelector('p')
    console.assert(p.classList.contains('active'),'That is wrong!')
    // clearing
    // 清空控制台
    console.clear()
    // 或者可以在控制台直接输如clear()，因为直接输入时作用域为当前对象，可以直接调用

    // Viewing DOM Elements
    // 当我们直接console.log(aDomElement)时，会输出一个HTML标签，无法查看他的属性和方法
    // 使用console.dir(aDomElement)，就可以像查询对象一样出现他的属性和方法了。
    console.log(p)
    console.dir(p)
    // Grouping together
    // 可以把多个console.log包裹起来，是控制台更加易读。
    dogs.forEach(dog => {
      console.groupCollapsed(`${dog.name}`)
      console.log(`This is ${dog.name}`)
      console.log(`${dog.name} is ${dog.age} years old`)
      console.log(`${dog.name} is ${dog.age * 7} dog years old`)
      console.groupEnd(`${dog.name}`)
    });

    // counting
    // 顾名思义，计数器
    console.count('num1')
    console.count('num1')
    console.count('num1')
    console.count('num2')
    console.count('num2')
    // timing
    // 顾名思义，计时器
    console.time('fetching data');
    fetch('https://api.github.com/users/wesbos')
      .then(data => data.json())
      .then(data => {
        console.timeEnd('fetching data');
        console.log(data);
      });

  </script>
</body>
```
