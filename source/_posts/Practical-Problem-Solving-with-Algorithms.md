---
title: Practical Problem Solving with Algorithms
date: 2023-11-22 23:42:44
categories: [学习笔记]
tags: [FrontEndMasters, Algorithms]
---

# Practical Problem Solving with Algorithms

## Introduction

Disclaimer

1. Lab, not lecture // 实验，不是讲座
2. Using JS // 使用 JS

## Course Overview

TL;DW

1. Ask better clarifying questions // 提出更好的澄清问题
2. Balance CPU vs Memory // 平衡 CPU 和内存
3. Shape data structure(s) to the problem, not the other way // 将数据结构形状与问题相匹配，而不是相反
4. "premature optimization" VS. "immature optimization" // “过早优化”与“不成熟优化”

Anytime there is a problem present to you, your first job is to understand that problem completely, not to write the code. That's the last thing you do and that's optional. // 每当有一个问题出现在你面前时，你的第一件事就是完全理解这个问题，而不是写代码。这是你最后要做的事情，这是可选的。

<!-- more -->

## Primer

### Data Structures Overview

- Common Data Structures
  - Array, Stack, Queue
  - Set, Object, Map
  - Tree, Graph

### Algorithms & Techniques Overview

- Common Algorithms

  - Bubble Sort, Quick Sort
  - Tree Traversal, Path Finding
  - Binary Search

- Common Techniques

  - Iteration
  - Recursion
  - Indexes, References

- Recursion 可以看做带状态的循环，通过 stack 来保存状态

### Golden Weighted Ball Problem

There are 8 balls, one of them is lighter than the others. How to find the lighter one with only 2 times of weighing? // 有 8 个球，其中一个比其他的轻。如何只用 2 次称重找到那个轻的？

1. Compare one ball with another ball, if they are equal, then the lighter one is not in these 2 balls, otherwise, the lighter one is in these 2 balls. // 比较一个球和另一个球，如果它们相等，那么轻的那个不在这两个球中，否则，轻的那个在这两个球中。

   - Worst case: 4 times of weighing // 最坏情况：4 次称重
   - Best case: 1 time of weighing // 最好情况：1 次称重

2. Compare 4 balls with another 4 balls, the lighter is on the lighter side, then compare 2 balls with another 2 balls, the lighter is on the lighter side, then compare 1 ball with another 1 ball, the lighter is on the lighter side. // 比较 4 个球和另外 4 个球，轻的那个在轻的那边，然后比较 2 个球和另外 2 个球，轻的那个在轻的那边，然后比较 1 个球和另外 1 个球，轻的那个在轻的那边。

   - Worst case: 3 times of weighing // 最坏情况：3 次称重
   - Best case: 3 times of weighing // 最好情况：3 次称重

3. Compare 3 balls with another 3 balls, if they are equal, then the lighter one is in the other 2 balls, otherwise, the lighter one is in these 3 balls. Then compare 1 balls with another 1 ball, the lighter is on the lighter side. Otherwise the rest 1 ball is the lighter. // 比较 3 个球和另外 3 个球，如果它们相等，那么轻的那个在另外 2 个球中，否则，轻的那个在这 3 个球中。然后比较 1 个球和另外 1 个球，轻的那个在轻的那边。否则，剩下的 1 个球就是轻的。
   - Worst case: 2 times of weighing // 最坏情况：2 次称重
   - Best case: 2 times of weighing // 最好情况：2 次称重

- 解决这个问题的关键是正确的分析问题，我们没办法分割每个球，所以每次称重的时候，我们要将球分成数量相等的两部分，这样才能保证每次称重的时候，我们都能找到轻的那个球，在这个限定条件下，只有以上 3 种解法。

## Example Problem

### Packing Algorithm & Collision Detection

- Assume we have 17 boxes, each box has a width and a height, we want to pack these boxes into a container, the container should be as small as possible. // 假设我们有 17 个盒子，每个盒子都有宽度和高度，我们想把这些盒子装进一个容器里，这个容器应该尽可能小。

### Tetris, Puzzles & Size Comparison

- 怎么实现一个俄罗斯方块的自动下落算法？
  - 目标：最高分
  - 限制：不使用 Machine Learning

## Periodic Table Speller

### Setup

- [Repo](https://github.com/getify/workshop-periodic-table/)

### Lookup Function

```js
function lookup(elementSymbol) {
  // TODO: return the element entry based on specified
  // symbol (case-insensitive)
  return elements.find(
    (element) => element.symbol.toLowerCase() === elementSymbol.toLowerCase()
  );
}
```

### Check Function

```js
function check(inputWord) {
  // TODO: determine if `inputWord` can be spelled
  // with periodic table symbols; return array with
  // them if so (empty array otherwise)
  if (inputWord.length > 0) {
    for (const element of elements) {
      const symbol = element.symbol.toLowerCase();
      if (symbol.length <= inputWord.length) {
        if (inputWord.slice(0, symbol.length) === symbol) {
          if (inputWord.length > symbol.length) {
            const result = check(inputWord.slice(symbol.length));
            if (result.length > 0) {
              return [symbol, ...result];
            }
          } else {
            return [symbol];
          }
        }
      }
    }
  }
  return [];
}
```

### Optimizing the Lookup Function

```js
// 通过建立索引的方式优化 lookup 函数
const symbolsMap = elements.reduce((acc, element) => {
  acc.set(element.symbol.toLowerCase(), element);
  return acc;
}, new Map());
function lookup(elementSymbol) {
  // TODO: return the element entry based on specified
  // symbol (case-insensitive)
  return symbolsMap.get(elementSymbol.toLowerCase());
}
```

### Finding Candidates

```js
function findCandidates(inputWord) {
  // The reason why not use Set is because the order is matter.
  const oneLetterWords = [];
  const twoLetterWords = [];

  for (let i = 0; i < inputWord.length; i++) {
    if (
      symbolsMap.has(inputWord[i].toLowerCase()) &&
      !oneLetterWords.includes(inputWord[i])
    ) {
      oneLetterWords.push(inputWord[i]);
    }
    if (i < inputWord.length - 1) {
      const twoLetterWord = inputWord.slice(i, i + 2);
      if (
        symbolsMap.has(twoLetterWord.toLowerCase()) &&
        !twoLetterWords.includes(twoLetterWord)
      ) {
        twoLetterWords.push(twoLetterWord);
      }
    }
  }

  return [...twoLetterWords, ...oneLetterWords];
}
```

### spellWord Function with Character Sets

```js
function check(inputWord) {
  const candidates = findCandidates(inputWord);
  return spellWord(inputWord, candidates);
}

/**
 *
 * @param {string} inputWord
 * @param {string[]} candidates
 * @returns
 */
function spellWord(inputWord, candidates) {
  if (inputWord.length === 0) {
    return [];
  }
  if (inputWord.length === 1) {
    if (candidates.includes(inputWord)) {
      return [inputWord];
    }
    return [];
  }
  {
    const two = inputWord.slice(0, 2);
    const rest = inputWord.slice(2);
    if (candidates.includes(two)) {
      if (rest.length > 0) {
        const result = [two, ...spellWord(rest, candidates)];
        if (result.join('') === inputWord) {
          return result;
        }
      } else {
        return [two];
      }
    }
  }
  {
    const one = inputWord.slice(0, 1);
    const rest = inputWord.slice(1);
    if (candidates.includes(one)) {
      if (rest.length > 0) {
        const result = [one, ...spellWord(rest, candidates)];
        if (result.join('') === inputWord) {
          return result;
        }
      } else {
        return [one];
      }
    }
  }

  return [];
}
```

## Chessboard Diagonals

### Drawing the Chessboard

- [Repo](https://github.com/getify/workshop-chess-diagonals)

```js
function draw(boardEl) {
  // TODO: draw the chessboard, 8 rows (divs)
  // of 8 tiles (divs) each, inserting all DOM
  // elements into `boardEl` div
  const rows = Array.from({ length: 8 }, (_, i) => i + 1);
  const rowEls = rows.map((row) => {
    const tiles = Array.from({ length: 8 }, (_, i) => i + 1).map((tile) => {
      const tileEl = document.createElement('div');
      tileEl.classList.add('tile');
      tileEl.dataset.row = row;
      tileEl.dataset.col = tile;
      return tileEl;
    });

    const rowEl = document.createElement('div');
    rowEl.classList.add('row');
    rowEl.append(...tiles);
    return rowEl;
  });

  boardEl.append(...rowEls);
}
```

### Highlighting the Diagonals

```js
/**
 * @param {HTMLDivElement} tileEl
 */
function highlight(tileEl) {
  // TODO: clear previous highlights (if any) and
  // then find the tiles in the two diagonals
  // (major and minor) that `tileEl` belongs to,
  // to highlight them via CSS class "highlighted"
  const row = Number(tileEl.dataset.row);
  const col = Number(tileEl.dataset.col);
  const tiles = Array.from(document.querySelectorAll('.tile'));
  tiles.forEach((tile) => tile.classList.remove('highlighted'));
  const majorDiagonal = tiles.filter(
    (tile) => Number(tile.dataset.row) - Number(tile.dataset.col) === row - col
  );
  const minorDiagonal = tiles.filter(
    (tile) => Number(tile.dataset.row) + Number(tile.dataset.col) === row + col
  );
  const diagonals = [...majorDiagonal, ...minorDiagonal];
  diagonals.forEach((tile) => tile.classList.add('highlighted'));
}
```

### Refactoring

```js
const diagonals = new Array(30).fill(0).map(() => []);
const tileDiagonals = new Map();

let highLightedTiles = [];

/**
 *
 * @param {HTMLDivElement} boardEl
 */
function draw(boardEl) {
  // TODO: draw the chessboard, 8 rows (divs)
  // of 8 tiles (divs) each, inserting all DOM
  // elements into `boardEl` div
  new Array(8).fill(0).forEach((_, row) => {
    const rowEl = document.createElement('div');
    rowEl.className = 'row';
    new Array(8).fill(0).forEach((_, col) => {
      const tileEl = document.createElement('div');
      tileEl.className = 'tile';
      rowEl.appendChild(tileEl);

      const majorDiagonal = diagonals[7 - row + col];
      majorDiagonal.push(tileEl);
      const minorDiagonal = diagonals[row + col + 15];
      minorDiagonal.push(tileEl);

      tileDiagonals.set(tileEl, [majorDiagonal, minorDiagonal]);
    });
    boardEl.appendChild(rowEl);
  });
}

/**
 * @param {HTMLDivElement} tileEl
 */
function highlight(tileEl) {
  // TODO: clear previous highlights (if any) and
  // then find the tiles in the two diagonals
  // (major and minor) that `tileEl` belongs to,
  // to highlight them via CSS class "highlighted"
  highLightedTiles.forEach((tile) => {
    tile.classList.remove('highlighted');
  });

  highLightedTiles = tileDiagonals.get(tileEl);
  highLightedTiles.forEach((diagonal) => {
    diagonal.forEach((tile) => {
      tile.classList.add('highlighted');
    });
  });
}
```

## Knight's Dialer

- [Repo](https://github.com/getify/workshop-knights-dialer)

### Reachable Keys Function

```js
function reachableKeys(startingDigit) {
  // TODO: return which digits a Knight's move
  // can hop to from a given starting digit/key
  //
  // e.g. 3 -> [ 4, 8 ]
  //      4 -> [ 3, 9, 0 ]
  //      5 -> []

  // brute force
  const reachable = [];
  for (const [rowIndex, row] of dialpad.entries()) {
    const colIndex = row.indexOf(startingDigit);
    if (colIndex !== -1) {
      // check all possible moves
      const rowMoves = [-2, -1, 1, 2];
      const colMoves = [-2, -1, 1, 2];
      for (const rowMove of rowMoves) {
        for (const colMove of colMoves) {
          // check if move is valid
          if (Math.abs(rowMove) !== Math.abs(colMove)) {
            const newRow = rowIndex + rowMove;
            const newCol = colIndex + colMove;
            if (
              newRow >= 0 &&
              newRow < dialpad.length &&
              newCol >= 0 &&
              newCol < dialpad[newRow].length &&
              dialpad[newRow][newCol] !== null
            ) {
              reachable.push(newDigit);
            }
          }
        }
      }
    }
  }

  return reachable;
}
```

### Optimizing with a Data Structure

We can call `reachableKeys` function 10 times, and cache the result, so that we can get the result in O(1) time. // 我们可以调用 `reachableKeys` 函数 10 次，并缓存结果，这样我们就可以在 O(1) 时间内得到结果。

```js
const reachableKeysCache = [
  [4, 6],
  [6, 8],
  [7, 9],
  [4, 8],
  [0, 3, 9],
  [],
  [0, 1, 7],
  [2, 6],
  [1, 3],
  [2, 4],
];

function reachableKeys(startingDigit) {
  return reachableKeysCache[startingDigit];
}
```

For some problems, we can pre-compute the result, and store it in a data structure, so that we can get the result in O(1) time. For these problems, we should pre-compute the result as much as possible instead of computing it in runtime. // 对于某些问题，我们可以预先计算结果，并将其存储在数据结构中，这样我们就可以在 O(1) 时间内得到结果。对于这些问题，我们应该尽可能多地预先计算结果，而不是在运行时计算结果。

### Recursive Traversal

```js
countPaths(4, 6) =
  countPaths(3, 5) +
  countPaths(9, 5) +
  countPaths(0, 5) +

countPaths(3, 5) =
  countPaths(4, 4) +
  countPaths(8, 4) +

countPaths(4, 4) =
  countPaths(3, 3) +
  countPaths(9, 3) +
  countPaths(0, 3) +

countPaths(3, 3) =
  countPaths(4, 2) +
  countPaths(8, 2) +

countPaths(4, 2) =
  countPaths(3, 1) +
  countPaths(9, 1) +
  countPaths(0, 1) +

countPaths(3, 1) =
  countPaths(4, 0) +
  countPaths(8, 0) +
```

```js
function countPaths(startingDigit, hopCount) {
  // TODO: given the digit/key to start from and
  // the number of hops to take, return a count
  // of all the possible paths that could be
  // traversed

  // Breadth first search
  let queue = [startingDigit];

  while (hopCount > 0) {
    let nextQueue = [];
    while (queue.length > 0) {
      const currentDigit = queue.shift();
      const keys = reachableKeys(currentDigit);
      nextQueue = nextQueue.concat(keys);
    }
    queue = nextQueue;
    hopCount--;
  }

  return queue.length;
}

function countPaths(startingDigit, hopCount) {
  // TODO: given the digit/key to start from and
  // the number of hops to take, return a count
  // of all the possible paths that could be
  // traversed

  // Depth first search
  if (hopCount === 0) {
    return 1;
  }

  return reachableKeys(startingDigit).reduce((acc, digit) => {
    return acc + countPaths(digit, hopCount - 1);
  }, 0);
}
```
