---
title: 'Antd Pro 项目开发总结：网络请求'
author: '米老朱'
cover: '/media/posts/bye-2017-ready-2018/01.jpg'
tags: ['React', 'Antd', 'Cors']
date: 2018-12-31
draft: true
---

最近一个项目基于 [Ant Design Pro](https://pro.ant.design/) 进行二次开发，项目完全前后端分离，网络请求部分使用其自带的 [request.js](https://github.com/ant-design/ant-design-pro/blob/master/src/utils/request.js) 方法进行二次开发。主要解决三个问题：使用 [JWT](https://jwt.io/) 对网络请求进行鉴权，同时前端支持自动过期；由于项目网址和 API 地址不是同一个域名，解决了开发和线上环境的跨域问题；改写 request.js 函数，使其支持 WebSocket 类型请求。

<!--more-->

## 支持 JWT

## JWT 如何过期

## 开发环境跨域支持

## 生产环境跨域支持

## 请求预检

## WebSocket 集成

## 踩过的坑

关于请求缓存，antd-pro 自带的 request.js 会缓存请求，但是当请求数据过大时候会报错，我们可以针对某次关闭请求缓存，避免报错。
