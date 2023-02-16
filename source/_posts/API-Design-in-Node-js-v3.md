---
title: API Design in Node.js v3
date: 2019-10-24 10:43:22
categories: 学习笔记
tags: [FrontEndMasters, Node.js, Express, JWT, MongoDB]
---
## Introduction

+ [GitHub Repo](https://github.com/FrontendMasters/api-design-node-v3)

+ [Slides](https://slides.com/scotups/api-design-in-node-with-express-v3/#/)

### API

+ What is an API
  + tldr; a server that creates an HTTP interface for interacting with some data
  + Applicaction programming interface
  + The name is used EVERYTHERE
  + Usually a server on some remote machine that dictates how another application can interact with some data
  + Basic data operations like, Create, Read, Update, Destory(CRUD)

+ What about REST?
  + tldr; most popular API design pattern, but is not the silver bullet. Very blurry
  + An API design that combines DB resources, route paths, and HTTP verbs to allow application describe what action they are trying to perform
  + Popularized when SaaS products starting offering APIs for integrations
  + Works with basic data models
  + Hard to scale with complex data models and client requirements
<!-- more -->
### Node.js for APIs

+ Node.js and APIs
  + tldr; build for high concurrent APIs that are not CPU intensive
  + Node.js is JavaScript, it's async and event driven
  + Single threaded(can optimize)
  + When kept async, Node can handle a high amount of concurrent request
  + Not great for CPU intensive work(data crunching, ML, big maths)
  + So many open source tools to help build APIs

### Express

+ Express
  + tldr; the standard API framework for Node.js
  + Handles all the tedious tasks like managing sockets, route matching, error handling, and more
  + Open source
  + Has a huge community and support from anything that has to do with APIs in Node.js
  + Not going anywhere anytime soon

### MongoDB

+ MongoDB
  + tldr; the go-to non-relational DB, works like a dream in Node.js
  + Non-relational document store that is easy to get started and scales well
  + Open source and backed by a big company
  + Tons of hosting solutions
  + ORM/ODM and other libs are some of the best for many DB
<!-- markdownlint-disable md024 -->
## Express

### Setup Code & Express

### Routing & Middleware

+ What is Middleware
  + tldr; list of functions that execute, in order, before your controllers
  + Allow you to execute functions on an incoming request with guaranteed order
  + Great for anthentication, transforming the request, tracking, error handling
  + Middleware can also respond to request like a controller would, but that is not their intent

### Custom Middleware

```js
const log = (req, res, next) => {
  // 自定义中间件
  console.log('logging')
  next() // 隐式参数，或者传入错误处理函数，自动执行任务队列的下一个任务
}

app.use(log) // 全局使用自定义中间件
```

### REST Routes with Express

+ REST routes with Express
  + tldr; Express was designed with REST in mind and has all you need
  + Express has a robust route matching system that allows for exact, regex, glob, and parameter matching
  + It also supports HTTP verbs on a route based level. Together with the routing, you can create REST APIs
    + app.get()
    + app.post()
    + app.put()
    + app.delete()

### Route Order

+ REST routes with Express
  + Routes match in the order that they were defined(top to bottom)

```js
app.get('/data', (req, res) => {
  res.send({ message: 'hello' })
})

app.get('/data', (req, res) => {
  res.send({ message: 'world' })
})
```

+ 客户端的收到的请求体中message为'hello'，所以同样的路由只会匹配第一个。

```js
app.get('/data', (req, res, next) => {
  // res.send({ message: 'hello' })
  next()
})

app.get('/data', (req, res) => {
  res.send({ message: 'world' })
})
```

+ 更改为如上代码后，message为’world‘，但不建议这么做，应该使用中间件实现类似需求，以增加可读性。

### Router & Sub Routes

+ REST routes with Express
  + For abstraction, Express allows you to create sub routers that combine to make a full router

```js
const router = express.Router()

router.get('/me', (req, res) => {
  // 定义路由规则，但并没有注册
  res.send({ me: 'hello' })
})

app.use('/api', router) // 以中间件的形式将router挂载到全局路由中
```

### Router Verb Methods

```js
// 同一请求路径可以根据不同动词进行处理
router.route('/cat')
  .get()
  .post()

router.route('/cat/:id')
  .get()
  .put()
  .delete()
```

### Exercise: Router & Sub Routes

```js
// server.js
import itemRouter from './resources/item/item.router'
app.use('/api/item', itemRouter)
```

```js
// item.router.js
import { Router } from 'express'
import controllers from './item.controllers'

const router = Router()

// /api/item
router
  .route('/')
  .get(controllers.getOne)
  .post(controllers.createOne)

// /api/item/:id
router
  .route('/:id')
  .get(controllers.getOne)
  .put(controllers.updateOne)
  .delete(controllers.removeOne)

export default router
```

### Data Modeling

### Data Modeling with MongoDB

+ Schemas for a schemaless DB?
  + tldr; You should always use a Schema for models, and mongoose makes it easy
  + MongoDB is a Schemaless document store, but you should always use schemas if you don't want to go crazy
  + MongoDB has added support for creating schemas, but Mongoose is much better
  + We can create models for each REST resource we want to expose via the API

### Transitioning from Schemas to Models

+ Schemas to models
  + tldr; Schemas are the instructions for the model
  + Schemas hold the instructions for models. Things like validations, names, indexes, and hooks

### Exercise: Mongoose Schema
<!-- markdownlint-disable md033 -->
<details>
<summary>solution</summary>

```js
import mongoose from 'mongoose'

const itemSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: true,
      trim: true,
      maxlength: 50
    },
    status: {
      type: String,
      required: true,
      enum: ['active', 'complete', 'pastdue'],
      default: 'active'
    },
    notes: String,
    due: Date,
    createdBy: {
      type: mongoose.SchemaTypes.ObjectId,
      ref: 'user',
      required: true
    },
    list: {
      type: mongoose.SchemaTypes.ObjectId,
      ref: 'list',
      required: true
    }
  },
  { timestamps: true }
)

itemSchema.index({ list: 1, name: 1 }, { unique: true })

export const Item = mongoose.model('item', itemSchema)
```

</details>

## Controllers & Models

### Controllers & Models Overview

+ Routes and Controllers
  + tldr; controllers are just middleware but with the intent on returning some data
  + Controllers handle what a Route + Verb combo can access from the DB
  + Think of them as the final middleware in the stack for a request. Their is no intent to proceed to another middleware function after a controller
  + Controllers implement the logic that interacts with our DB models
  + Can generalize controllers to work for many models because we're going with a REST approach which requires CRUD actions on resources

### Express Response Object

+ res对象可以链式调用

### Refactoring CRUD Routes with Models

### Creating, Read, Update & Delete a Document

+ Using models
  + tldr; Mongoose models work very nicely with CRUD
  + C - `model.create()`, `new model()`
  + R - `model.find()`, `model.findOne()`, `model.findById()`
  + U - `model.update()`, `model.findByIdAndUpdate()`, `model.findOneAndUpdate()`
  + D - `model.remove()`, `model.findByIdAndUpdate()`, `model.findOndeAndRemove()`

### CRUD Controller Design Overview

+ GET / Read many
+ GET /:id Read one
+ POST / Create one
+ PUT /:id Update one
+ DELETE /:id Delete one

### Exercise: CRUD Controller

<details>
<summary>crud.js</summary>

```js
export const getOne = model => async (req, res) => {
  try {
    const doc = await model
      .findOne({ createdBy: req.user._id, _id: req.params.id })
      .lean()
      .exec()

    if (!doc) {
      return res.status(400).end()
    }

    res.status(200).json({ data: doc })
  } catch (e) {
    console.error(e)
    res.status(400).end()
  }
}

export const getMany = model => async (req, res) => {
  try {
    const docs = await model
      .find({ createdBy: req.user._id })
      .lean()
      .exec()

    res.status(200).json({ data: docs })
  } catch (e) {
    console.error(e)
    res.status(400).end()
  }
}

export const createOne = model => async (req, res) => {
  const createdBy = req.user._id
  try {
    const doc = await model.create({ ...req.body, createdBy })
    res.status(201).json({ data: doc })
  } catch (e) {
    console.error(e)
    res.status(400).end()
  }
}

export const updateOne = model => async (req, res) => {
  try {
    const updatedDoc = await model
      .findOneAndUpdate(
        {
          createdBy: req.user._id,
          _id: req.params.id
        },
        req.body,
        { new: true }
      )
      .lean()
      .exec()

    if (!updatedDoc) {
      return res.status(400).end()
    }

    res.status(200).json({ data: updatedDoc })
  } catch (e) {
    console.error(e)
    res.status(400).end()
  }
}

export const removeOne = model => async (req, res) => {
  try {
    const removed = await model.findOneAndRemove({
      createdBy: req.user._id,
      _id: req.params.id
    })

    if (!removed) {
      return res.status(400).end()
    }

    return res.status(200).json({ data: removed })
  } catch (e) {
    console.error(e)
    res.status(400).end()
  }
}

export const crudControllers = model => ({
  removeOne: removeOne(model),
  updateOne: updateOne(model),
  getMany: getMany(model),
  getOne: getOne(model),
  createOne: createOne(model)
})
```

</details>

## Auth

### Authentication in APIs Overview

+ Authentication with JWT(JSON Web Token)s

+ Auth basics
  + tldr; You can never truly protect an API, but requiring authentication makes it a bit safer
  + Authentication is controlling if an incoming request can proceed or not
  + Authorization is controlling if an authenticated request has the correct permissions to access a resource
  + Identification is determining who the requester is

### JSON Web Token Authentication

+ JWT authentication
  + tldr; tokens passed every request to check auth on the server
  + A bearer token strategy that allows the API to be stateless with user auth
  + Created by a combination of secrets on the API and a payload like a user object
  + Must be sent with every request where the API will then try to verify the token was created with the expected secrets
  + After sucessful verification, JWT payload is accessible to the server. Can be used to authorization and identification

### Exercise: Secure An API with JWTs

<details>
<summary>auth.js</summary>

```js
import config from '../config'
import { User } from '../resources/user/user.model'
import jwt from 'jsonwebtoken'

export const newToken = user => {
  return jwt.sign({ id: user.id }, config.secrets.jwt, {
    expiresIn: config.secrets.jwtExp
  })
}

export const verifyToken = token =>
  new Promise((resolve, reject) => {
    jwt.verify(token, config.secrets.jwt, (err, payload) => {
      if (err) return reject(err)
      resolve(payload)
    })
  })

export const signup = async (req, res) => {
  if (!req.body.email || !req.body.password) {
    return res.status(400).send({ message: 'Email and password required' })
  }
  try {
    const user = await User.create(req.body)
    const token = newToken(user)
    return res.status(201).send({ token })
  } catch (error) {
    console.error(error)
    return res.status(400).end()
  }
}

export const signin = async (req, res) => {
  if (!req.body.email || !req.body.password) {
    return res.status(400).send({ message: 'Email and password required' })
  }

  const user = await User.findOne({ email: req.body.email }).exec()

  if (!user) {
    return res.status(401).send({ message: 'Not auth' })
  }

  try {
    const match = await user.checkPassword(req.body.password)
    if (!match) {
      return res.status(401).send({ message: 'Not auth' })
    }
    const token = newToken(user)
    return res.status(200).send({ token })
  } catch (error) {
    console.error(error)
    return res.status(401).send({ message: 'Not auth' })
  }
}

export const protect = async (req, res, next) => {
  if (!req.headers.authorization) {
    return res.status(401).end()
  }

  let token = req.headers.authorization.split('Bearer ')[1]
  if (!token) {
    return res.status(401).end()
  }
  try {
    const payload = await verifyToken(token)
    const user = await User.findById(payload.id)
      .select('-password')
      .lean()
      .exec()
    req.user = user
    next()
  } catch (error) {
    console.error(error)
    return res.status(401).end()
  }
}
```

</details>

<details>
<summary>server.js</summary>

```js
app.post('/signup', signup)
app.post('/signin', signin)
app.use('/api', protect)
```

</details>
