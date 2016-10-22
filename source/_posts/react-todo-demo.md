---
title: 实例demo之React-Todos
date: 2016-05-08T22:11:51.000Z
categories: 工作
tags:
  - demo
  - react
toc: false
---

--------------------------------------------------------------------------------

<header style="margin-top:-55px;">
    <h1 class="todo-title"><span>React-Todos</span><a href="https://github.com/luckykun/react-demo" style="font-size:14px;font-weight:normal;">view on github</a></h1>
</header>

<div class="container react-todo-demo" style="margin-top:-30px;">
    <div id="app"></div>
</div>

<script src="https://rawgit.com/luckykun/react-demo/master/src/vendor/react.min.js"></script>
<script src="https://rawgit.com/luckykun/react-demo/master/out/bundle.js"></script>

<!--more-->

`React-Todos`是一个管理任务清单的例子，数据使用localstorage存储在浏览器中，有以下几点功能：
- 在输入框输入任务名称，回车键显示。
- 勾选其中一条任务列表，表示已完成。
- 鼠标移入其中一个list，可以删除。
- 底部有全选和删除已完成任务列表的功能。


--------------------------------------------------------------------------------
