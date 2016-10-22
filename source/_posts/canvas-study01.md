---
title: canvas学习之API整理笔记（一）
date: 2016-09-01T11:07:56.000Z
categories: 工作
tags:
  - canvas
  - html5
toc: true
---

--------------------------------------------------------------------------------

其实canvas本身很简单，就是去学习它的API，多看实例，多自己动手练习，多总结。但是canvas的API实在是有点多，对于初学者来说，可能学到一半就止步不前了。我也有这种感觉，在学习的过程中，编写实例，用到了其中很多的属性和方法，但是回头来看的时候总觉得什么也没用。所以决定系统性的记录一下它常用到的API，方便以后查阅，也顺便造福一下大家。

另外：附一个之前学习的时候自己跟着教程写的一个小游戏：[tinyHeart小游戏](http://luckykun.com/work/2016-05-30/tiny-heart-demo.html)
<!--more-->

# 开始之前
假设html代码中有一个canvas标签：

```js
<canvas id="canvas">你的浏览器不支持canvas！</canvas>
```

如果你的浏览器是IE8及以下，那么很遗憾，上面那段文字会被渲染出来！而且下面的方法也都不能使用了；所以请使用支持canvas的浏览器来使用后面的方法。

```js
//获取canvas容器
var can = document.getElementById(‘canvas’);
//创建一个画布
var ctx = can.getContext(‘2d’);
```

另外我们可以还可以得到容器的宽和高度

```js
var canWid = can.width;   //canvas 的宽度
var canHei = can.height;   //canvas 的高度
```

canvas只是一个容器，本身没有绘制的能力，所以我们要得到一个画布`ctx`，使之具有绘制各种图形的能力。下文所有的方法都是ctx的方法。

# 绘制
绘制一个矩形：

```js
//填充矩形（x, y是横纵坐标，原点在canvas的左上角）
ctx.fillRect(x, y, width, height);
//边框矩形，默认1px 黑色。   
ctx.strokeRect(x, y, width, height);
//清除指定的矩形区域，变为透明
ctx.clearRect(x, y, width, height);       //绘制动态效果时，常用来清除整个画布
```

绘制路径:

```js
//新建路径，beginPath是绘制新图形的开始
ctx.beginPath()
//路径（线）的起点，一般在上面这条命令后执行
ctx.moveTo(x, y)  
//线的终点
ctx.lineTo(x, y)   
//绘制圆形
ctx.arc(x, y, r, start, end, true/false)   //x, y圆心，r半径，start和end是开始和结束角度，false表示顺时针（默认），true表示逆时针。
//绘制弧线
ctx.arcTo(x1, y1, x2, y2, r);    //当前端点、(x1,y1)和(x2,y2)这三个点连成的弧线，r是半径。
//闭合路径，不是必须的，如果线的终点跟起点一样，会自动闭合。
ctx.closePath()
//通过线条绘制轮廓（边框）
ctx.stroke()   
//通过路径填充区域（实心）
ctx.fill()
```

说明：

1.fill()和stroke()函数表示绘图结束。如果要继续绘制，需要重新新建路径（beginPath()）。

2.如果lineTo()最后的路径没有封闭，fill()函数会自动封闭路径，而stroke()函数不会。

例：绘制一个三角形

```js
ctx.beginPath();
ctx.moveTo(75, 50);    //路径起点
ctx.lineTo(100, 75);
ctx.lineTo(100, 25);
ctx.fill();   //自动将路径闭合，并默认填充黑色。
```

# 样式
- 颜色

  ```js
  ctx.fillStyle = 'red'   //针对fill()有效的颜色，还可以取值：'#fff'、'rgba(0, 0, 0, 0.5)'等。
  ctx.strokeStyle = 'red'   //针对stroke()有效的颜色，取值同上。
  ctx.globalAlpha = 0.5;   //透明度
  ```

- 线段端点

  ```js
  ctx.lineWidth = 2;   //线条宽度
  ctx.lineCap = 'butt(默认)'、'round(圆弧)'、'square(方形)'  //线段端点显示的样式
  ```

  ```js
  var ctx = document.getElementById('canvas').getContext('2d');
  var lineCap = ['butt','round','square'];
  ctx.strokeStyle = 'black';
  for (var i=0;i<lineCap.length;i++){
      ctx.lineWidth = 15;
      ctx.lineCap = lineCap[i];
      ctx.beginPath();
      ctx.moveTo(25+i*50,10);
      ctx.lineTo(25+i*50,140);
      ctx.stroke();
  }
  ```

  效果如下图：

  ![](https://img.alicdn.com/tps/TB1MubjMVXXXXXuXFXXXXXXXXXX-143-158.png)

- 线段连接处

  ```js
  ctx.lineJoin = 'miter(默认)'、round(圆角)、`bevel(横线)`   //两线段连接处所显示的样子
  ```

  ```js
  var ctx = document.getElementById('canvas').getContext('2d');
  var lineJoin = ['round','bevel','miter'];
  ctx.lineWidth = 10;
  for (var i=0;i<lineJoin.length;i++){
      ctx.lineJoin = lineJoin[i];
      ctx.beginPath();
      ctx.moveTo(10,50+i*40);
      ctx.lineTo(50,10+i*40);
      ctx.lineTo(90,50+i*40);
      ctx.lineTo(130,10+i*40);
      ctx.lineTo(170,50+i*40);
      ctx.stroke();
  }
  ```

  效果如下图：

  ![](https://img.alicdn.com/tps/TB1XRvfMVXXXXccXFXXXXXXXXXX-181-139.png)

- 虚线

  ```js
  ctx.setLineDash([4, 2])   //设置虚线，参数为数组，第一个值为实现宽度，第二个值为空白的宽度
  ctx.lineDashOffset = 0;   //虚线起始偏移量
  ```

  ```js
  var can = document.getElementById('canvas');
  var ctx = can.getContext('2d');
  var offset = 0;
  function draw() {
      offset++;      
      if (offset > 16) {            
          offset = 0;      
      }
      ctx.clearRect(0,0, can.width, can.height);  
      ctx.setLineDash([6, 2]);  
      ctx.lineDashOffset = -offset;  
      ctx.strokeRect(10,10, 100, 100);
  }
  setInterval(draw, 20);
  ```

  效果如下图：

  ![](https://img.alicdn.com/tps/TB1knDkMVXXXXXLXFXXXXXXXXXX-145-114.gif)

- 渐变

  ```js
  var bg = ctx.createLinearGradient(x1, y1, x2, y2);  //定义线性渐变，渐变的起点 (x1,y1) 与终点 (x2,y2)。
  var bg1 = ctx.createRadialGradient(50, 50, 0, 50, 50, 100);   //定义径向渐变
  bg.addColorStop(0, 'red');  //定义好，之后开始上色
  bg.addColorStop(0.5, 'blue');
  bg.addColorStop(1, 'rgba(0, 0, 0, 0.6)');
  ```

  ```js
  var bg = ctx.createLinearGradient(0, 0, 0, 200);     //线性渐变
  bg.addColorStop(0, 'black');
  bg.addColorStop(0.6, '#fff');
  ctx.fillStyle = bg;
  ctx.fillRect(10, 10, 100, 100);
  ```

  效果如下图：

  ![](https://img.alicdn.com/tps/TB1G3_XMVXXXXX4aXXXXXXXXXXX-125-119.png)

```js
  var bg1 = ctx.createRadialGradient(100, 100, 0, 100, 100, 50);    //径向渐变
  bg1.addColorStop(0, '#FF5F98');
  bg1.addColorStop(0.75, '#FF0188');
  bg1.addColorStop(1, 'rgba(255,1,136,0)');
  ctx.fillStyle = bg1;
  ctx.fillRect(0,0,150,150);
```

  效果如下图：

  ![](https://img.alicdn.com/tps/TB1.qW8MVXXXXbcaXXXXXXXXXXX-140-146.png)

# 文字

```js
var ctx = document.getElementById('canvas').getContext('2d');
ctx.shadowOffsetX = 2;     //X轴阴影距离，负值表示往上，正值表示往下
ctx.shadowOffsetY = 2;     //Y轴阴影距离，负值表示往左，正值表示往右
ctx.shadowBlur = 2;     //阴影的模糊程度
ctx.shadowColor = "rgba(0, 0, 0, 0.5)";    //阴影颜色
ctx.font = "30px Times New Roman";    //设置字体和字体大小
ctx.fillStyle = "Black";
ctx.fillText("Sample String", 15, 30);    //实体文字
ctx.strokeStyle = 'red';
ctx.strokeText('Hello world', 15, 100);    //边框文字
```

效果如下图：

![](https://img.alicdn.com/tps/TB1HIvbMVXXXXcuaXXXXXXXXXXX-219-149.png)

文字的属性出了上面的列出的以外，还有以下一些属性：

```js
ctx.font = '20px Times New Roman';
ctx.textAlign = 'center';    //start, end, left, right or center
ctx.textBaseline = 'middle'  //top, hanging, middle, alphabetic, ideographic, bottom
ctx.direction = 'inherit'   //ltr, rtl, inherit
```

# 图像

```js
var img = new Image();
img.src= './images/background.jpg';
ctx.drawImage(img, 0, 0);   //img为图像，(0, 0)为起始坐标
```

这里的img可以是一个img对象，也可以是一个img元素。

```js
<img id="img" src="./images/background.jpg" style="display:none;">
ctx.drawImage(document.getElementById('img'), 0, 0);
```

另外，绘制图片的时候还可以对图片进行缩放，类似于css中的`background-size`：

```js
ctx.drawImage(img, 0, 0, w, h);   //w、h指定图片的宽高，则会同比例缩放。
```

# 变形
慢慢的从这里开始，就要开始涉及到复杂的绘制了。而开始复杂的绘制之前，我们得先了解一个概念：`canvas绘图的状态`。
- 状态

  canvas 的状态就是当前画面应用的所有样式和变形的一个快照。另外，用来操作这个状态的有两个方法：`save()和restore()`。save()用来保存当前状态，restore()用来恢复刚才保存的状态。他们都可以多次调用。

```js
  ctx.fillStyle = 'black';
  ctx.fillRect(20, 20, 150, 150);
  ctx.save();  //保存当前状态
  ctx.fillStyle= '#fff';
  ctx.fillRect(45, 45, 100, 100);
  ctx.restore();    //恢复到刚才保存的状态
  ctx.fillRect(70, 70, 50, 50);
```

  效果如下图：

  ![](https://img.alicdn.com/tps/TB1CLzJMVXXXXa_aXXXXXXXXXXX-235-227.png)
- 位移(translate)

  ```js
  ctx.translate(x, y);   //更改canvas的原点
  ```

  ```js
  var ctx = document.getElementById('canvas').getContext('2d');
  for(var i = 1; i< 4; i++) {
      ctx.save();   //使用save方法保存状态，让每次位移时都针对（0，0）移动。
    ctx.translate(100*i, 0);
    ctx.fillRect(0, 50, 50, 50);
    ctx.restore();
  }
  ```

  效果如下图：

  ![](https://img.alicdn.com/tps/TB1Oyf8MVXXXXX9XXXXXXXXXXXX-425-170.png)

- 旋转

  ```js
  ctx.rotate(Math.PI * 2)     //参照原点顺时针旋转360度
  ```

```js
  ctx.translate(75,75);    //把原点移动到(75, 75);
  for (var i=1; i<6; i++){       // 从里到外一共6圈
    ctx.save();
    ctx.fillStyle = 'rgb('+(50*i)+','+(255-50*i)+',255)';
    for (var j=0; j<i*6; j++){     // 每一圈有i*6个圆点
      ctx.rotate(Math.PI*2/(i*6));
      ctx.beginPath();
      ctx.arc(0,i*12.5,5,0,Math.PI*2,true);
      ctx.fill();
    }
    ctx.restore();
  }
```

  效果如下图：

  ![](https://img.alicdn.com/tps/TB1VMIaMVXXXXXjXXXXXXXXXXXX-183-181.png)
- 缩放

  ```js
  ctx.scale(x, y);     //基于原点缩放，x、y是两个轴的缩放倍数
  ```

  ```js
  var ctx = document.getElementById('canvas').getContext('2d');
  ctx.fillStyle = 'red';
  ctx.scale(0.8, 1.2);
  ctx.beginPath();
  ctx.arc(75, 75, 60, 0, Math.PI * 2);
  ctx.fill();
  ```

  本来绘制的是一个半径为60的圆形，但是经过缩放之后，实际效果如下：

  ![](https://img.alicdn.com/tps/TB1drnVMVXXXXbqXVXXXXXXXXXX-127-194.png)

# 动画
> 元素的位置移动，就形成了动画。

一帧一帧的来渲染这个元素，而且这个元素每一帧的位置都不一样，我们的眼睛看到的就是动画了。实现起来也很方便，js提供了两个方法：`setTimeout 和setInterval`都可以实现，但是一个有逼格的程序员实现动画是不会用这两个方法的，而是用`requestAnimationFrame`这个方法。有什么区别呢？下面简单做个比较。
- `setInterval(myFun, 10);` 意思是隔一毫秒执行一个myFun函数，但是这样就有一个问题了，比如我myFun函数里面绘制的东西比较耗时，而10ms之内还没有完全绘制出来，但是这段代码强制1ms之后又开始绘制下一帧了，所以就会出现`丢帧`的问题；反之，如果时间设置太长，就会出现`画面不流畅、视觉卡顿`的问题。
- `requestAnimationFrame(myFun);` 如果我们这样写，又是什么意思呢？意思是根据一定的时间间隔，会自动执行myFun函数来进行绘制。这个"一定的时间间隔"就是根据浏览器的性能或者网速快慢来决定了，总之，它会保证你绘制完这一帧，才会绘制下一帧，保证性能的同时，也保证动画的流畅。

# 结语
这些API已经包含了大部分常用的了，结合requestAnimationFrame函数已经可以绘制很多酷炫的效果了。但是光看这些API很简单，但是想要在绘制游戏或者动画中用起来得心应手还需要看大量的实例，自己实践总结的。

本文就到这里，感谢各位看官～



--------------------------------------------------------------------------------
