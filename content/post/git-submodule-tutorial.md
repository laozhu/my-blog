---
title: "使用 Git Submodule 管理项目"
author: "米老朱"
cover: "/media/posts/git-submodule-tutorial/01.jpg"
tags: ["Git", "GitHub", 'Hugo']
date: 2017-08-24
draft: false
---

在此之前，博主并不知道 [Git Submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules) 为何物，直到遇到这样一个困扰：本站的代码仓库是 → [laozhu/my-blog](https://github.com/laozhu/my-blog)，所使用主题（theme）的代码仓库是 → [laozhu/hugo-nuo](https://github.com/laozhu/hugo-nuo)。这个主题的作者（也就是本人）仍在积极开发中，每次主题有了新的 `commit & push`，我都需要在主题仓库和网站仓库同时 `pull` 代码，`pull` 完成还得在网站仓库 `commit` 代码，这种模式实在很 low 逼，下面我们通过 `Git Submodule` 解决这个问题。

<!--more-->

## 子模块简介

![Git submodule workflow](/media/posts/git-submodule-tutorial/01.jpg)

`Git Submodule` 中文通常翻译为 Git 子模块，它允许我们在一个代码仓库中优雅的嵌套使用另外一个或多个代码仓库，从而达到减少代码重复提交，优化工作流程的目的，是内部团队和开源社区协作都非常依赖的 Git 特性。如果对 `Git Submodule` 还不甚了解，可以去看一下 [gohugoio/hugoThemes](https://github.com/gohugoio/hugoThemes)，这是 [Hugo Themes](https://themes.gohugo.io/) 官方网站的代码仓库，它就是通过 `Git Submodule` 特性整合了来自全球各地的 Hugo 主题，并保持与原主题仓库的代码同步。

## 创建子模块

在主代码仓库里面创建子模块引用，执行以下指令（以我的博客仓库和主题仓库为例）即可：

```bash
# Usage
$ git submodule add [url] [path]

# With path
$ git submodule add https://github.com/laozhu/hugo-nuo themes/hugo-nuo

# Without path
$ cd themes
$ git submodule add https://github.com/laozhu/hugo-nuo
```

## 子模块引用文件

在 [my-blog](https://github.com/laozhu/my-blog) 这个仓库下面多出了一个 `.gitmodules` 文件，该文件列出了所包含的子模块列表，并为列表中每一个子模块指定了本地路径（path）和远程仓库地址（url），除此以外我们还可选为子模块指定 `branch` 分支，不指定默认为 `master` 分支。

```bash
$ cat .gitmodules

[submodule "themes/hugo-nuo"]
  path = themes/hugo-nuo
  url = https://github.com/laozhu/hugo-nuo
```

顺便观摩一下 Hugo Themes 仓库的 [.gitmodules](https://github.com/gohugoio/hugoThemes/blob/master/.gitmodules) 引用文件。

## 查看子模块

要查看当前代码仓库所使用的子模块及其状态，除了看 `.gitmodules` 文件外，还可以执行 `git submodule ` 命令。

```bash
$ git submodule
# 已检出子模块代码
cedbe91340dbcff661fa089b116441b11b050d38 themes/hugo-nuo (heads/master)
# 前面带 - 表示未检出代码，子模块是空文件夹
-cedbe91340dbcff661fa089b116441b11b050d38 themes/hugo-nuo (heads/master)
```

## 克隆含有子模块的项目

当你需要克隆一个包含了子模块的远程代码仓库，有两种方式（第二种更加简洁，更优）。

```bash
# Clone => Init => Update
$ git clone https://github.com/laozhu/my-blog
$ git submodule init
$ git submodule update

# Clone recursive
$ git clone --recursive https://github.com/laozhu/my-blog
```

## 拉取子模块更新

拉取子模块更新不再需要 `clone` 和 `init` 操作，只需 `update` 即可，当你的主代码仓库执行 `pull` 或者切换分支操作后，别忘了执行 `update` 操作，以保证子模块中的代码与新的 `.gitmodules` 中版本一致。为了防止误提交旧的子模块依赖信息，每次执行 `pull` 后，可使用 `git status` 查看文件状态。

```bash
# After Pull
$ git pull https://github.com/laozhu/my-blog
# After Checkout
$ git checkout -b develop origin/develop
# You need
$ git status -s
$ git submodule update
```

## 提交子模块修改

当你需要对当前使用的某个子模块进行修改，并且希望所做修改能够提交到子模块的主仓库，一定要记得切换到 `master` 分支再修改并提交。

```bash
cd themes/hugo-nuo
git checkout master
git add .
git commit -m "Create shortcode for stackblitz"
git push orgin master
```

## 将目录转化为子模块

项目开发过程中会遇到这样一个场景：觉得某一个功能抽象程度很高，与整个系统也不耦合，于是就希望把这个功能独立成一个模块进行团队共享甚至开源，这时候我们就需要将一个子目录转化为一个子模块，但因为子目录的代码在主代码仓库中已经被跟踪过了，如果我们仅仅是删除子目录，添加同名的子模块的话，`git` 就会报下面的错误：

```bash
$ rm -rf themes/hugo-nuo
$ git submodule add https://github.com/laozhu/hugo-nuo themes/hugo-nuo
'hugo-nuo' already exists in the index
```

那到底该怎么办呢？很简单，使用 `git rm` 取消子目录的暂存即可：

```bash
$ git rm -r themes/hugo-nuo
$ git submodule add https://github.com/laozhu/hugo-nuo themes/hugo-nuo
```

## 删除子模块

Git 中删除子模块略微麻烦一些，因为目前还没有 `git submodule rm` 这样的命令行，我们要做很多工作才能删得干净：

```bash
$ git submodule deinit themes/hugo-nuo
$ vim .gitmodules # 移除要删除的子模块
$ git add .gitmodules
$ git rm --cached themes/hugo-nuo
$ rm -rf .git/modules/themes/hugo-nuo
$ rm -rf themes/hugo-nuo
$ git commit -m "Remove submodule themes/hugo-nuo"
```

真希望有 `git submodule rm` 这个命令行...

## 子模块批量处理

对于像 [gohugoio/hugoThemes](https://github.com/gohugoio/hugoThemes) 这种超级依赖子模块的仓库怎么管理呢，使用 `foreach` 循环指令就可以啦。

```bash
# Nested submodule
$ git submodule foreach git submodule update

# Checkout master then pull
git submodule foreach 'git checkout master; git pull'
```

## 参考资料

1. [Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
2. [Git submodule的坑](http://blog.devtang.com/2013/05/08/git-submodule-issues/)
3. [git子模块](https://yuguo.us/weblog/git-submodule/)
4. [Git Submodules: Adding, Using, Removing, Updating](https://chrisjean.com/git-submodules-adding-using-removing-and-updating/)
5. [How do I remove a submodule?](https://stackoverflow.com/questions/1260748/how-do-i-remove-a-submodule)
