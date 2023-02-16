---
title: Introduction to Data Visualization with d3.js v4
date: 2019-10-09 16:21:38
categories: [学习笔记]
tags: [FrontEndMasters, Data Visualization, D3.js]
---
## d3.js

### Introduction

[slides](http://slides.com/shirleywu/fm-d3intro#/)

+ AGENDA
  + D3 Ecosystem
  + Selections
  + Data binding
  + Enter-append
  + Scales
  + Shapes
  + Update & Exit
  + Transitions
  + Forces
<!-- more -->
### d3.js Introduction

+ D3 Ecosystem
  + [API Reference](https://github.com/d3/d3/blob/master/API.md)
    + [Selections](https://github.com/d3/d3-selection)
    + [Scales](https://github.com/d3/d3-scale) & [Axes](https://github.com/d3/d3-axis)
    + [Shapes](https://github.com/d3/d3-shape)
    + [Forces](https://github.com/d3/d3-force)
  + [bl.ocks](https://bl.ocks.org/) & [Blockbuilder](https://blockbuilder.org/)([search](https://blockbuilder.org/search))

## API Reference

### Selection and Data

[selection](https://github.com/d3/d3-selection)

### Selection adn Data Demo
<!-- markdownlint-disable md033 -->
<details>
<summary>HTML</summary>

```html
<svg>
  <rect />
  <rect />
  <rect />
  <rect />
  <rect />
</svg>
<script src="./app.js"></script>
```

</details>

<details>
<summary>JS</summary>

```js
import { selectAll } from 'd3'

const data = [100, 250, 175, 200, 120]
const rectWidth = 100
const height = 300
selectAll('rect')
  .data(data)
  .attr('x', (d, i) => i * rectWidth)
  .attr('y', (d) => height - d)
  .attr('width', rectWidth)
  .attr('height', (d) => d)
  .attr('fill', '#00f')
  .attr('stroke', '#fff')
```

</details>

<details>
<summary>CSS</summary>

```css
body {
  margin: 0;
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
svg {
  width: 100%;
  height: 100%;
}
```

</details>

### Enter-Append

```html
<svg></svg>
<script>
  var rectWidth = 100;
  var height = 300;
  var data = [100, 250, 175, 200, 120];
  var svg = d3.select('svg');

  svg.selectAll('rect')
    .data(data)
    .enter().append('rect')
    .attr('x', (d, i) => i * rectWidth)
    .attr('y', d => height - d)
    .attr('width', rectWidth)
    .attr('height', d => d)
    .attr('fill', 'blue')
    .attr('stroke', '#fff');
</script>
```

+ 可以发现，上述代码中HTML结构中并没有rect元素，那么我们在使用selectAll选取了什么？
+ 答案是空的选择，类似promise，提供了一个占位符
+ 我们在之后使用了`.enter().append('rect')`来追加rect元素并在之后对其属性值进行操作

### Scales and Axes

```js
d3.scaleLinear()
  .domain([min, max]) // input
  .range([min, max]) // output
```

+ scale
  + mapping from data attributes(domain) to display(range)
    + data -> x-value
    + value -> y-value
    + value -> opacity
    + etc.

```js
// get min/max
var min = d3.min(data, d => d[city])
var max = d3.max(data, d => d[city])

// or use extent, which gives back [min, max]
var extent = d3.extent(data, d => d[city])

var yScale = d3.scaleLinear()
  .domain(extent)
  .range([height, 0])
```

+ Scales use often

```js
// continuous
d3.scaleLinear()
d3.scaleLog()
d3.scaleTime()

// ordinal
d3.scaleBand()
```

+ Axes

```js
var yAxis = d3.axisLeft()
  .scale(yScale) // pass in a scale

d3.select('svg')
  // create a group element we can translate
  // so that the axis will be visible in SVG
  .append('g')
  .attr('transform', 'translate(40, 20)')
  // selection.call(yAxis) is the same as yAxis(selection)
  // and an axis will be created within the selection
  .call(yAxis)
```

[code example](https://blockbuilder.org/sxywu/8045c7e4f4aebce27722e23eec960a6b)

### Challenge: Creating a Chart

[exercise link](https://blockbuilder.org/sxywu/909992222842cdbda009006e456a23b0)

[solution](https://blockbuilder.org/blingblingredstar/84aba6b5a8561669a91467dd988e16d9)

## Shapes

### Shapes Introduction

+ SVG elements
  + rect
    + x: x-coordinate of top-left
    + y: y-coordinate of top-left
    + width
    + height
  + circle
    + cx: x-coordinate of center
    + cy: y-coordinate of center
    + r: radius
  + text
    + x: x-coordinate
    + y: y-coordinate
    + dx: x-coordinate offset
    + dy: y-coordinate offset
    + text-anchor: horizontal text
    + alignment
  + path
    + d: path to follow
    + Moveto, Lineto, Curveto, Arcto
  + [SVG Paths](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths)
  + [SVG Transform](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/transform)

### d3-shape Module

+ d3-shape calculates the path attribute so we don't have to

+ d3.line()

```js
// input: array of objects
var data = [
  {date: new Date(2007, 3, 24), value: 93.24},
  {date: new Date(2007, 3, 25), value: 95.35},
  {date: new Date(2007, 3, 26), value: 98.84},
  {date: new Date(2007, 3, 27), value: 99.92},
  {date: new Date(2007, 3, 30), value: 99.80},
  {date: new Date(2007, 4,  1), value: 99.47},
  …
];

var line = d3.line()
  .x((d) => { return xScale(d.date); })
  .y((d) => { return yScale(d.value); });

d3.select('svg')
  .append('path')
  .attr('d', line(data)); // output: path that connects each point(object) with lines or curves
```

+ d3.pie()

```js
const pie = d3.pie()

// input
const data = [1, 1, 2, 3, 5, 8, 13, 21]

pie(data)

// output
[
  {"data":  1, "value":  1, "startAngle": 6.050474740247008, "endAngle": 6.166830023713296, "padAngle": 0},
  {"data":  1, "value":  1, "startAngle": 6.166830023713296, "endAngle": 6.283185307179584, "padAngle": 0},
  {"data":  2, "value":  2, "startAngle": 5.817764173314431, "endAngle": 6.050474740247008, "padAngle": 0},
  {"data":  3, "value":  3, "startAngle": 5.468698322915565, "endAngle": 5.817764173314431, "padAngle": 0},
  {"data":  5, "value":  5, "startAngle": 4.886921905584122, "endAngle": 5.468698322915565, "padAngle": 0},
  {"data":  8, "value":  8, "startAngle": 3.956079637853813, "endAngle": 4.886921905584122, "padAngle": 0},
  {"data": 13, "value": 13, "startAngle": 2.443460952792061, "endAngle": 3.956079637853813, "padAngle": 0},
  {"data": 21, "value": 21, "startAngle": 0.000000000000000, "endAngle": 2.443460952792061, "padAngle": 0}
]
```

+ d3.arc()

```js
const pie = {
  "data":  1,
  "value":  1,
  "startAngle": 6.050474740247008,
  "endAngle": 6.166830023713296,
  "padAngle": 0
}

const arc = d3.arc()
  .innerRadius(0)
  .outerRadius(100)
  .startAngle(d => d.startAngle)
  .endAngle(d => d.endAngle)

arc(pie)
// M-23.061587074244123,-97.30448705798236A100,100,0,0,1,-11.609291412523175,-99.32383577419428L0,0Z
```

[code](https://blockbuilder.org/sxywu/95eb2a3b8e880c6cc2e951b08b4c5b1a)

### Challenge: Line Graph

[exercise link](https://blockbuilder.org/blingblingredstar/fe9a7a50dcbf11e33beb851f363a35be)

## Update and Exit

### enter() and update()

[Object constancy](https://bost.ocks.org/mike/constancy/)

```js
// bars includes update selection
var bars = svg.selectAll('rect')
  .data(data, d => d /*key function: controls which datum is assigned to which element 类似react的key属性*/);

// exit
bars.exit().remove();

// enter
var enter = bars.enter().append('rect')
  .attr('width', rectWidth)
  .attr('stroke', '#fff');

// enter + update
bars = enter.merge(bars)
  .attr('x', (d, i) => i * rectWidth)
  .attr('y', d => height - d)
  .attr('height', d => d)
  .attr('fill', d => colors(d));
```

### exit() and merge()

+ Exit selection

```js
// exit
bars.exit().remove();
```

+ Enter selection: chain attributes that don't depend on data

```js
// enter
var enter = bars.enter().append('rect')
  .attr('width', rectWidth)
  .attr('stroke', '#fff');
```

+ Combines 2 selections into one

```js
 bars = enter.merge(bars)
    // Enter+update selection: chain attrs dependent on data
    .attr('x', (d, i) => i * rectWidth)
    .attr('y', d => height - d)
    .attr('height', d => d)
    .attr('fill', d => colors(d));
```

[exercise link](https://blockbuilder.org/blingblingredstar/08db9828c664db156c84901721e86762)

## Transitions

### Animating Transitions

+ Transitions
  + Emphasize changes in state(object constancy)
    + [example 1](http://sxywu.com/80k/)
    + [example 2](http://sxywu.com/obamas/)

+ Transitions

```js
const t = d3.transition().duration(1000) // Define transition, syncs animation everywhere it's used
const svg = d3.select('svg')

let bars = svg.selectAll('rect').data(data, (data) => data)

// exit
bars
  .exit()
  .transition(t) // Animate height down to 0 before removing
  .attr('y', height)
  .attr('height', 0)
  .remove()

// enter
const enter = bars
  .enter()
  .append('rect')
  .attr('width', rectWidth)
  .attr('stroke', '#fff')
  .attr('y', height) // Same attributes set before .transition: state A to transition from
  // If attributes not specified for A, d3 extrapolates from default
// enter + update
bars = enter
  .merge(bars)
  .attr(x, (data, i) => i * rectWidth)
  .attr('fill', (data) => colors(data))
  .transition(t) // Animate remaining <rect>'s height to their next state
  .attr('y', (data) => height - data) // Everything after `.transition()`:
  .attr('height', (data) => data) // state B you want to transition to
```

+ Transitions
  + go from state A to state B

+ [code example](https://blockbuilder.org/sxywu/34ea25433940f2c67ab9336d65e11a36)

### Challenge: Animating Transitions

[exercise](https://blockbuilder.org/sxywu/87352259773cd6d68b8f867241d8c638)

[solution](https://blockbuilder.org/blingblingredstar/a6e454ff27ced58220c09b03984896c2)

## Force Layout

### d3-force Module

+ [Understanding the Force](https://medium.com/@sxywu/understanding-the-force-ef1237017d5)

### Position and Collision

+ Forces
  + [Position](https://github.com/d3/d3-force#positioning)(forceX, forceY)
  + [Collision](https://github.com/d3/d3-force#collision)(forceCollide)

## D3 + React

### Working Together

| D3                        | React                                        |
| ------------------------- | -------------------------------------------- |
| interactive dataviz       | single-page apps whose data change over time |
| enter-update-exit pattern | virtual DOM diffing                          |

+ They both solve the same problem

+ Approach #1
  + Use case: App with a small dataset and simple visualization
    + React for structure
    + D3 for data calculation
    + React for rendering
  + Pro
    + Clean, easy to reason about
  + Con
    + lifecycle for every tick -> unperforment
    + Cannot use D3 functions that need access to DOM

+ Approach #2
  + Use case: App with a small dataset but highly interactive visualization
    + React for structure
    + D3 for data calculation
    + D3 and React for rendering
      + React for element
      + D3 for attribute
    + [On D3, React, and a little bit of Flux](https://medium.com/@sxywu/on-d3-react-and-a-little-bit-of-flux-88a226f328f3)
  + Pro
    + Takes advantage of respective strengths
  + Con
    + React component around all the nodes -> unperforment
    + Makes people uncomfortable

+ Approach #3
  + Use case: App with a large dataset and frequently updating visualization
    + React for structure
    + D3 for data calculation
    + D3 for rendering
  + Pro
    + The visualization scales!
    + Use all the d3 functions
  + Con
    + Why you even React, bro?

+ Lessons learned
  + Never ever have React and D3 controlling same parts of the DOM(or else nasty bugs)
  + Know the rules to break them
  + Assess the needs of the project, then figure out the combination of D3 + React that make sense
