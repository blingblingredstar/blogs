---
title: Web Performance Fundamentals
date: 2021-11-18 21:20:05
categories: 学习笔记
tags: [FrontEndMasters, performance]
---
## Introduction

Part 1: Understanding
Part 2: Improving
Part 3: Planning

## Elements of Performance

### Why Care About Web Performance

Part 1: Understanding

- Psychology of performance
- Measuring performance
- Interpreting performance data
<!-- more -->
Why is performance important?

You can see lot of charts that try to explain how is performance improvement improve the business but **correction != causation**

Why is performance important #1

- Google says so
  - Google will rank you on your performance

Why is performance important #2

- Angry and frustrated users don't stick around long

### Perceived Performance

Analysis

- What behavior on the website slowed down your experience?
- How might the business model of each site contribute to their site's performance?

### Psychology of Waiting

Wait time feels subjective

1. People want to start
2. Board waits feel slower
3. Anxious waits feel slower
4. Unexplained waits feel slower
5. Uncertain waits feel slower
6. People will wait for value

### Web Vitals

Measuring Web Performance

- The Old Way: Page load
  - Gaming the metrics -- Lazy loading
- The New Way: Web vitals

  - Start -> FCP -> LCP -> CLS
  - FID(First Input Delay)

- First Contentful Paint(FCP)
  - The time until the user sees an indication that the page is loading
- Largest Contentful Paint(LCP)
  - The time until the user sees most of the page and believes it is (almost) ready
- Cumulative Layout Shift(CLS)
  - The movement distance and impact of page elements during the entire lifetime of the document the user sees

How might this impact Client-Side Rendering?

- First Input Delay(FID)

  - The browser time delay between the user's first click and execution of application code

- FCP -> Respond quick
- LCP -> Get to the point
- CLS -> Don't move stuff
- FID -> Don't load too much

- Loading
  - LCP
  - Good < 2.5s < Needs improve < 4.0s < Poor
- Interactivity
  - FID
  - Good < 100ms < Needs improve < 300ms < Poor
- Visual Stability
  - CLS
  - Good < 0.1 < Needs improve < 0.25 < Poor

### Lighthouse

Chrome lighthouse quirks and gotchas

- Relative to your machine, network
- Chrome window size
- Chrome application priority

## Metrics

### Where to Measure Performance

Where do we measure from?

- Server -> User(Usually we just use local dev environment to test not user environment)
- Synthetic Data(Use a test server to measure performance)
- Field Data(Sent user data to a monitoring server ps.RUM tools like requestmetrrics.com)

### Chrome User Experience

Signal to noise

- Signal: Lab data < Synth Data < Field Data
- Noise: Lab data < Synth Data < Field Data

[web.dev](https://web.dev/)

### Interpreting Field Data

If we got 95 lighthouse score in lab data, we may got serval field data score like 98/96/86...

- Understanding the sample
- Averages
- Median and percentiles
  - May be we should most care about the P95 user's experience

## Improving Performance

### Web Business Objectives

Part 2: Improving

- Business objectives
- Improving each web vital
- Code demos and exercises

Web Business Objectives(Where the money comes from)

- Awareness
- Retention
- Conversion
- Competition

For short time durations, the difference must be at least 20% for most people to care.

[lightest.app](https://www.lightest.app/)

### Performance API

[Performance API Doc](https://developer.mozilla.org/en-US/docs/Web/API/performance_property)

Performance API - Entry Timings

- `performance.getEntries()`
- `performance.getEntriesByType()`
- `performance.getEntriesByName()`

| Prompt for unload | redirect                  | App cache  | DNS                               | TCP                                             | Request      | Response                  | Processing                                             | Onload                      |
| ----------------- | ------------------------- | ---------- | --------------------------------- | ----------------------------------------------- | ------------ | ------------------------- | ------------------------------------------------------ | --------------------------- |
| navigationStart   | redirectStart/redirectEnd | fetchStart | domainLookupStart/domainLookupEnd | connectStart/(secureConnectionStart)/connectEnd | requestStart | responseStart/responseEnd | domLoading/domInteractive/domContentLoaded/domComplete | loadEventStart/loadEventEnd |

Performance API - Performance Observer

```js
new PerformanceObserver((entryList) => {
  const entries = entryList.getEntries();
}).observe(opts);
```

### Performance Analytics

```js
(function (ready) {
  if (
    document.readyState === 'complete' ||
    document.readyState === 'interactive'
  ) {
    ready();
  } else {
    document.addEventListener('readystatechange', function () {
      if (document.readyState === 'complete') {
        ready();
      }
    });
  }
})(function perf() {
  var data = {
    url: window.location.href,
    dcl: 0,
    load: 0,
    fcp: 0,
    lcp: 0,
    cls: 0,
    fid: 0,
  };

  var fcpObserver = new PerformanceObserver(function handleFCP(entryList) {
    var entries = entryList.getEntries() || [];

    entries.forEach(function (entry) {
      if (entry.entryType === 'first-contentful-paint') {
        data.fcp = entry.startTime;
        console.log('first-contentful-paint', data.fcp);
      }
    });
  }).observe({ type: 'paint', buffered: true });

  var lcpObserver = new PerformanceObserver(function handleLCP(entryList) {
    var entries = entryList.getEntries() || [];

    entries.forEach(function (entry) {
      if (entry.entryType === 'largest-contentful-paint') {
        if (entry.startTime > data.lcp) {
          data.lcp = entry.startTime;
          console.log('largest-contentful-paint', data.fcp);
        }
      }
    });
  }).observe({ type: 'largest-contentful-paint', buffered: true });

  var clsObserver = new PerformanceObserver(function handleCLS(entryList) {
    var entries = entryList.getEntries() || [];

    entries.forEach(function (entry) {
      if (!entry.hadRecentInput) {
        data.cls += entry.value;
        console.log('Increased CLS Performance', data.cls);
      }
    });
  }).observe({ type: 'layout-shift', buffered: true });

  var fidObserver = new PerformanceObserver(function handleFID(entryList) {
    var entries = entryList.getEntries() || [];

    entries.forEach(function (entry) {
      data.fid = entry.processingStart - entry.startTime;
      console.log('Recorded FID Performance', data.fid);
    });
  }).observe({ type: 'first-input', buffered: true });

  window.addEventListener('beforeunload', function () {
    var navEntry = this.performance.getEntriesByType('navigation')[0];
    data.dcl = navEntry.domContentLoadedEventStart;
    data.load = navEntry.loadEventStart;

    var payload = JSON.stringify(data);
    navigator.sendBeacon('/api/perf', payload);
    console.log('Sending performance', payload);
  });
});
```

Performance Analytics Conclusions

- CLS is inversely correlated to Session Time
- LCP is inversely correlated to Bounce Rate

Note: this is only for some site, not a universal rule

## Optimizing Metrics

How to improve performance?

- **Do fewer things!**

### Improving FCP

First contentful paint

- The time until the user sees an indication that the page is loading.
- Response quick

Requirement

- Quick server
- Small files
- short transport

Server side:

- Sized correctly
- Minimal processing
- Network bandwidth

Small documents:

- Content size
- Compression

Short transmission

- Use CDNs(USA East coast to West coast is 200-300ms)

### Improving LCP

Largest contentful paint

- The time until the user sees most of the page and believe it is(almost) ready
- Get to the point

Methods:

1. Defer resources until later
2. Optimize images
3. Reduce request overhead

```html
<!-- use async attribute will still block the render -->
<script async scr="main.js"></script>
<!-- use defer will just fetch the file and not execute until the initial render is done -->
<script defer scr="main.js"></script>
<!-- set loading attribute to lazy to fetch the image after the initial render -->
<img src="image.jpg" loading="lazy" />
```

- Safari and ie doesn't support loading attribute!

```js
/**
 * lazyLoader
 * Check for elements in the document to be loaded later when visible to the user.
 * @see https://developers.google.com/web/fundamentals/performance/lazy-loading-guidance/images-and-video/
 * @example
 *   <element src="" data-src="/url/" data-srcset="..." />
 */
(function (ready) {
  if (
    document.readyState === 'complete' ||
    document.readyState === 'interactive'
  ) {
    ready();
  } else {
    document.addEventListener('DOMContentLoaded', ready);
  }
})(function lazyLoader() {
  var lazyEls = [].slice.call(document.querySelectorAll('[data-src]'));

  /**
   * @param {HTMLElement} el
   */
  function load(el) {
    var src = el.getAttribute('data-src');
    var srcset = el.getAttribute('data-srcset');
    if (src) {
      el.setAttribute('src', src);
    }

    if (srcset) {
      el.setAttribute('srcset', srcset);
    }

    el.removeAttribute('data-src');
    el.removeAttribute('data-srcset');
  }

  if ('IntersectionObserver' in window) {
    var lazyObserver = new IntersectionObserver(function (entries, observer) {
      entries.forEach(function (entry) {
        if (entry.isIntersecting) {
          var el = entry.target;
          load(el);
          lazyObserver.unobserve(el);
        }
      });
    });

    lazyEls.forEach(function (el) {
      if (el.tagName === 'SCRIPT') {
        load(el);
      } else {
        lazyObserver.observe(el);
      }
    });
  } else {
    lazyEls.forEach(load);
  }
});
```

### Responsive Images

```html
<img
  src="pic-1200.jpg"
  srcset="pic-600.jpg 600w, pic-900.jpg 900w, pic-1200.jpg 1200w"
  sizes="(max-width: 600px) 600px,
         (max-width: 900px) 900px,
         1200px"
/>
```

Also, we can use tinyPNG or other tools to crunch images

```js
const path = require('path');
const fs = require('fs').promises;

const imagemin = require('imagemin');
const imageminJpegtran = require('imagemin-jpegtran');
const imageminPngquant = require('imagemin-pngquant');
const imageminSvgo = require('imagemin-svgo');
const imageminGifsicle = require('imagemin-gifsicle');

(async () => {
  const files = await imagemin(
    ['public/assets/images/**/!(*.min).{jpg,png,svg,gif}'],
    {
      plugins: [
        imageminJpegtran(),
        imageminPngquant({
          strip: true,
          quality: [0.2, 0.6],
        }),
        imageminSvgo({
          plugins: [{ removeViewBox: false }],
        }),
        imageminGifsicle(),
      ],
    }
  ).then((files) => {
    files.forEach(async (file) => {
      const source = path.parse(file.sourcePath);
      file.destinationPath = `${source.dir}/${source.name}.min${source.ext}`;
      await fs.writeFile(file.destinationPath, file.data);
    });
  });
})();
```

### HTTP2, Catching, and Pre-Loading

Reduce overhead with HTTP/2

| Pros              | Cons                         |
| ----------------- | ---------------------------- |
| Faster            | Server setup + Compatibility |
| Reuse connections | SSL Required                 |

Reduce overhead with caching

```header
cache-control: max-age=600
expires: Wed, 20 Jan 2021 03:13:21 GMT
etag: "6005a4af-c96f"
```

Reduce overhead with preloading

```html
<link rel="preconnect" href="https://fonts.gstatic.com" />
<link rel="preload" href="./icons.css" />
```

### Improving CLS

Cumulative layout shift

- The movement distance and impact of page elements during the entire lifetime of the document the user sees.
- Don't move stuff

Before vs. after LCP

- Layout hints

Demonstration: Banner positioning

### Improving FID

First Input Delay

- The browser time delay between the user's first click and execution of application code
- Don't load too much

- We got this issue because we moving deckchairs
- But will users wait for that?
  - How anxious are they?
  - How valuable is it to them?
  - Do they understand the wait?

### Data Beyond Spreadsheets

After above optimizations, did we improve performance?

Business projections

- Improving session time
- Improving bounce rates

Data beyond spreadsheets

- Flame distribution
- Web vitals
- Time breakdown

## Planing

- Performance culture
- Working with non-tech folks
- Designing new applications

### Performance Panel

[Course](https://frontendmasters.com/courses/chrome-dev-tools-v2/)

### Performance Culture

Performance is a team value

- It's not a task
- Performance is like security and quality has to do cross the entire circle
  - Test performance early
  - Test performance often
  - Monitor performance always
- You need data
- Fast is part of planning

### Goals & Performance Budget

Setting your goals

- Who are your users?
- What device(s) do they have?
- How long will they wait?
- How fast would impress?

[performancebudtget.io](https://www.performancebudget.io/)

### Performance Scenarios

Scenario: Marketing wants to add another third-party pixel service

- You need data
  - Not a performance chart
  - Maybe you need a bounce and session time chart
  - You definitely need a revenue and checkout chart
- Without data, you might be wrong

Scenario: Designing a new web app

- Business objective
- Import metrics