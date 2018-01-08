---
title: "HTML 文档之 Head 最佳实践"
author: "米老朱"
cover: "/media/posts/html-head-best-practices/01.jpg"
tags: ["HTML", "head"]
date: 2018-01-07
draft: false
---

每一个标准的 HTML 文档都包含一个 `<head>` 头部分，除了声明文档类型，编码方式和文档标题，引入外部资源这些基本功能外，<head> 头还能做很多非常有用的事情，这篇文章整理了作者认可的一些最佳实践，写在这里与各位分享。

<!--more-->

## 语言

在 `html` 标签中通过 `lang` 属性进行明确的语言声明，将会有助于翻译，英文、简体中文和繁体中文网页所属性值如下：

```html
<html lang="en">
<html lang="zh-Hans">
<html lang="zh-Hant">
```

## 编码

请在 `<head>` 中第一行统一使用 `utf-8` 编码声明。

```html
<meta charset="utf-8">
```

## Base 元素

尽量不使用 `<base>` 元素，绝对地址和 URL 对于开发者和用户来说都更好。

```html
<!-- Bad -->
<base href="/blog/">
<link href="hello-world" rel="canonical">

<!-- Good -->
<link href="/blog/hello-world" rel="canonical">
```

## Viewport

设置 `viewport` 的宽度为设备宽度，默认缩放比为 1（允许用户缩放），设置 `viewport-fit=cover` 以兼容 iPhone X 全面屏“刘海”的显示。

```html
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
```

## DNS 预读取

DNS 预读取是一项使浏览器主动去执行域名解析的功能，其范围包括当前文档内的所有链接，这能够减少用户点击链接时的延迟。

```html
<meta http-equiv="x-dns-prefetch-control" content="on">
<link rel="dns-prefetch" href="//www.google-analytics.com">
<link rel="dns-prefetch" href="//fonts.googleapis.com">
```

## 预加载

指示浏览器预先请求当前页面所需要的关键性资源。

```html
<link rel="preload" href="/css/main.css" as="style">
<link rel="preload" href="/js/main.js" as="script">
```

## 预请求

指示浏览器预先请求用户即将浏览页面所需要的关键性资源。

```html
<link rel="prefetch" href="/img/css-sprite.png">
<link rel="prefetch" href="/fonts/icons.woff2">
```

## 渲染偏好

对于国内各种双核浏览器，通过设置 `renderer` 头要求它们使用 webkit 内核；对于 IE 浏览器，通过设置 `IE=edge` 要求它使用最新的版本；对于百度搜索强制转码的流氓做法，通过 `no-transform` 禁止其自动转码；指示浏览器对类似电话、邮箱或地址的内容不要自作聪明的瞎识别（移动端）。

```html
<meta name="renderer" content="webkit"> <!-- 用在360中 -->
<meta name="force-rendering" content="webkit"> <!-- 用在其它 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge"> <!-- 针对 IE 浏览器 -->
<meta http-equiv="Cache-Control" content="no-transform"> <!-- 针对百度搜索 -->
<meta name="format-detection" content="telephone=no,email=no,adress=no">
```

## 文档信息

HTML 文档的一些元数据，包括：作者、描述、关键词和生成工具；设置 `robots` 为 `index,follow` 指示搜索引擎爬虫该页面需要进入索引，并且页面内的所有链接都要继续跟踪；设置 `referrer` 为 `origin-when-cross-origin` 指示浏览器在进行跨域跳转时，其 `referrer` 值将统一为域名，而非具体的 URL 地址。

```html
<meta name="author" content="米老朱">
<meta name="description" content="米老朱的个人博客">
<meta name="keywords" content="米老朱,极客,博客,WEB,开发,产品设计">
<meta name="generator" content="Hugo 0.32">
<meta name="robots" content="index,follow">
<meta name="referrer" content="origin-when-cross-origin">
```

## Icons

虽说所有浏览器都还支持过时的 favicon.ico 格式，但在 HTML5 时代，我们应该使用更好的 PNG icon with sizes 方案。同时为了兼容老旧浏览器，我们可以将生成好的 favicon.ico 文件放在网站的根目录下面，通常浏览器会自动请求并加载它，并不需要额外通过 link 标签引入。

```html
<meta name="theme-color" content="#db5945"> <!-- 主题颜色 -->
<meta name="application-name" content="米老朱的博客"> <!-- 应用名称 -->
<meta name="apple-mobile-web-app-capable" content="yes"> <!-- 隐藏状态栏 -->
<meta name="apple-mobile-web-app-status-bar-style" content="black"> <!-- 状态栏颜色 -->
<meta name="apple-mobile-web-app-title" content="米老朱的博客"> <!-- iOS 下的应用名称 -->
<meta name="msapplication-tooltip" content="米老朱的博客"> <!-- Hover 的提示信息 -->
<meta name="msapplication-TileColor" content="#db5945"> <!-- 磁贴背景颜色 -->
<meta name="msapplication-TileImage" content="/img/logo-144.png"> <!-- 磁贴图标 -->
<meta name="msapplication-config" content="/browserconfig.xml"> <!-- 磁贴配置文件 -->
<link rel="icon" type="image/png" href="/img/logo-16.png" sizes="16x16"> <!-- Browser Favicon -->
<link rel="icon" type="image/png" href="/img/logo-32.png" sizes="32x32"> <!-- Taskbar Shortcut -->
<link rel="icon" type="image/png" href="/img/logo-96.png" sizes="96x96"> <!-- Desktop Shortcut -->
<link rel="icon" type="image/png" href="/img/logo-128.png" sizes="128x128"> <!-- Chrome Web Store -->
<link rel="icon" type="image/png" href="/img/logo-196.png" sizes="196x196"> <!-- Chrome for Android Home Screen -->
<link rel="icon" type="image/png" href="/img/logo-228.png" sizes="228x228"> <!-- Opera Coast Icon -->
<link rel="apple-touch-icon" href="/img/logo-120.png"> <!-- iPhone -->
<link rel="apple-touch-icon" href="/img/logo-152.png" sizes="152x152"> <!-- iPad -->
<link rel="apple-touch-icon" href="/img/logo-180.png" sizes="180x180"> <!-- iPhone Plus -->
<link rel="apple-touch-icon" href="/img/logo-167.png" sizes="167x167"> <!-- iPad Pro -->
<link rel="mask-icon" href="/img/logo.svg" color="green"> <!-- Safari Pinned Tab Icon -->
```

微软为了让 Metro UI 更好看，引入了 `browserconfig.xml` 文件，主要用于定制网站固定磁铁的图标和背景颜色，其格式如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<browserconfig>
   <msapplication>
     <tile>
        <square70x70logo src="/img/logo-70.png"/>
        <square150x150logo src="/img/logo-150.png"/>
        <wide310x150logo src="/img/logo-310x150.png"/>
        <square310x310logo src="/img/logo-310.png"/>
        <TileImage src="/img/logo-144.png"/>
        <TileColor>#db5945</TileColor>
     </tile>
   </msapplication>
</browserconfig>
```

favicon.ico 实际上是一个图片容器，里面至少应该包含 16x16，32x32，48x48 三个尺寸的 png 图片，我们可以使用 [ImageMagick](https://github.com/ImageMagick/ImageMagick) 工具在本地直接生成（生成之前需要对 png 图片进行压缩以减小尺寸）。

```bash
$ convert favicon-16.png favicon-32.png favicon-48.png favicon.ico
```

*注意：apple-mobile-web-app-status-bar-style 值默认状态栏为白色，可设置为 black（黑色） 或者 black-translucent（灰色半透明）；mask-icon 引入的 svg 文件必须只有一个图层，并且 `viewBox` 属性应该为 "0 0 16 16"。*

## Twitter Cards

Twitter 卡片用于将网站内容以更加优雅漂亮的方式分享到 [twitter.com](https://twitter.com/) 网站，从形式上说，分为：summary, summary_large_image, app, player 四种形式，通常我们的站点只需要 summary 这种形式。

```html
<meta name="twitter:card" content="summary">
<meta name="twitter:site" content="@melaozhu">
<meta name="twitter:title" content="此处为分享标题">
<meta name="twitter:description" content="此处为分享描述">
<meta name="twitter:image" content="此处为分享配图">
```

配置上线后，你可以通过这个 [Card validator](https://cards-dev.twitter.com/validator) 工具检查是否正确显示。

## Facebook Open Graph

[Open Graph](http://ogp.me/) 是一套开放的网页标注协议，通过 meta 标签标注网页的类型，主要由 Facebook 推动，已经成为社交分享领域的事实标准。如果你希望明确告诉社交网络或搜索引擎你的网页类型，你应该添加这些 meta 标签。

```html
<meta property="og:type" content="article">
<meta property="og:title" content="此处为分享标题">
<meta property="og:description" content="此处为分享描述">
<meta property="og:image" content="此处为分享配图">
<meta property="og:url" content="此处为分享的链接地址">
```

## 固定链接

对于一份文档存在多个 URL 的情况，通过 `rel="canonical"` 指定唯一的固定链接。

```html
<link rel="canonical" href="https://laozhu.me/">
```

## RSS 链接

对于支持 RSS 订阅的页面，可针对 RSS 阅读器提供可订阅的源文件。

```html
<link rel="alternative" href="/index.xml" title="米老朱的博客" type="application/atom+xml">
```

## Polyfill

可以专门为老旧的 IE 浏览器引入 Polyfill 方案，举个例子，为了让 IE6-IE8 浏览器能够使用 HTML5 标签和 Media Query 特性，我们需要引入 [html5shiv](https://github.com/aFarkas/html5shiv) 和 [response.js](https://github.com/scottjehl/Respond) 这两个库。

```html
<!--[if lt IE 9]>
<script src="//cdn.bootcss.com/html5shiv/3.7.3/html5shiv.min.js"></script>
<script src="//cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
<![endif]-->
```

其他的 Polyfill 方案写法类似。

## 打印样式表

对于可打印的页面（如文章页面），可提供针对打印机的样式表，使得网站用户可以将文章打印下来阅读。

```html
<link rel="stylesheet" href="/css/print.css" media="print">
```

## 交替样式表

定义交替样式表的时候，指定其 `title` 属性，以方便用户在浏览器中根据名称选择替代样式，交替样式表多用于多主题切换的站点。

```html
<link href="default.css" rel="stylesheet">
<link href="high-contrast.css" rel="alternate stylesheet" title="High contrast">
```

## 参考资料

1. [HTML Best Practices](https://github.com/hail2u/html-best-practices)
2. [Favicon Cheatsheet](https://github.com/audreyr/favicon-cheat-sheet)
3. [DNS 预读取](https://developer.mozilla.org/zh-CN/docs/Controlling_DNS_prefetching)
4. [Prefetching, preloading, prebrowsing](https://css-tricks.com/prefetching-preloading-prebrowsing/)
5. [Favicons, Touch Icons, Tile Icons, etc. Which Do You Need?](https://css-tricks.com/favicon-quiz/)
6. [Here's Everything You Need to Know About Favicons in 2017](https://sympli.io/blog/2017/02/15/heres-everything-you-need-to-know-about-favicons-in-2017/)
7. [Configuring Web Applications](https://developer.apple.com/library/content/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html)
8. [Creating Pinned Tab Icons](https://developer.apple.com/library/content/documentation/AppleApplications/Reference/SafariWebContent/pinnedTabs/pinnedTabs.html)
9. [“The Notch” and CSS](https://css-tricks.com/the-notch-and-css/)
10. [Alternative style sheets](https://developer.mozilla.org/en-US/docs/Web/CSS/Alternative_style_sheets)
