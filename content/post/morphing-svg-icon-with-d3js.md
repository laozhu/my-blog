---
title: "利用 d3.js 实现图标变形动画"
cover: "/media/posts/morphing-svg-icon-with-d3js/00.gif"
tags: ["D3.js", "SVG", "Icon"]
date: 2017-08-15
draft: false
---

SVG 格式在 WEB 开发中有着广泛的应用，在使用 SVG 创建图标后，人们通常会为之增加一些简单动画：鼠标悬浮改变颜色透明度；利用 `transform` 旋转角度缩放尺寸；鼠标点击切换图标等，但这些简单动画并不过瘾。本文以「播放」和「暂停」图标切换为例，讲述如何创建图标变形动画，让图标切换更酷炫更平滑。

<!--more-->

## 演示效果

{{% jsfiddle "laozhu/6bed532m" "html,js,result" %}}

[Morphing SVG Play Icon using D3.js](https://jsfiddle.net/laozhu/6bed532m/)

先展示下最终效果过把眼瘾，再继续写如何实现（看不到演示效果的请点击上面的链接查看）。

## 解题思路

实现变形动画有很多种方法，既有 SVG 动画方案，也有非 SVG 模拟方案。为了让大家了解博主的思考过程，下面对各种方法做一个简单的介绍，然后再重点介绍博主所使用的方法。

**1. 非 SVG 模拟方案**

非 SVG 的方案使用多个 HTML 空标签组合模拟图标，然后配合 `CSS Transition/Transform` 或者 `CSS Animation` 实现动画，有些 Hack 的味道，会产生一堆没有意义的空标签，让 HTML 文档结构不清晰，语义不明了，是最应该被抛弃的解决方案。针对非 SVG 模拟方案，可以通过下面链接的 Demo 了解实现原理：

[Material Design Morphing Icons](https://codepen.io/albebonv/pen/gIlrw)

**2. 使用 [SMIL](https://developer.mozilla.org/zh-CN/docs/Web/SVG/SVG_animation_with_SMIL) 的 SVG 动画**

只需在 SVG 元素内添加一个或多个 `<animate>` 元素，就能让元素动起来，这个方法实现动画简单优雅，代码量又小，本该是最优方法。但很不幸的是，这个标准已经被大厂们抛弃了，在 IE/Edge 上暂未得到支持，在 Chrome/Opera 上也已经是 deprecated 的状态。既然都被主流浏览器抛弃了，我们开发者也只能含泪舍弃。如果你还不太理解这个方法，可以通过下面链接了解：

[Shape Morph Button](https://codepen.io/chriscoyier/pen/DpFfE)

**3. 使用第三方类库实现**

![d3.js](/media/posts/morphing-svg-icon-with-d3js/01.png)

经过一番尝试，博主发现 [GSAP](https://greensock.com/gsap)，[Snap.svg](http://snapsvg.io/) 以及 [d3.js](https://d3js.org/) 这三个类库都可以很好满足我们的需求，权衡之后博主选择了 d3.js 这个库。d3 是 Data-Driven Documents 的缩写，d3.js 是一个可以帮你处理数据驱动文档的类库，其中文档不仅指 HTML 也包括了 SVG 文档。利用 d3.js 的模块化设计，我们并不需要引入整个 d3.js 类库，只需引入以下三个库即可：

- [d3-selection](https://github.com/d3/d3-selection) - 选择和操作 d3 元素
- [d3-transition](https://github.com/d3/d3-transition) - 增加过渡动画
- [d3-ease](https://github.com/d3/d3-ease) - 引入缓动函数

利用以上三个 d3 子类库，博主封装了一个 `MorphingIcon` 类，并模仿 [YouTube](https://www.youtube.com/) 网站的视频播放器图标，实现了「播放」和「暂停」图标变形动画。

## 文档结构

{{% figure src="/media/posts/morphing-svg-icon-with-d3js/02.gif" title="Design Morphing Shapes & Copy Path d Value" alt="Design Morphing Shapes & Copy Path d Value" %}}

图标的文档结构如下，外面包裹一层 `button` 作为图标容器，捕捉点击事件。在 SVG 的 `defs` 标签中定义图标所有状态下的 id，当前状态，下一个状态和路径值，每一种状态对应一个 PATH 元素（备注：在 `defs` 中定义的路径没有被 `use` 标签引用之前，不会在前端显示出来）。使用 `use` 标签引用初始 path 元素的 `id` 值。

PATH 元素是 SVG 文件中最通用的元素，任何其他的基本形状甚至文本都可转化为 PATH 元素。本篇文章中的变形效果依赖 PATH 元素 d 属性值的变化，因此使用之前需要将所有的其他元素均转化成 PATH 元素。对于基本形状，[morphSVG plugin](https://greensock.com/morphSVG) 这个类库提供了一个函数 `convertToPath` 可以将基本形状转变成 PATH 元素，而后再应用变形动画；对于文本元素，可以在 Sketch 软件中对文字应用 `Convert to Outlines` 命令可以轻松将文本转成 PATH 元素。

```html
<button class="icon-button icon-button-play" aria-label="Play">
  <svg class="morphing-icon morphing-icon-play" width="100%" height="100%" viewBox="0 0 36 36">
    <defs>
      <path id="icon-play" data-state="paused" data-next-state="playing" d="..." />
      <path id="icon-pause" data-state="playing" data-next-state="paused" d="..." />
    </defs>
    <use xlink:href="#icon-play" />
  </svg>
</button>
```

**为了保证下文中 JavaScript 代码的正常执行，请严格按照给定文档结构构建图标**

## MorphingIcon 类

使用 `ES6` 的 `Class` 语法实现 `MorphingIcon` 类，然后传入图标的选择器构造一个 `MorphingIcon` 对象，该对象不仅支持对某一特定事件的响应，也支持两种或两种以上状态循环切换。

```javascript
class MorphingIcon {
  // MorphingIcon 构造函数
  constructor(icon) {
    this.icon = document.querySelector(icon); // 图标元素
    this.states = this.initialStates(); // 可选状态表
    this.using = this.states.using; // 当前状态
    this.replaceWithUsingPath();
  }

  // 初始化可选状态表和当前状态
  initialStates() { return states }

  // 替换 use 标签为当前状态下的路径值
  replaceWithUsingPath() { ... }

  // 通过变形动画切换到下一个状态
  goToNextState(duration = 100, easingFunc = d3.easeLinear, delay = 0) { ... }

   // 通过变形动画切换到指定状态
  goToCertainState(state, duration = 100, easingFunc = d3.easeLinear, delay = 0) { ... }
}

// 创建一个变形动画图标对象
const playIcon = new MorphingIcon('.morphing-icon-play');
```

## 构造函数

传入图标的 `id` 或者 `class` 即可构造一个 `MorphingIcon` 对象，这个对象有三个属性：当前图标元素（icon），图标可选状态表（states）和图标当前状态（using）。可选状态表采用循环链表数据结构，包含该图标所有可能的状态及相互关系，同时使用一个 `using` 属性记录当前图标状态。下面的 `JSON` 就是本文 [Demo](https://jsfiddle.net/laozhu/6bed532m/) 中的图标状态表，从中可以看出，该图标有「播放」和「暂停」两个状态，并且互为彼此的下一状态，图标的当前状态是暂停。图标状态表是根据图标的文档结构自动生成，这也是我们必须严格按照给定文档结构构建图标的原因。

```json
{
  "paused": {
    "id": "#icon-play",
    "state": "paused",
    "nextState": "playing",
    "d": "M 12,26 18.5,22 18.5,14 12,10 Z M 18.5,22 25,18 25,18 18.5,14 Z"
  },
  "playing": {
    "id": "#icon-pause",
    "state": "playing",
    "nextState": "paused",
    "d": "M 12,26 16,26 16,10 12,10 Z M 21,26 25,26 25,10 21,10 Z"
  },
  "using": "paused"
}
```

构造函数除了生成 `MorphingIcon` 对象，初始化图标状态表外，还将 SVG 中的 `use` 引用替换成了实际的 `path` 元素，这个工作是由 `replaceWithUsingPath` 方法完成的。这样做的原因是：元素的切换无法应用 `transition` 过渡动画，只有同一元素属性值得变化才能应用 `transition` 过渡动画（这点与 CSS Transition 相同）。

## 下一状态

方法 `goToNextState` 将图标从当前状态切换到下一状态，并附加 d3.js 预设的过渡动画。这个方法主要的应用场景就是图标响应点击事件，在两个或多个状态间循环切换状态。从函数定义可以看出，我们可以像使用 CSS Transition 一样指定过渡动画的持续时间，缓动函数和延迟时间。

```javascript
goToNextState(duration = 100, easingFunc = d3.easeLinear, delay = 0) { ... }
```

## 指定状态

方法 `goToCertainState` 将图标从当前状态切换到指定状态，并附加 d3.js 预设的过渡动画。这个方法可以让我们更加自主的在图标各状态间切换，比如鼠标 `hover` 的时候切换到某一状态，鼠标 `click` 时切换到某一状态。函数参数也比 `goToNextState` 多了一个 `state`，用于指定目标状态。

```javascript
goToCertainState(state, duration = 100, easingFunc = d3.easeLinear, delay = 0) { ... }
```

如果你明白了博主上面写的是什么鬼，可以根据示例设计一个多状态的图标，并使用 `MorphingIcon` 这个类它添加变形动画。下面是博主在学习和实践过程中所参考的文章，供你参考。

## 参考资料

1. [D3.js - Data-Driven Documents](https://d3js.org/)
2. [GSAP, the standard for JavaScript HTML5 animation](https://greensock.com/gsap)
3. [Snap.svg, the JavaScript SVG library for the modern web](http://snapsvg.io/)
4. [A Guide to Alternatives to SMIL Features](https://css-tricks.com/smil-is-dead-long-live-smil-a-guide-to-alternatives-to-smil-features/)
5. [路径 - SVG教程 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial/Paths#Arcs)
