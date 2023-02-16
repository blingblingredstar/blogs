---
title: Responsive Web Typography v2
date: 2019-09-05 22:29:47
categories: [学习笔记]
tags: [FrontEndMasters, CSS, Variable Fonts, Typography]
---
## Introduction

[slides](https://slides.com/frontendmasters/responsivetypography#/)

## Responsive Type Overview

+ Typography 101(the concise edition)
+ What is Responsive Typography
+ Variable fonts: responsive type
+ Implementing web fonts well: performence, proportion & polish
+ Typography for reading(with a healthy dose of modern CSS)
+ Editional typography & art direction

+ Responsive Typography in a nutshell
  + Performence: select fonts with care, load what you need & don't block the page draw
  + Progressive: plan for failure, tune up the loading process & fallback fonts to minimize FOUT
  + Proportion: small screens demand subtle scale
  + Polish: Design is the details: OpenType & then some
<!-- more -->
[Website Style Guide Resources](http://styleguides.io/)

+ The future of responsive typography
  + Variable fonts: new technology, new possibilties
  + More Modern CSS
  + Even better performence
  + Going beyond the basics

### Typography 101

### Anatomy of Letterforms

[https://fontofthemonth.club/](https://fontofthemonth.club/)

### Type Styles, Selection & Paring

+ Serif
+ Sans-serif
+ Slab serif
+ Monospaced
+ Display
+ Script
+ Blackletter
+ Handwriting

[magpiepaperworks](https://www.magpiepaperworks.com/)

A most wonderful serif paired with a stupendous sans that has a similar sensibility, proportion and propriety

+ Print & Pixel Parity
  + Typefaces are becoming available: Typekit, Fonts.com, MyFonts, Type Network, others
  + Still lots of sameness, so different stands out
  + Most print techniques are possible with CSS, and many new techniques only work in digital

### Web Fonts Performence

+ Performence Matters
  + Great typography isn't "I used all of them"
  + Load only what you need
  + Each font has a performence cost, so budget wisely

### Progressive Enhancement

### Flash of Unstyled Text

+ FOUT is our fault
  + Use these: `.wf-inactive` / `.wf-active`
  + This CSS results in a blank screen during load:
    + `body { font-family: "Trade Gothic", helvetica, arial; }`
  + Add this & give them content, then fonts:
    + `.wf-inactive body ( font-family: helvetica, arial; )
  + Adjust `font-size`, `line-heigth`, `letter-spacing` to avoid jumpiness
  + Making it easy since 2010

### Proportion

+ Desktop geese & handheld gander
  + Small canvas requires subtle proportions
  + What works in print... works in print
  + Robert Bringhurst matters, but scale must adapt

[A More Modern Scale for Web Typography](http://typecast.com/blog/a-more-modern-scale-for-web-typography)

### Letter Spacing Polish

+ Polish: don't forget fit & finish
+ Polish: don't leave orphans behind

### Web Font Tips & Tricks

Miscellany

+ Use max-width on elements to keep text readable

```css
@media (min-width: 58em) {
  p { max-width: 38em; }
}
```

+ CSS3 brings character counts, but not universal(vw & vh, ch & cx)

+ **EMs** or **REMs**, but no *PX*

+ Don't forget: use real content! Beaucase Lorem Lpsum is a poser

### Web Fonts Demonstration

### Responsive Variable Fonts

### Variable Fonts Browser Support

### Variable Fonts Evolution

### Variable Fonts Resources

[Axis-Praxis](https://www.axis-praxis.org/specimens/__DEFAULT__)

[variable fonts](https://v-fonts.com/)

[Microsoft Edge Demos Current demo:Variable Fonts](https://developer.microsoft.com/en-us/microsoft-edge/testdrive/demos/variable-fonts/)

[TypeNetwork](https://www.typenetwork.com/brochure/opentype-font-variations/#introduction)

[Monotype](https://www.monotype.com/fonts/variable-fonts)

[variable web TYPOGRAPHY](https://zeichenschatz.net/demos/vf/variable-web-typo/)

## Implementing

### Loading Web Fonts

+ First rule of Fight Club(& web design)
  + Don't block page render
  + 53% of all users will leave in 3s
  + Design is making a choice

+ Progressively enhance
  + Support for @font-face is more likely than JS
  + Butter stance: tune for loading process
  + This helps address devices with no @font-face support too
  + Works without requiring JS, but gets better when it's there

### Common Font Issues

+ Proportion: one size won't rule them all
+ Proportion: a measure of improvement
  + Stuck a nerve in the web community
  + Showed a gap in RWD thinking & approach
  + Changed the whole focus of my book

+ Perserve Meaning, Not pixel size
  + Design is about communicating ideas & influencing behavior
  + Use scale as a starting point & tweak for specific typefaces & usage scenarios
  + Much like the grid: this is not religion, it's just a start

### Coding Web Fonts

### Font Loading Stages

### Changing Type Faces

### Typography for Reading

[CSS Variables: var(--subtitle) - CSSConf.Asia 2016](https://www.youtube.com/watch?v=kZOJCVvyF-4)

### Variable Font Demonstration

[current slide](https://slides.com/frontendmasters/responsivetypography#/243)

[code pen](https://codepen.io/jpamental/pen/MGEPEL)

[css shape outside](https://developer.mozilla.org/en-US/docs/Web/CSS/shape-outside)

### CSS Variables

### Variable Fonts Fallback

## Future of Web Typography

### Variable Font Features

### Pulling From History

+ Optical sizing

### OpenType Features

+ Ligatures & Swashes
+ Fractions
+ Kerning
+ Painless Fallback
+ File size penalty(for now)
+ Initial Caps
+ Better Blockquotes
+ Multi-column layouts
+ All fall back gracefully

### Text First Design

[current slide](https://slides.com/frontendmasters/responsivetypography#/265)

+ A little in abundance is a lot
  + great typography is the sum of the details

### Adding Font Features

### Editorial Design

### Editorial Design with Variable Fonts

### Font variation Demos

[variable font codepen demos](https://codepen.io/jpamental/)
