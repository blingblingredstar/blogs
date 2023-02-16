---
title: Data Visualization for React Developers
date: 2019-10-23 15:56:36
categories: 学习笔记
tags: [FrontEndMasters, React, Data Visualization, D3.js]
---
## Introduction

+ [slides](https://slides.com/shirleywu/deck-11#/)

+ Workshop Goal
  + Use D3 to calculate data
  + React to render visualizations

+ WHY?
  + D3's learning curve
  + enter - update - exit == React virtual DOM(let React do the hard work!)

+ Agenda
  + Basic chart types and when to use them
  + The making of a chart with SVG
  + Going from data to SVG shapes
  + Using React to render SVG and Canvas
  + Exceptions and finishing touches
<!-- more -->
## The Basic Chart Types

### Introduction to Different Data Types

+ Data types
  + Categorical(genres)
  + Ordinal(t-shirt sizes)
  + Quantitative(temperatures)
  + Temporal(dates)
  + Spatial(cities)

+ Basic Charts and When to Use Them
  + Bar chart
    + For categorical comparisons
    + Domain: categorical
    + Range: quantitative
  + Histogram
    + For categorical distributions
    + Domain: quantitative bins
    + Range: frequency of quantitative bin
  + Scatterplot
    + For correlation
    + 2 attributes and the relationship between their quantitative values
  + Line chart
    + For temporal trends
    + Domain: temporal
    + Range: quantitative
  + Tree
    + For hierarchy
    + Parent-child relations
    + Multiple tiers of category
  + Node-link diagram
    + For connection
    + Relationship between entities
  + Chloropleth
    + For special trends
    + Domain: spatial regions
    + Range: quantitative
      + Best for:
        + Regional patterns
        + Only one variable
        + Relative data(normalize for population)
      + Not good for:
        + Subtle difference in data

### Pie Charts: Do's and Do not's

+ Basic Charts and When to Use Them
  + Pie chart
    + For hierarchical part-to-whole
    + Best for:
      + When values are around 25%, 30%, or 75%
      + 3 or 4 values
    + Not good for:
      + Comparing fine differences
      + Multiply totals

### Course Demonstration Review

+ What we'll be building today
  + [Example](http://sxywu.com/react-d3-example/)
  + [data](https://raw.githubusercontent.com/sxywu/react-d3-example/master/public/sf.json)

## The Masking of a Chart

### Introduction to SVG

+ SVG Elements

| rect                        | circle                     | text                         | path                           |
| --------------------------- | -------------------------- | ---------------------------- | ------------------------------ |
| x: x-coordinate of top-left | cx: x-coordinate of center | x: x-coordinate              | d: path to follow              |
| y: y-coordinate of top-left | cy: y-coordinate of center | y: y-coordinate              | Moveto, Lineto, CurveTo, Arcto |
| width                       | r: radius                  | dx: x-coordinate offset      |                                |
| height                      |                            | dy: y-coordinate offset      |                                |
|                             |                            | text-anchor: horizontal text |                                |
|                             |                            | alignment                    |                                |

### Weather Data Chart Examples

+ Making of a bar chart
  + 365 `<rect />`'s
  + x: date
  + y: high temp
  + height: low - high temp
  + fill: average temp

+ Making of a line chart
  + 2 `<path />`'s
  + d: line commands that connect points
  + For each point
    + x: date
    + y: temperature
    + fill: red for high temp blue for low temp

+ Making of a radial chart
  + 365 `<path />`'s
  + d: line + curve commands to make slices
  + for each slice
    + angle: day
    + inner radius: low temp
    + outer radius: high temp
    + fill: average temp

### Exercise: Data to SVG Shapes

+ [Observable book links](https://beta.observablehq.com/@sxywu/data-visualization-for-react-developers-starter)

### Going from Data to SVG Shapes

+ Going from data to SVG shapes
  + One of the(many) things D3 is good for!

+ Data to SVG:
  + scales

    ```js
    d3.scaleLinear()
      .domain([min, max]) // input
      .range([min, max]) // output
    ```

  + scale: mapping from data attributes(domain) to display(range)
    + date -> x-value
    + value -> y-value
    + value -> opacity
    + etc.

+ Data to SVG:
  + scales

    ```js
    // get min/max
    const width = 800
    const height = 600
    const data = [
      {date: new Date('01-01-2015'), temp: 0},
      {date: new Date('01-01-2017'), temp: 3},
    ]

    const min = d3.min(data, d => d.date)
    const max = d3.max(data, d => d.date)

    // or use extent, which gives back [min, max]

    const extent = d3.extent(data, d => d.temp)

    const xScale = d3.scaleTime()
      .domain([min, max])
      .range([0, width])

    const yScale = d3.scaleLinear()
      .domain(extent)
      .range([height, 0])
    ```

+ Data to SVG:
  + Scales I use often
  + Quantitative
    + Continuous domain Continuous range
      + scaleLinear
      + scaleLog
      + scaleTime
    + Continuous domain Discrete range
      + scaleQuantize
  + Catetorical
    + Discrete domain Discrete range
      + scaleOrdinal
    + Discrete domain Continuous range
      + scaleBand

### Exercise: Going from Data to SVG

+ [Starter notebook](https://beta.observablehq.com/@sxywu/data-visualization-for-react-developers-starter)
+ [Full notebook](https://beta.observablehq.com/@sxywu/data-visualization-for-react-developers-full)
<!-- markdownlint-disable md033 -->
<details>
<summary>solution</summary>

```js
const extent = d3.extent(data, ({date}) => date)
const xScale = d3.scaleTime()
  .domain(extent)
  .range([0, width])
const [min, max] = d3.extent(data, d => d.high)
const yScale = d3.scaleLinear()
  .domain([Math.min(min, 0), max])
  .range([height, 0])

//  3. map avg temp to color
const colorExtent = d3.extent(data, d => d.avg).reverse()
const colorScale = d3.scaleSequential()
  .domain(colorExtent)
  .interpolator(d3.interpolateRdYlBu)

return data.map(d => {
  return {
    x: xScale(d.date),
    y: yScale(d.high),
    height: yScale(d.low) - yScale(d.high),
    fill: colorScale(d.avg),
  }
})
```

</details>

### Creating Line Chart

+ [current slide](https://slides.com/shirleywu/deck-11#/27)

+ Data to SVG:
  + line chart
    + path
    + d: path to follow
    + MoveTO, LineTo, CurveTo, ArcTo

+ Data to SVG:
  + d3.line()

    ```js
    var data = [
      {date: '2007-3-24', value: 93.24},
      {date: '2007-3-25', value: 95.35},
      {date: '2007-3-26', value: 98.84},
      {date: '2007-3-27', value: 99.92},
      {date: '2007-3-28', value: 99.80},
      {date: '2007-3-29', value: 99.47},
      …
    ];

    var line = d3.line()
      .x((d) => { return xScale(new Date(d.date)); })
      .y((d) => { return yScale(d.value); });

    line(data)
    ```

### Exercise: Creating Line Chart

+ [Starter Notebook](https://observablehq.com/@sxywu/data-visualization-for-react-developers-starter)

<details>
<summary>solution</summary>

```js
lineChartData = {
  // ...
  const xExtent = d3.extent(data, d => d.date)
  const xScale = d3.scaleTime()
    .domain(xExtent)
    .range([0, width])
  // min: low temp  max: high temp
  const highMax = d3.max(data, d => d.high)
  const lowMin = d3.min(data, d => d.low)
  const yScale = d3.scaleLinear()
    .domain([lowMin, highMax])
    .range([height, 0])
  
  const line = d3.line()
    .x(d => xScale(d.date))
  
  return [
    {
      path: line.y(d => yScale(d.high))(data),
      fill: 'red',
    }, {
      path: line.y(d => yScale(d.low))(data),
      fill: 'blue',
    }
  ]
}
```

</details>

### Creating a Radial Chart

+ Data to SVG:
  + Radial Chart
    + path
    + d: path to follow
    + MoveTo, LineTo, CurveTo, ArcTo

+ Data to SVG:
  + d3.arc()

```js
const pie = {
  data: 1,
  value: 1,
  startAngle: 6.050474740247008,
  endAngle: 6.166830023713296,
}

const arc = d3.arc()
  .innerRadius(0)
  .outerRadisu(100)
  .startAngle(d => d.startAngle)
  .endAngle(d => d.endAngle)

arc(pie)
// M-23.061587074244123,-97.30448705798236A100,100,0,0,1,-11.609291412523175,-99.32383577419428L0,0Z
```

### Exercise: Radial Chart

+ [Starter Notebook](https://beta.observablehq.com/@sxywu/data-visualization-for-react-developers-starter)

<details>
<summary>solution</summary>

```js
radialChartData = {
  // ...
  const radiusScale = d3.scaleLinear()
    .domain([
      d3.min(data, d => d.low),
      d3.max(data, d => d.high)
    ])
    .range([0, width / 2])
  // get the angle for each slice
  // 2PI / 365
  const perSliceAngle = (2 * Math.PI) / data.length
  // startAngle = index * perSliceAngle
  // endAngle = (index + 1) * perSliceAngle
  
  const arcGen = d3.arc()
  const pathString = arcGen({
    startAngle: 0 * perSliceAngle,
    endAngle: 1 * perSliceAngle,
    innerRadius: radiusScale(data[0].low),
    outerRadius: radiusScale(data[0].high),
  })
  
  const colorScale = d3.scaleSequential()
    .domain(d3.extent(data, d => d.avg).reverse())
    .interpolator(d3.interpolateRdYlBu)
  
  return data.map((d, i) => {
    const path = arcGen({
      startAngle: i * perSliceAngle,
      endAngle: (i + 1) * perSliceAngle,
      innerRadius: radiusScale(d.low),
      outerRadius: radiusScale(d.high)
    })
    return {
      path,
      fill: colorScale(d.avg)
    }
  })
}
```

</details>

## Rendering with React

### Breaking Down D3 API

+ [current slide](https://slides.com/shirleywu/deck-11#/33)

+ Some functions are really helpful for getting data ready for D3's scale/shape/layout functions

### D3 Manipultations and Interactions

+ [current slide](https://slides.com/shirleywu/deck-11#/37)

### React Renders

+ [current slide](https://slides.com/shirleywu/deck-11#/39)

+ React renders: Architecture
  + Division of responsibilities:
    + Chart component
      1. Gets passed in raw data as prop
      2. Translates raw data to screen space
      3. Renders the calculated data
      + Manages state for interactions that don't require redrawing of the chart(hover, click)
    + Root component
      + Manages updates to raw data
      + Manages state for interactions that require redrawing of charts(filter, aggregate, sort, etc.)
  + Where to calculate data:
    + getDerivedStateFromPorps
      + Pro: simple and straightforward
      + Con: asynchronous, race conditions if not careful
    + Render
      + Pro: very straightforward
      + Con: will recalculate on every lifecycle
    + componentDidMount & componentDidUpdate
      + Pro: no race condition
      + Con: less straightforward
  + Assumes:
    + React manages state(no redux or similar)
    + Multiple charts that all share some part of the data or state
  + Main takeaway:
    + D3 calculations can go anywhere(that makes sense for your project) as long as React can access it in its render function

    ```js
    componentDidUpdate(nextProps, nextState) {
      // prevents infinite loop
      if (this.props.someId !== nextProps.someId) {
        this.calculateData();
      }
    }

    componentDidMount() {
      // Make sure component is rendered first
      if (this.SVG.current) {
        this.calculateData();
      }
    }

    calculateData() {
      ...
      this.setState({data})
    }
    ```

### Exercise: React and Bar Chart

+ [starter code](https://codesandbox.io/s/kop49w9r2v?module=%2Fsrc%2Fvisualizations%2FChart.js)

+ [solution](https://codesandbox.io/s/z3n69yrk2m)

### Exercise: React and Radial Chart

+ [starter code](https://codesandbox.io/s/l4p0k6m86q?module=%2Fsrc%2Fvisualizations%2FChart.js)

+ [solution](https://codesandbox.io/s/ppllx51ozq)

## The Finishing Touches

### The Three Exceptions

+ [current slide](https://slides.com/shirleywu/deck-11#/45)

+ Functions where D3 needs access to the DOM
  + Axis
  + Transitions
  + Brush
  + Zoom

+ D3 renders
  1. Instantiate D3 function in componentDidMount
  2. Create `<g />` container in render
  3. Place D3 code in componentDidMount and/or componentDidUpdate
  + Never *ever* let D3 and React manage same parts of the DOM! OR BUGS!!

### Axes, Legends & Annotations

+ D3 renders: axes
  + Axes are very important in making the data readable, and D3 makes it easy

```js
// 1. create axisLeft or axisBottom at beginning of React lifecylcle and set corresponding scale
const yAxis = d3.axisLeft()
  .scale(yScale)
// 2. create an SVG group element in render
<g ref='group' />
// 3. call axis on the group element in componentDidUpdate
d3.select(this.refs.group).call(yAxis)
```

### Exercise: Axes

+ [starter code](https://codesandbox.io/s/kop49w9r2v?module=%2Fsrc%2Fvisualizations%2FChart.js)

+ [solution](https://codesandbox.io/s/3w4p0px1q)

### Transitions

+ [current slide](https://slides.com/shirleywu/deck-11#/50)

+ D3 renders: transitions

    ```js
    // in componentDidUpdate
    d3.select(this.refs.bars)
      .selectAll('rect')                // select elements to transition
      .data(this.state.bars)            // bind data
      .transition()                     // call transition
      .attr('y', d => d.y)              // set the attributes to transition
      .attr('height', d => d.height)
      .attr('fill', d => d.fill)

    // in render
    <g ref="bars">
      {this.state.bars.map((d, i) => {
        // make sure React doesn't manage the attributes D3 is transitioning!
        return <rect key={i} x={d.x} width="2" />
      })}
    </g>
    ```

+ It works, it's performant, but the code is ugly. Don't highly recommend it

### Brush

+ [current slide](https://slides.com/shirleywu/deck-11#/52)

+ D3 renders: brush

```js
// in componentDidMount
// create brush instance
this.brush = d3.brush()
  // define burshable area(extent)
  .extent([[0, 0], [width, height]])
  // pass in a function to execute on every brush, or brush end
  .on('end', () => {...})

d3.select(this.refs.brush).call(this.brush)

// in render
<g ref="brush" />
```

### Exercise: Brush

+ [starter code](https://codesandbox.io/s/k2z4w991l5?module=%2Fsrc%2Fvisualizations%2FChart.js)

+ [solution](https://codesandbox.io/s/ovym37wm4y)

### Additional Resources

+ [current slide](https://slides.com/shirleywu/deck-11#/55)

+ Readability: legends
  + [d3 SVG Legend](http://d3-legend.susielu.com/)

+ Readability: annotations
  + [react-annotation](http://react-annotation.susielu.com/)

+ React Framework
  + [REACT + D3 = VX](https://vx-demo.now.sh/)
  + [Semiotic (v 1.18.0+) is a data visualization framework for React.](https://emeeks.github.io/semiotic/#/)

### Canvas

+ [current slide](https://slides.com/shirleywu/deck-11#/57)

+ Large datasets: canvas

  ```js
  // in render
  <canvas ref="canvas"
    styel={{ width: `${width}px`, height: `${height}px` }}
    // retina screen friendly
    width={ 2 * width } height={ 2 * height }
  />

  // in componentDidMount
  ctx = this.refs.canvas.getContext('2d')
  ```

+ Performance because only one DOM element that we're "drawing" shapes on

+ Large datasets: canvas
  + Rect
    + ctx.fillRect(x, y, width, height)
      + or ctx.strokeRect(x, y, width, height)
  + Circle
    + ctx.beginPath()
    + ctx.arc(x, y, radius, startAngle, endAngle, anticlockwise)
    + ctx.fill()
      + or ctx.stroke()
  + Line
    + ctx.beginPath()
      + moveTo, lineTo, bezierCurveTo
    + ctx.fill()
      + or ctx.stroke()

### Creating Chart in Canvas

+ [starter code](https://codesandbox.io/s/1vxpo07zvq?module=%2Fsrc%2Fvisualizations%2FChart.js)

+ [solution](https://codesandbox.io/s/6oxovlmok)
