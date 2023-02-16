---
title: 如何使用GitHub Pages预览HTML
date: 2019-08-26 10:01:43
categories: [经验心得]
tags: [GitHub, GitHub Pages]
---
## 为什么要使用GitHub Pages预览HTML

当我们做出一个HTML页面后，如果不止想要在本地进行展示，而是展示在网络上，而又不想自己购买设置服务器，那么我们可以使用GitHub的Pages功能，将页面部署在GitHub Pages上进行展示。

GitHub Pages可以让你的GitHub远程仓库展示在互联网上，并且不需要任何服务器和数据库设置。

这样，就可已将我们的HTML页面通过互联网进行展示了。
<!-- more -->
## 如何进行部署

### 新建空仓库方式

部署说明主要参考官方说明[https://pages.github.com/](https://pages.github.com/)

1. 创建一个远程仓库
   + 首先要在GitHub上创建一个仓库，命名为`<GitHub用户名>.github.io`。
   + 注意这里的用户名必须与GitHub的用户名一致，否则不会生效。
2. 使用命令行或者其他方式本地克隆远程仓库
   + 如果使用命令行，直接在本地项目目录下输入下方命令即可
     + `git clone https://github.com/<GitHub用户名>/<GitHub用户名>.github.io`
3. 在本地目录编辑HTML、css等文件，并使用git进行管理
   + 关于本地git，可以参考我的[这篇笔记](https://blingblingredstar.github.io/2019/08/25/Git%E5%85%A5%E9%97%A8-%E6%9C%AC%E5%9C%B0%E4%BB%93%E5%BA%93/)
4. 将本地项目推送到远程仓库之后，可以直接访问`https://<GitHub用户名>.github.io`来预览文件效果。

### 已有的仓库

如果想要展示已有的远程仓库上的HTML，可以通过以下方式

1. 切换到仓库页面，点击`Settings`标签，或者浏览器地址栏直接输入`https://github.com/<GitHub用户名>/<仓库名>/settings`
2. 下拉页面，找到GItHub Pages部分
3. 在`Source`选项选择`master branch`，此时输入`https://<GItHub用户名>.github.io/<仓库名>/`即可访问主分支下的`index.html`文件。

遇到的一些问题：

+ 首先要注意资源的引入路径，使用GItHub Pages功能，根据上面的设置，在浏览器输入`https://<GItHub用户名>.github.io/<仓库名>/`会访问主分支下根目录的`index.html`，如果文件不存在，会显示根目录下的Markdown文件。
+ 此时要注意HTML中引入的css及js文件路经。
+ 如果使用了打包工具，且输出目录为`./dist`，那么直接访问`https://<GItHub用户名>.github.io/<仓库名>/dist/index.html`即可，此时注意打包工具所输出的依赖文件路径，例如使用parcel进行打包，需要在打包时添加命令参数，例如`parcel build index.html --public-url /<远程仓库名>/dist/`，否则打包输出的`index.html`文件的依赖路径会有问题，造成资源访问不到，无法加载。
