---
title: Introduction to NextJS 13 V3
date: 2023-08-26 22:20:32
categories: 学习笔记
tags: [FrontEndMasters, React, NextJS]
---

# Introduction to NextJS 13 V3

## Introduction

- [NextJS](https://nextjs.org/)
- [Course Repo](https://github.com/Hendrixer/intro-nextjs-v3)
- [Course Slides](https://scottmoss.notion.site/scottmoss/Intro-to-Next-js-V3-6cefbdba58d94e3897dcb8d7e7fc0337)

### History of Next.js

React is a JavaScript library for building user interfaces. It was created by Facebook in 2011 and made open source in 2013. React quickly gained popularity among developers due to its simplicity and effectiveness in building complex web applications. > React 是一个用于构建用户界面的 JavaScript 库。它由 Facebook 于 2011 年创建，并于 2013 年开源。由于其简单性和构建复杂 Web 应用程序的有效性，React 很快在开发人员中流行起来。

In 2016, Vercel (formerly Zeit) released Next.js, a framework for building server-side rendered React applications. Next.js provides benefits over regular React applications such as easier server-side rendering, automatic code splitting, and optimized performance. > 2016 年，Vercel（前身为 Zeit）发布了 Next.js，这是一个用于构建服务器端渲染的 React 应用程序的框架。Next.js 提供了比常规 React 应用程序更多的好处，例如更容易的服务器端渲染、自动代码拆分和优化的性能。

Next.js was created to solve some of the challenges that developers faced while building React applications. These challenges included the complexity of server-side rendering, which required a lot of configuration and setup, and the lack of optimized performance for large-scale applications. > Next.js 的创建是为了解决开发人员在构建 React 应用程序时面临的一些挑战。这些挑战包括服务器端渲染的复杂性，这需要大量的配置和设置，以及大规模应用程序缺乏优化的性能。

Next.js simplifies the process of building server-side rendered React applications by providing a set of tools and conventions that make it easier to get started. It also provides automatic code splitting, which helps to optimize the performance of the application. > Next.js 通过提供一组工具和约定来简化构建服务器端渲染的 React 应用程序的过程，从而使入门变得更加容易。它还提供了自动代码拆分，这有助于优化应用程序的性能。

Overall, Next.js has many advantages over regular React applications, including easier server-side rendering, automatic code splitting, and optimized performance. It continues to be popular among developers and has been adopted by many companies for building complex web applications. > 总的来说，Next.js 在许多方面都优于常规的 React 应用程序，包括更容易的服务器端渲染、自动代码拆分和优化的性能。它在开发人员中仍然很受欢迎，并已被许多公司采用，用于构建复杂的 Web 应用程序。

<!-- more -->

## Project Setup & Routing

### React Server Components

- Server components > 服务端组件

  - React Server Component (RSC) is a new paradigm introduced in React 18, and Next.js 13 has introduced support for RSC through their app directory. > React Server Component (RSC) 是 React 18 中引入的一种新范式，Next.js 13 通过其 app 目录引入了对 RSC 的支持。

- SPA > 单页应用

  - A Single-Page Application (SPA) works by rendering and data fetching taking place in the browser itself. This results in the First Contentful Paint (FCP) taking longer, affecting the user experience. > 单页应用（SPA）的工作原理是在浏览器本身进行渲染和数据获取。这导致第一次内容绘制（FCP）需要更长的时间，从而影响用户体验。

- SSR > 服务端渲染

  - In contrast, SSR mitigates these issues by rendering the initial page in the server itself, reducing the time taken for FCP and vastly improving the user experience. However, the page will not become interactive immediately, and the browser needs to wait till it completes downloading the JavaScript bundle to add interactivity. > 相比之下，SSR 通过在服务器本身渲染初始页面来缓解这些问题，从而减少了 FCP 所需的时间，大大改善了用户体验。但是，页面不会立即变得可交互，浏览器需要等待它完成下载 JavaScript 包才能添加交互性。

- RSC > 服务端组件

  - RSC is a new way of doing server-side rendering by allowing developers to write components that run on the server and stream their output to the client. > RSC 是一种新的服务端渲染方式，它允许开发人员编写在服务器上运行并将其输出流式传输到客户端的组件。
  - In RSC, there are two types of components: server components, which can be rendered on the server, and client components, which are rendered on the browser. Server components can be defined as any component that doesn’t involve user interactivity like a mouse click or keyboard input or use React hooks like the useState hook and the useEffect hook. > 在 RSC 中，有两种类型的组件：服务端组件，可以在服务器上呈现，以及客户端组件，可以在浏览器上呈现。服务器组件可以定义为不涉及用户交互（如鼠标单击或键盘输入）或使用 React 钩子（如 useState 钩子和 useEffect 钩子）的任何组件。
  - The server can render these components and stream them to the browser. The server needs to send the JavaScript code to render only the client components, so the JavaScript bundle size is going to be a lot smaller and faster to download. This means that the user interface can become interactive faster reducing TTI. > 服务器可以呈现这些组件并将其流式传输到浏览器。服务器需要发送 JavaScript 代码来渲染仅客户端组件，因此 JavaScript 包大小将更小且下载速度更快。这意味着用户界面可以更快地变得交互式，从而减少 TTI。

- RSC vs. SSR
  - In contrast, SSR performs server-side rendering only during the initial page load. This means that other than the initial components, all other components are rendered in the browser when SSR is used. So, the browser has to download the JavaScript for the whole app just like in a Single-Page Application (SPA). This makes RSC more efficient than SSR in terms of performance and user experience. > 相比之下，SSR 仅在初始页面加载期间执行服务器端渲染。这意味着除了初始组件之外，所有其他组件都是在浏览器中呈现的，当使用 SSR 时。因此，浏览器必须像单页应用程序（SPA）中一样下载整个应用程序的 JavaScript。这使得 RSC 在性能和用户体验方面比 SSR 更有效。
