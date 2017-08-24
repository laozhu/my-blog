---
title: "本站 Hugo 主题 Nuo 正式上线！"
author: "米老朱"
tags: ["Hugo", "Theme"]
date: 2017-08-04
---

经过博主近两周的折腾，本站主题 [hugo-nuo](https://themes.gohugo.io/theme/hugo-nuo/) 终于在 Hugo 官方主题仓库上线咯，欢迎下载体验和反馈意见。这个主题适合于个人博客用户，包含：首页、文章页、作品（开发中）、友链和关于五个主要页面，支持 [搜狐畅言](http://changyan.kuaizhan.com/) 和 [Disqus](https://disqus.com/) 评论系统，集成了大量 [Hugo Shortcodes](https://gohugo.io/content-management/shortcodes/) 以方便引入第三方资源。主题构建依赖 `node` / `rollup` / `sass` 开发环境，你可以参照官方 [README](https://github.com/laozhu/hugo-nuo#build) 搭建环境进行二次开发。

<!--more-->

## 截图

无图无真相，你可以直接访问 [laozhu.me](https://laozhu.me) 查看主题模样，博主也准备了桌面和手机的高清无码截图，可以先感觉下。

{{% figure src="/media/posts/hugo-nuo-theme/01.png" title="Hugo Nuo 主题桌面形态" alt="Hugo Nuo 主题桌面形态" %}}

{{% figure src="/media/posts/hugo-nuo-theme/02.gif" title="Hugo Nuo 主题移动形态" alt="Hugo Nuo 主题移动形态" %}}

整个主题是响应式的，可以根据浏览器的宽度和高度自动改变布局。

## 特性

- [x] 全站采用响应式页面布局
- [x] 一个可自行定制的主菜单
- [x] 文章列表页支持有封面和无封面两种布局
- [x] 提供一个漂亮的友情链接页面布局
- [x] 支持 21 个社交网站账号和图标
- [x] 支持百度统计和 Google 分析工具快速集成
- [x] 支持主流搜索引擎站长工具的网站快速验证
- [x] 支持搜狐畅言和 Disqus 评论系统（二选一）
- [x] 大量第三方 shortcodes 支持（如：网易云音乐）

详细的特性可以查看主题的 GitHub 代码仓库 → [laozhu/hugo-nuo](https://github.com/laozhu/hugo-nuo) 相关说明，如果仍有不清楚的地方，可以在本站回复博主，或者去代码仓库给我提个 issue。

## 安装

如果你还没有安装 Hugo，请先按照 [官方教程](https://gohugo.io/getting-started/) 安装，然后用命令行创建一个新的博客站点（以 macOS 为例）。

```bash
$ brew install hugo
$ hugo version # 检查是否安装成功
$ cd ~/Sites
$ hugo new site my-blog
$ cd my-blog
```

如果你不需要修改主题的样式和脚本，只需要替换某些 logo 图片，在 Hugo 博客目录下执行:

```bash
$ cd themes
$ git clone https://github.com/laozhu/hugo-nuo
```

然后根据需要替换主题目录下的 `static/img` 里面的图片即可（请保持图片的尺寸一致）。

## 配置

首先参照主题 [exmapleSite](https://github.com/laozhu/hugo-nuo/tree/master/exampleSite) 里面的 [config.toml](https://github.com/laozhu/hugo-nuo/blob/master/exampleSite/config.toml) 修改你自己博客的配置文件，具体可参照此 [README](https://github.com/laozhu/hugo-nuo#the-config-file) 文件。

*备注：你可以将 exampleSite 文件夹添加到 `.gitignore` 中，避免被 push 到自己博客的代码仓库中。*

然后通过命令行分别添加 [links.md](https://raw.githubusercontent.com/laozhu/hugo-nuo/master/exampleSite/content/links.md) 和 [about.md](https://raw.githubusercontent.com/laozhu/hugo-nuo/master/exampleSite/content/about.md) 页面，格式参照相应链接中的内容即可。

```bash
$ hugo new links.md
$ hugo new about.md
```

页面文件头中的 `title` 指该页面标题，`layout` 用于指定页面布局，当前可选 `about` 和 `links` 两种，`menu` 和 `weight` 指定该页面是否添加到主菜单，并通过权重调整菜单排序。

```yaml
title: "About"
date: "2014-04-09"
layout: "about"
menu: "main"
weight: 40
```

这两个页面搞定后，剩下的就是构思和写作文章了。

## 定制主题

主题的样式使用 `sass` 组织，脚本使用 `rollup` 打包，图片和字体不依赖，使用 `cpx` 直接复制即可，使用 `ImageOptim` 压缩各种格式的图片，执行以下命令安装非 `node` 环境下的依赖包：

```bash
$ brew cask install imageoptim 
$ sudo gem install sass
$ sudo pip install pygments
```

进入到主题目录下，通过 `npm install` 安装主题开发所依赖的软件包，然后就可以使用 `npm run` 执行以下各种命令：

```bash
$ cd themes/hugo-nuo

# 安装依赖
$ npm install

# js 脚本开发
$ npm run dev
$ npm run build

# sass 样式开发
$ npm run sass

# 复制照片和字体
$ npm run images
$ npm run fonts

# js / csss lint
$ npm run eslint
$ npm run stylelint

# 优化图片大小
$ npm run imagemin
```

## 兼容性

当前测试结果：

| 浏览器             | 测试版本   | 支持度  |
| :---------------- | :-------: | :----: |
| Google Chrome     | 60        | ✅    |
| Mozilla Firefox   | 54        | ✅    |
| macOS Safari      | 10        | ✅    |
| iOS Safari        | 10        | ✅    |
| Chrome for Mobile | 60        | ✅    |
| Opera             | 46        | ✅    |
| QQ浏览器 for macOS | 4.2       | ✅    |
| Microsoft Edge    | 16        | ✅    |
| IE                | 9-11      | ✅    |
| Old IE            | 7-8       | ❌    |

## TODO

对于这个主题，还有两点是我想去完善的：一是给文章页增加 `TOC` 支持，二是定制一个作品页面。先先立个 flag，有时间的话就做掉吧。

- [ ] 文章 TOC 支持
- [ ] 设计一个作品（Works）页面

# 参考资料

1. [GitHub Repo](https://github.com/laozhu/hugo-nuo)
2. [Hugo Themes Page](https://themes.gohugo.io/hugo-nuo/)
3. [Hugo 主题 Nuo 文章样式预览](https://laozhu.me/post/hugo-nuo-post-preview/)
