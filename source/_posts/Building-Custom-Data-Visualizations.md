---
title: Building Custom Data Visualizations
date: 2019-10-15 14:39:15
categories: 学习笔记
tags: [FrontEndMasters, Data Visualization, D3.js]
---
## Introductions

+ [slides](https://slides.com/shirleywu/deck-10#/)

### Exploratory and Expository

+ Custom data visualizations can be categorized into two broad categories:
  + Expository vs. exploarotry

+ Expository
  + static dataset
  + explore data for story
  + communicate story to audience
+ Examples:
  + New York Times
  + [The Pudding](https://pudding.cool/)
  + The Washington Post
  + etc...
<!-- more -->
+ Exploratory
  + dynamic dataset
  + interview stakeholders
  + build tool for stakeholders to explore the data
+ Examples
  + scientific visualizations
  + internel bussiness tools at Netflix, Uber, Airbnb, etc

+ Expository
  + Data
    + top 10 blockbusters every year for the last two decades
  + Goal
    + come up with a design and implement it together(Yay participation!)
  + [The raw data](https://gist.githubusercontent.com/sxywu/b94eb86c807b05080d7ee470bd1e815c/raw/bc0e59845dae332100acaa73f510580ccbe317bd/110_movies.json)

### Course Agenda

+ Data exploration with [Ovservables](https://observablehq.com/) and [Vega-Lite](https://vega.github.io/vega-lite/)
+ Design with [Marks, Channels](https://jenniewblog.wordpress.com/2016/03/08/marks-and-channels-chapter5/), and [Gestalt Laws](https://en.wikipedia.org/wiki/Principles_of_grouping)
+ Code with [SVG paths](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#Path_commands) and D3 [shapes](https://github.com/d3/d3-shape), layouts
+ Finish with [annotations](https://d3-annotation.susielu.com/), [axes](https://github.com/d3/d3-axis), [legends](https://d3-legend.susielu.com/)

## Data

### Exploring the Dataset

+ Data exploration
  1. list data attributes
  2. Ask questions
  3. Explore the data

+ Will use an [Observable notebook](https://beta.observablehq.com/d/907b07f81f284b17) for this

+ Data exploration
  + How to use observables

| To load external libraries: | d3 = require('d3')                                    |
| --------------------------- | ----------------------------------------------------- |
| To write text:              | md \`\`                                               |
| To set global variable:     | globalVar = ...                                       |
| To write a block of js code | {...}                                                 |
| To create an SVG element    | { const svg = DOM.svg(width, height) ... return svg } |

+ Data exploration
  + Data types
    + Categorical(movie genres)
    + Ordinal(t-shirt sizes)
    + Quantitatives(ratings/scores)
    + Temporal(dates)
    + Spatial(cities)

### Chart Types & Vega-Lite

+ Some basic chart types:
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
    + 2 categories, and the relationship between their quantitative values
  + Line chart
    + For temporal trends
    + Domain: temporal
    + Range: quantitative

+ [Vega-Lite](https://vega.github.io/vega-lite/)
  + A grammar of interactive graphics
    + Data source
    + Mark(tick, bar, point, line, etc)
    + Encoding(x, y, color, etc)
    + For each encoding: type, field
  + [Example Gallery](https://vega.github.io/vega-lite/examples/)

+ [solution 1](https://github.com/FrontendMasters/fm-snippets/blob/master/2018-08-03-building-custom-data-visualizations/fem-BuildingCustomDataVisualizations-example01.js)

+ [solution 2](https://github.com/FrontendMasters/fm-snippets/blob/master/2018-08-03-building-custom-data-visualizations/fem-BuildingCustomDataVisualizations-example02.js)

### Using Modules in Observable

+ [How to: require stubborn modules](https://beta.observablehq.com/@tmcw/requiring-modules-troubleshooting)

+ [Module require debugger](https://observablehq.com/@tmcw/module-require-debugger)

### Using Heatmap

+ [Annual Weather Heatmap](https://vega.github.io/vega-lite/examples/rect_heatmap_weather.html)

+ [solution](https://observablehq.com/@sxywu/building-custom-data-visualizations-fem)

### Exploration Exercise

[full notebook](https://observablehq.com/@sxywu/building-custom-data-visualizations-data-explorations)

+ Data exploration advice
  + Check for missing data, and the validity of the data
  + Focus on one question at a time(it's very easy to get sidetracked with a tangent)
  + If there IS an interesting tangent, make a note for later
  + If the question leads on to a dead-end, explore another question or the tangent you found earlier
  + Don't be afraid to go out and look for additional data to aid your exploration
  + Sometimes, no interesting pattern IS very interesting

## Design

### Marks & Channels

+ [The ProPublica GitHub](https://github.com/propublica)

+ Translate From Data to Design
  1. Concentrate on the takeways to communicate across
  2. What does that mean in terms of the data?(Individual or aggregate elements? Which attributes?)
  3. Map the relevant data to visual elements

+ Design: Marks & Channels
  + Map individual or aggregate data element to marks
  + Map data attributes to channels

+ Desigh: Marks
  + Points
  + Lines
  + Areas

+ Desigh: Channels
  + Position
    + Horizontal
    + Vertical
    + Both
  + Color
  + Shape
  + Tilt
  + Size
    + Length
    + Area
    + Volume

+ Quantitative
  + Position
  + Size
  + Color
+ Categorical
  + Shape
  + Texture
    + [Textures.js](https://riccardoscalco.it/textures/)
  + Color
+ Temporal
  + Animation

+ [slide](https://slides.com/shirleywu/deck-10#/24)

+ Design: Marks & Channels
  + One-to-one mapping of data to channel
  + Multiple mappings of channel to mark(x, y, size, color usually)
  + Do not EVER map multiple data attributes to the same channel

### Gestalt Laws of Grouping

+ Deisgn: Gestalt Laws of Grouping
  + The human mind naturally groups individual elements into patterns
  + Can use in data visualization to save processing time
    + > [The Functional Art](http://www.thefunctionalart.com/p/about-book.html)

+ Deisgn: Gestalt Laws of Grouping
  + Proximity
    + Put related objects near each other
  + Similarity
    + Indicate like objects(helpful if they can't be placed close to each other)
  + Enclosure
    + Helpful when creating visualizations with multiple sections

### Remix & Overlay

+ [Current Slide](https://slides.com/shirleywu/deck-10#/30)

+ Design: Remix & Overlay
  > "You don't always need to start from scratch, remix what's out there already" - Nadieh Bremer
  + But make sure they're the right visuals to communicate your message

+ [Info We Trust - Data stories by RJ Andrews.](https://infowetrust.com/)

+ [The Rhythm of Food](http://rhythm-of-food.net/)

## Code

### Turning Designs into Code

+ Break it down! What do you need to draw the marks? What do you need to calculate the channels?
+ To draw marks: SVG(or canvas)
+ To calculate channels: D3 scales, shapes and layouts(or straight-up math!)

+ SVG elements
  + rect
  + circle
  + text
  + path

### D3 Shapes and Layouts

+ [current slide](https://slides.com/shirleywu/deck-10#/35)

+ D3核心API可分为以下几类
  + Data preparation
  + Layout calculation
  + DOM manipulation
  + Finishing touches
  + Interactions

+ 其中前两个可以将数据转换为需要绘制的SVG图形，并将结果使用第三个类别中的api绘制在页面上

+ Sometimes all you need are scales to get from data to screen space
  + Scales
    + Continuous
    + Quantize
    + Quantile
    + Threshold
    + Oridinal

+ Often times, you may need specific layouts
  + These output x/y positions

+ [Who's speaking in Middle Earth](http://www.datasketch.es/july/code/nadieh/)

### Code Exercise

+ [Starter code](https://codesandbox.io/s/3r5rz58xjp?module=%2Fsrc%2Fstarter.js)

## The Finishing Touches

### Readability

+ [current slide](https://slides.com/shirleywu/deck-10#/45)

+ Readability
  + Titles, descriptions and legends to explain the visualization
  + Axes and annotations to describe the data

+ [d3-legend by Susie Lu](https://d3-legend.susielu.com/)

+ [d3-annotation by Susie Lu](https://d3-annotation.susielu.com/)

### Exercise: Readability

1. Add axes
2. Add legends
3. Add annotations

### Adding Aesthetics

+ More SVG for context & aesthetics
  + Pattern
    + [Textures.js](https://riccardoscalco.it/textures/)
  + Gradients
    + [Visual Cinnamon](https://www.visualcinnamon.com/)
  + Text on a path
    + [Visual Cinnamon](https://www.visualcinnamon.com/)
  + SVG filters(blurs, drop-shadows)
    + [Visual Cinnamon](https://www.visualcinnamon.com/)
  + Clipping & masking
    + [Visual Cinnamon](https://www.visualcinnamon.com/)
    + [Baby Spike](https://www.visualcinnamon.com/portfolio/baby-spike)

### Exercise: Adding Aesthetics

1. Add textures for holiday
2. Add dropshadow to movies

### Interactions

+ Interactions
  + D3:
    + hover, click and other simple interactions
  + D3 & React(or similar)
    + update after user manipulation of underlying data
    + link multiple visualizations
    + exploratory tools(filtering, aggregating)

## Wrapping Up

### Exploratory Visualizations

+ Process(at Netflix with Susie & Elijah)
  1. Initial meeting with stakeholders to figure out most important questions
  2. Meeting with data engineers
  3. Mock-up in sketch, sandbox with [Semiotics](https://emeeks.github.io/semiotic/#/), see shape of data
  4. Prototype, iterate with stakeholders

+ Advice(from Netflix)
  + Different data sources, often SQL queries -> plan for queries that take longer(important for interactions)
  + Questions for stakeholders:
    + What's the business question they're trying to answer?
    + How do the metrics they're comparing fall into a decesion?

+ Advice, cont.
  + Level of aggregation that's most effective for decision making:
    + Get it to ~7 things that are granular and meaningful enough
    + If not, top 10 of a default
  + Gain trust and credibility within org
    + Have to compete with tables of data(detailed but hard to read)
    + People will get to a state you never designed for, so think through edge cases

### Additional Resources

+ Books:
  + *The Functional Art* by Alberto Cairo
  + *Visual Analysis and Design* by Tamare Munzner
+ Online:
  + [Datawrapper Blog](https://blog.datawrapper.de/)
  + [Flowing Data](https://flowingdata.com/category/visualization/)
  + [The little of visualization design](http://www.visualisingdata.com/2016/03/little-visualisation-design/)
  + [The Pudding](http://pudding.cool/)
  + [Information is Beautiful Awards](https://www.informationisbeautifulawards.com/showcase?award=2017&pcategory=winner&type=awards)
