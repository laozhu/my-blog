---
title: "纯 CSS 实现嵌套菜单"
author: "米老朱"
tags: ["CSS", "Menu"]
date: 2016-10-10
draft: false
---

花了一晚上做玩车老板布置的作业 —— 用纯 CSS 实现了一个嵌套菜单。总结一下：「在动手写样式之前要花时间设计 HTML 结构，好的结构能让样式写起来事半功倍」。

<!--more-->

### HTML 结构

```html
<ul class="menu">
  <li class="item-left">
    <a href="#">
      <div class="menu-item">ItemA</div>
      <div class="item-content">Content of ItemA</div>
    </a>
  </li><li>
    <a href="#">
      <div class="menu-item">ItemB</div>
      <div class="item-content">Content of ItemB</div>
    </a>
  </li><li>
    <a href="#">
      <div class="menu-item">ItemC</div>
      <div class="item-content">Content of ItemC</div>
    </a>
  </li><li class="item-right">
    <a href="#">
      <div class="menu-item">ItemD</div>
      <div class="item-content">Content of ItemD</div>
    </a>
  </li>
</ul>
```

在 HTML 结构设计上需要注意以下三点：

1. 相邻的 `li` 之间不留空格和空行
2. 最左和最右的 `li` 增加独立 `class`
3. `li` 元素的父子菜单项包含在 `a` 标签中

### 消除空隙

当对有序或无序列表应用 `position: inline-block` 样式后，会发现相邻菜单项之间存在空格，空格是由列表项换行导致，直接采用如上不换行写法，问题解决。当然我们还可以用 CSS 来解决这个问题，这样可以保留 HTML 换行结构的美观性，具体方法如下：

```css
ul {
  font-size: 0;
}

ul li {
  display: inline-block;
  font-size: 16px;
}
```

### 消除抖动

实践过程中发现，鼠标进出菜单项，其会发生 1~2 像素的抖动。原因在于「正常状态没有给菜单项设置边框，鼠标悬浮时设置了 1px 的边框」，问题找到修复就很简单，正常状态增加同样宽度边框，并将颜色设置为 transparent 即可。

```css
.menu-item {
  border: 1px solid transparent;

  &:hover {
    border: 1px solid red;
  }
}
```

### 解决定位失灵

在对父子菜单进行 Z 轴定位时发现并没有效果，原因也很简单，对于 `position: static` 的元素无法进行任何定位操作，他会无视 `left | right | top | bottom | z-index` 等所有定位指令。

```css
.menu-item {

  &:hover {
    position: relative;
    z-index: 1000;
    border-bottom: none;
  }
}

.item-content {
  z-index: 100;
}
```

将父菜单置于子菜单之上，并将其下边框取消，以形成父子菜单合在一起的视觉效果，如下图所示：

![Nested Menu](/media/posts/pure-css-nest-menu/01.png)

### 演示

趟过了以上坑，终于实现了想要的效果，很满足，查看完整演示：

<http://codepen.io/laozhu/pen/ORkmpO>
