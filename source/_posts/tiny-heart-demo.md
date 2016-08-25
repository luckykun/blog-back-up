---
title: 实例demo之小游戏tinyHeart
date: 2016-05-30T09:41:43.000Z
categories: 工作
tags:
  - demo
  - html5
  - canvas
toc: false
---

--------------------------------------------------------------------------------

<style>
.introduce{width:100%;min-height:50px;margin:0 auto;overflow:hidden;margin-top:-40px;}
.introduce p.title{font-size: 16px;font-weight: bold;text-align: center;}
.page{width: 100%;min-height: 600px;margin: 0px auto;margin-top: 6px;}
#main{width: 100%;height: 600px;position: relative;top: -60px;}
#canvas1, #canvas2 {width: 800px;height: 600px;position: absolute;left: 50%;top: 0;margin-left: -400px;}
#canvas1{z-index: 1;}
#canvas2{z-index: 0;}
</style>

<header style="margin-top:-55px;text-align:center;">
    <h1><span>TinyHeart</span><a href="https://github.com/luckykun/tinyHeart" style="font-size:14px;font-weight:normal;">view on github</a></h1>
</header>
<div class="introduce">
    <p class="title">游戏规则：</p>
    <p>1. 大鱼跟着鼠标的位置移动而吃到果实，如吃到红色果实，身体变红，画布下方的个数加1；如吃到蓝色果实，身体变蓝，下方的倍数加1。</p><p>2. 画布右上角显示着小鱼的体力值，初始为10，身体为红色，随着时间的推移，体力值减小，身体颜色变淡。</p><p>3. 大鱼吃到果实之后去喂小鱼，此时小鱼的体力值也会相应增加，画布上方的分值等于画布下方的倍数乘以个数的累加和。</p><p>4. 当小鱼体力值减到0时，游戏结束；点击画布，则可重新开始游戏。好了，拯救小鱼行动开始，躁起来吧！</p>
</div>

<!--more-->

<div class="page">
    <div class="content" id="main">
    <canvas id="canvas1" width="800" height="600"></canvas>
    <canvas id="canvas2" width="800" height="600"></canvas></div>
</div>


<script src="https://rawgit.com/luckykun/tinyHeart/master/js/common-min.js"></script>
<script src="https://rawgit.com/luckykun/tinyHeart/master/js/index-min.js"></script>
<script type="text/javascript">
	jzk.startgame();
</script>


----------
