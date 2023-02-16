---
title: Code Transformation and Linting with Abstract Syntax Trees
date: 2019-07-30 21:49:21
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, AST, Babel, ESLint, Codemods]
---

## Introduction

### What this talk is

+ Introduction to Abstract Syntax Trees
+ Introduction to inspecting and traversing ASTs with ESLint and Babel

### What this talk is not

+ Deep dive into ASTs
+ Publishing actual plugin packages
<!-- more -->
### Setup

[https://github.com/kentcdodds/asts-workshop#setup](https://github.com/kentcdodds/asts-workshop#setup)

```bash
git clone https://github.com/kentcdodds/asts-workshop.git
cd asts-workshop
yarn run setup --silent
node scripts/autofill-feedback-email.js YOUR_EMAIL@DOMAIN.COM
git commit -am "ready to go"
```

## Abstract Syntax Trees

### Example of Abstract Syntax Trees

BABEL使用了AST来转化代码。

例如可以使用`babel-plugin-lodash`来转化文件

[babel-plugin-lodash  A simple transform to cherry-pick Lodash modules so you don’t have to.](https://github.com/lodash/babel-plugin-lodash)

+ Example
  + Transforms

```js
import _ from 'lodash'
import { add } from 'lodash/fp'

const addOne = add(1)
_.map([1, 2, 3], addOne)
```

+ roughly to

```js
import _add from 'lodash/fp/add'
import _map from 'lodash/map'

const addOne = _add(1)
_map([1, 2, 3], addOne)
```

[eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react)
[eslint-plugin-security](https://github.com/nodesecurity/eslint-plugin-security)
[eslint-plugin-jsx-a11y](https://www.npmjs.com/package/eslint-plugin-jsx-a11y)

### Introducing Abstract Syntax Trees

#### What Is an AST

Code for humans

```js
const uniqueRandomArray = require('unique-random-array')

const transformersNames = require('./transformers-names.json')
const getRandomItem = uniqueRandomArray(transformersNames)

module.exports = {
  all: transformersNames,
  random: random,
}

function random(number) {
  if (number === undefined) {
    return getRandomItem()
  } else {
    const randomItems = []
    for (let i = 0; i < number; i++) {
      randomItems.push(getRandomItem())
    }
    return randomItems
  }
}
```

Code for computers

```js
{
  "type": "File",
  "start": 0,
  "end": 482,
  "loc": {
    "start": {
      "line": 1,
      "column": 0
    },
    "end": {
      "line": 22,
      "column": 0
    }
  },
  "program": {
    "type": "Program",
    "start": 0,
    "end": 482,
    "loc": {
      "start": {
        "line": 1,
        "column": 0
      },
      "end": {
        "line": 22,
        "column": 0
      }
    },
    // 等余下3000行
```

#### Exploring ASTs

[jointjs demo](http://resources.jointjs.com/demos/rappid/apps/Ast/index.html)

[astexplorer.net demo](http://astexplorer.net/#/gist/da1ffc9ca82e239419fee2af19222394/latest)

## ESLint Plugin and AST

### Challenge 1: Making a Plugin

`npm start exercise.eslint.0`

```js
// eslint exercise 0 (no-console)
// When you're finished with this exercise, run
//   "npm start exercise.eslint.1"
//   to move on to the next exercise

module.exports = {
  meta: {
    docs: {
      description: 'Disallow use of console',
      category: 'Best practice',
      recommended: true,
    },
  },
  // you're going to need context :)
  // eslint-disable-next-line no-unused-vars
  create(context) {
    return {
      Identifier(node) {
        if (node.name !== 'console') {
          return
        }
        context.report({
          node,
          message: 'Using console is not allowed',
        })
      },
    }
  },
}
```

### Challenge 2: Adding New Methods

```js
// eslint exercise 1 (no-console)
// When you're finished with this exercise, run
//   "npm start exercise.eslint.2"
//   to move on to the next exercise
const disAllowedMethods = ['log', 'info', 'warn']

module.exports = {
  create(context) {
    return {
      Identifier(node) {
        const isConsoleCall = looksLike(node, {
          name: 'console',
          parent: {
            type: 'MemberExpression',
            parent: {type: 'CallExpression'},
            property: {
              name: val => disAllowedMethods.includes(val),
            },
          },
        })
        if (isConsoleCall) {
          context.report({
            node,
            message: 'Using console is not allowed',
          })
        }
      },
    }
  },
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

### Challenge 3: Schema Property

`npm start exercise.eslint.2`

`npm start`

```js
const disallowedMethods = ['log', 'info', 'warn', 'error', 'dir']

module.exports = {
  meta: {
    schema: [
      {
        type: 'object',
        properties: {
          allowedMethods: {
            type: 'array',
            items: {
              enum: ['log', 'info', 'warn', 'error', 'dir'],
            },
            minItems: 1,
            uniqueItems: true,
          },
        },
      },
    ],
  },
  create(context) {
    const config = context.options[0] || {}
    const allowedMethods = config.allowedMethods || []
    return {
      Identifier(node) {
        if (
          !looksLike(node, {
            name: 'console',
            parent: {
              type: 'MemberExpression',
              parent: {type: 'CallExpression'},
              property: {
                name: val =>
                  !allowedMethods.includes(val) &&
                  disallowedMethods.includes(val),
              },
            },
          })
        ) {
          return
        }
        context.report({
          node: node.parent.property,
          message: 'Using console is not allowed',
        })
      },
    }
  },
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

### Challenge 4: Edge Cases

`npm start ex.es.3`

`npm start`

```js
const disallowedMethods = ['log', 'info', 'warn', 'error', 'dir']

module.exports = {
  meta: {
    docs: {
      description: 'Disallow use of console',
      category: 'Best Practices',
      recommended: true,
    },
    schema: [
      {
        type: 'object',
        properties: {
          allowedMethods: {
            type: 'array',
            items: {
              enum: ['log', 'info', 'warn', 'error', 'dir'],
            },
            minItems: 1,
            uniqueItems: true,
          },
        },
      },
    ],
  },
  create(context) {
    const config = context.options[0] || {}
    const allowedMethods = config.allowedMethods || []
    const consoleUsage = []
    return {
      Identifier(node) {
        if (node.name !== 'console') {
          return
        }
        consoleUsage.push(node)
      },
      'Program:exit'() {
        consoleUsage.forEach(identifier => {
          if (isDisallowedFunctionCall(identifier)) {
            context.report({
              node: identifier.parent.property,
              message: 'Using console is not allowed',
            })
          } else {
            const variableDeclaratorParent = findParent(
              identifier,
              parent => parent.type === 'VariableDeclarator',
            )
            if (variableDeclaratorParent) {
              const references = context
                .getDeclaredVariables(variableDeclaratorParent)[0]
                .references.slice(1)
              references.forEach(reference => {
                if (
                  !looksLike(reference, {
                    identifier: {
                      parent: {
                        property: isDisallowedFunctionCall,
                      },
                    },
                  })
                ) {
                  return
                }
                context.report({
                  node: reference.identifier.parent.property,
                  message: 'Using console is not allowed',
                })
              })
            }
          }
        })
      },
    }

    function isDisallowedFunctionCall(identifier) {
      return looksLike(identifier, {
        parent: {
          type: 'MemberExpression',
          parent: {type: 'CallExpression'},
          property: {
            name: val =>
              !allowedMethods.includes(val) && disallowedMethods.includes(val),
          },
        },
      })
    }
  },
}

function findParent(node, test) {
  if (test(node)) {
    return node
  } else if (node.parent) {
    return findParent(node.parent, test)
  }
  return null
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

### Challenge 5: Applying Fixes

`npm start ex.es.4`

`npm start`

```js
const disallowedMethods = ['log', 'info', 'warn', 'error', 'dir']

module.exports = {
  meta: {
    docs: {
      description: 'Disallow use of console',
      category: 'Best Practices',
      recommended: true,
    },
    fixable: 'code',
    schema: [
      {
        type: 'object',
        properties: {
          allowedMethods: {
            type: 'array',
            items: {
              enum: ['log', 'info', 'warn', 'error', 'dir'],
            },
            minItems: 1,
            uniqueItems: true,
          },
        },
      },
    ],
  },
  create(context) {
    const config = context.options[0] || {}
    const allowedMethods = config.allowedMethods || []
    const consoleUsage = []
    return {
      Identifier(node) {
        if (node.name !== 'console') {
          return
        }
        consoleUsage.push(node)
      },
      'Program:exit'() {
        consoleUsage.forEach(checkDeniedMethodUsage)
      },
    }

    function report(property, consoleReference) {
      context.report({
        node: property,
        message: 'Using console is not allowed',
        fix(fixer) {
          return (
            consoleReference.name === 'console' &&
            fixer.replaceText(consoleReference, 'logger')
          )
        },
      })
    }

    function isDisallowedFunctionCall(identifier) {
      return looksLike(identifier, {
        parent: {
          type: 'MemberExpression',
          parent: {type: 'CallExpression'},
          property: {
            name: val =>
              !allowedMethods.includes(val) && disallowedMethods.includes(val),
          },
        },
      })
    }

    function checkDeniedMethodUsage(identifier) {
      if (isDisallowedFunctionCall(identifier)) {
        report(identifier.parent.property, identifier)
      } else {
        const variableDeclaratorParent = findParent(
          identifier,
          parent => parent.type === 'VariableDeclarator',
        )
        if (variableDeclaratorParent) {
          const references = context
            .getDeclaredVariables(variableDeclaratorParent)[0]
            .references.slice(1)

          references.forEach(reference => {
            if (
              findParent(
                reference.identifier,
                parent => parent.type === 'VariableDeclarator',
              )
            ) {
              checkDeniedMethodUsage(reference.identifier)

              return
            }

            if (
              !looksLike(reference, {
                identifier: {
                  parent: {
                    property: isDisallowedFunctionCall,
                  },
                },
              })
            ) {
              return
            }

            report(reference.identifier.parent.property, identifier)
          })
        }
      }
    }
  },
}

function findParent(node, test) {
  if (test(node)) {
    return node
  } else if (node.parent) {
    return findParent(node.parent, test)
  }
  return null
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

## Babel Plugin & AST

### Introducing Babel & AST

[Babel Plugin](https://astexplorer.net/#/gist/9fbfbf53b7d03a616321f4b53b5d52b0/latest)

### Challenge 6: Captains Log

`npm start ex.ba.0`

`npm start`

```js
// babel exercise 0 (captains-log)
// When you're finished with this exercise, run
//   "npm start exercise.babel.1"
//   to move on to the next exercise

/* eslint no-unused-vars:0 */

export default function(babel) {
  // I'm leavning this here for you because it's handy :)
  const {types: t} = babel

  return {
    name: 'captains-log',
    visitor: {
      CallExpression(path) {
        const isConsoleCall = looksLike(path, {
          node: {
            callee: {
              type: 'MemberExpression',
              object: {
                name: 'console',
              },
            },
          },
        })

        if (!isConsoleCall) {
          return
        }
        const start = path.node.loc.start
        path.node.arguments.unshift(
          t.stringLiteral(`${start.line}:${start.column}`),
        )
      },
    },
  }
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

### Challenge 7: Prefixing

`npm start ex.ba.1`

`npm start`

```js
// babel exercise 1 (captains-log)
// When you're finished with this exercise, run
//   "npm start exercise.babel.2"
//   to move on to the next exercise

export default function(babel) {
  const {types: t} = babel

  return {
    name: 'captains-log',
    visitor: {
      CallExpression(path) {
        if (
          !looksLike(path, {
            node: {
              callee: {
                type: 'MemberExpression',
                object: {
                  name: 'console',
                },
              },
            },
          })
        ) {
          return
        }

        const parentFunction = path.findParent(t.isFunctionDeclaration)
        const start = path.node.loc.start
        let prefix = ''
        if (parentFunction) {
          prefix += `${parentFunction.node.id.name} `
        }
        prefix += `${start.line}:${start.column}`
        path.node.arguments.unshift(t.stringLiteral(prefix))
      },
    },
  }
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

### Challenge 8: Arrow Function

`npm start ex.ba.2`

`npm start`

```js
// babel exercise 2 (captains-log)
// When you're finished with this exercise, run
//   "npm start exercise.babel.3"
//   to move on to the next exercise

export default function(babel) {
  const {types: t} = babel

  return {
    name: 'captains-log',
    visitor: {
      CallExpression(path) {
        if (
          !looksLike(path, {
            node: {
              callee: {
                type: 'MemberExpression',
                object: {
                  name: 'console',
                },
              },
            },
          })
        ) {
          return
        }
        let prefix = ''
        const parentFunctionName = getFunctionName(path)
        if (parentFunctionName) {
          prefix += parentFunctionName
        }
        const start = path.node.loc.start
        prefix += ` ${start.line}:${start.column}`
        path.node.arguments.unshift(t.stringLiteral(prefix.trim()))
      },
    },
  }
}

function getFunctionName(path) {
  const parentFunction = path.findParent(
    parent =>
      parent.isFunctionDeclaration() || parent.isArrowFunctionExpression(),
  )

  if (parentFunction) {
    if (
      parentFunction.isArrowFunctionExpression() &&
      parentFunction.parentPath.isVariableDeclarator()
    ) {
      return parentFunction.parent.id.name
    } else {
      return parentFunction.node.id.name
    }
  }
  return null
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

### Challenge 9: Divide Function

`npm start ex.ba.3`

`npm start`

```js
// babel exercise 3 (captains-log)
// When you're finished with this exercise, run
//   "npm start exercise.babel.4"
//   to move on to the next exercise

export default function(babel) {
  const {types: t} = babel

  return {
    name: 'captains-log',
    visitor: {
      CallExpression(path) {
        if (
          !looksLike(path, {
            node: {
              callee: {
                type: 'MemberExpression',
                object: {
                  name: 'console',
                },
              },
            },
          })
        ) {
          return
        }
        let prefix = ''
        const functionName = getFunctionName(path)
        if (functionName) {
          prefix += functionName
        }
        const start = path.node.loc.start
        prefix += ` ${start.line}:${start.column}`
        path.node.arguments.unshift(t.stringLiteral(prefix.trim()))
      },
    },
  }

  function getFunctionName(path) {
    const parentFunction = path.findParent(parent => {
      return (
        t.isFunctionDeclaration(parent) ||
        t.isArrowFunctionExpression(parent) ||
        t.isFunctionExpression(parent)
      )
    })
    if (!parentFunction) {
      return null
    }
    if (looksLike(parentFunction, {node: {id: t.isIdentifier}})) {
      return parentFunction.node.id.name
    } else if (t.isVariableDeclarator(parentFunction.parent)) {
      return parentFunction.parent.id.name
    }
    return null
  }
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

## Codemods & AST

### Introducing Codemods and AST

[demo](https://astexplorer.net/#/gist/e8ff56e9255ea78988194a79659a3b56/latest)

### Challenge 10: Upgrade Patterns

`npm start exercise.codemod.0`

`npm start`

```js
// codemod exercise 0 (ymnn-jquery)
// When you're finished with this exercise, run
//   "npm start exercise.codemod.1"
//   to move on to the next exercise

/* eslint no-unused-vars:0 */

export default function(babel) {
  // I'm leavning this here for you because it's handy :)
  const {types: t, template} = babel

  return {
    name: 'ymnn-jquery',
    visitor: {
      CallExpression(path) {
        const isJqueryCallExpression = looksLike(path, {
          node: {
            callee: {
              name: '$',
            },
          },
          parent: {
            type: 'MemberExpression',
            property: {
              name: 'hide',
            },
          },
        })

        if (!isJqueryCallExpression) {
          return
        }
        const overallPath = path.parentPath.parentPath
        const templateString = `EL.style.display = 'none'`
        const assignmentBuilder = template(templateString)
        const assignment = assignmentBuilder({
          EL: t.identifier(path.node.arguments[0].name),
        })
        overallPath.replaceWith(assignment)
      },
    },
  }
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

### Challenge 11: Swap Out Utillties

`npm start exercise.codemod.3`

`npm start`

```js
export default function(babel) {
  const {template, types: t} = babel

  return {
    name: 'ymnn-jquery',
    visitor: {
      Program: {
        enter(path, {file}) {
          file.set('assignedJQueryEls', new Set())
        },
        exit(path, {file}) {
          const jQueryElVariableDeclarators = file.get('assignedJQueryEls')
          Array.from(jQueryElVariableDeclarators).forEach(
            assignedCallExpression => {
              const declaratorPath = assignedCallExpression.parentPath
              const binding = declaratorPath.scope.getBinding(
                declaratorPath.node.id.name,
              )
              binding.referencePaths.forEach(referencePath => {
                if (
                  looksLike(referencePath, {
                    parentPath: {
                      type: 'MemberExpression',
                      parentPath: {
                        type: 'CallExpression',
                      },
                    },
                  })
                ) {
                  if (
                    !isSupportedJQueryFunctionCall(referencePath.parentPath)
                  ) {
                    return
                  }
                  const [el] = assignedCallExpression.node.arguments
                  handleSupportedJQueryFunctionCall(
                    el,
                    referencePath.parent.property.name,
                    referencePath.parentPath.parentPath,
                  )
                  declaratorPath.remove()
                }
              })
            },
          )
        },
      },
      CallExpression(path, {file}) {
        if (
          !looksLike(path, {
            node: {
              callee: {name: '$'},
            },
          })
        ) {
          return
        }
        if (!isSupportedJQueryFunctionCall(path.parentPath)) {
          if (t.isVariableDeclarator(path.parentPath)) {
            file.get('assignedJQueryEls').add(path)
          }
          return
        }
        const jqueryFunction = path.parentPath.node.property.name
        const pathToReplace = path.parentPath.parentPath
        const [el] = path.node.arguments
        handleSupportedJQueryFunctionCall(el, jqueryFunction, pathToReplace)
      },
    },
  }

  function handleSupportedJQueryFunctionCall(
    el,
    jqueryFunction,
    pathToReplace,
  ) {
    if (jqueryFunction === 'addClass') {
      updateAddClass(el, pathToReplace)
    } else {
      updateDisplay(el, pathToReplace, jqueryFunction)
    }
  }

  function updateDisplay(el, pathToReplace, jqueryFunction) {
    const templateString = `ELEMENT.style.display = DISPLAY;`
    const assignment = template(templateString)({
      ELEMENT: el,
      DISPLAY: t.stringLiteral(jqueryFunction === 'show' ? '' : 'none'),
    })
    pathToReplace.replaceWith(assignment)
  }

  function updateAddClass(el, pathToReplace) {
    const templateString = `ELEMENT.classList.add(CLASS_NAME);`
    const assignment = template(templateString)({
      ELEMENT: el,
      CLASS_NAME: pathToReplace.node.arguments,
    })
    pathToReplace.replaceWith(assignment)
  }
}

function isSupportedJQueryFunctionCall(memberExpression) {
  return looksLike(memberExpression, {
    key: 'callee',
    node: {
      property: {
        name: val => ['hide', 'show', 'addClass'].includes(val),
      },
    },
  })
}

function looksLike(a, b) {
  return (
    a &&
    b &&
    Object.keys(b).every(bKey => {
      const bVal = b[bKey]
      const aVal = a[bKey]
      if (typeof bVal === 'function') {
        return bVal(aVal)
      }
      return isPrimitive(bVal) ? bVal === aVal : looksLike(aVal, bVal)
    })
  )
}

function isPrimitive(val) {
  return val == null || /^[sbn]/.test(typeof val)
}
```

