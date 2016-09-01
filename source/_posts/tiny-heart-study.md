---
title: h5+canvas实现酷炫的小游戏
date: 2016-05-29T09:41:43.000Z
categories: 工作
tags:
  - 教程
  - html5
  - canvas
toc: true
---

-----------------

最近除了做业务，也在尝试学习h5和移动端，在这个过程中，学到了很多，利用h5和canvas做了一个爱心鱼的小游戏。源码在github上，down下来直接就能够运行。要是觉得还行，就给个star吧！[源码地址点这里](https://github.com/luckykun/tinyHeart)

下面我就做游戏的步骤来分享总结一下用到h5和canvas的API和一些常见的数学函数。推荐你先去玩一玩游戏，才能更好的明白这些逻辑。[点这里去玩一下](http://luckykun.com/work/2016-05-30/tiny-heart-demo.html)



<!--more-->

# 前言
首先，一个游戏最重要的就是动画，怎么让元素动起来呢？先来看一句话：

> 元素的位置移动，就形成了动画。

一帧一帧的来渲染这个元素，而且这个元素每一帧的位置都不一样，我们的眼睛看到的就是动画了。OK，先来介绍`requestAnimationFrame`这个函数。

我们都知道，隔一段时间重新渲染，可以用到`setTimeout` 和`setInterval`这两个函数，那这里为什么不用呢？

我来简单举个例子吧：
- setInterval(myFun, 1);  意思是隔一毫秒执行一个myFun函数，但是这样就有一个问题了，比如我myFun函数里面绘制的东西比较耗时，而1ms之内还没有完全绘制出来，但是这段代码强制1ms之后又开始绘制下一帧了，所以就会出现`丢帧`的问题，而如果时间设置太长，就会出现`视觉卡顿`的问题。
- requestAnimationFrame(myFun); 如果我们这样写，又是什么意思呢？意思是根据`一定的时间间隔`，会自动执行myFun函数来进行绘制。这个"一定的时间间隔"就是根据浏览器的性能或者网速快慢来决定了，总之，它会保证你绘制完这一帧，才会绘制下一帧，保证性能的同时，也保证动画的流畅。

动画解决了，那么用什么来绘制每一帧的页面呢？这时就要用到h5的神奇----`canvas`了，所以canvas画布的API非常重要。

## html文件

```html
    <div class="page">
        <div class="content" id = "main">
            <canvas id = "canvas1" width="800" height="600">
            </canvas>
            <canvas id = "canvas2" width="800" height="600">
            </canvas>
        </div>
    </div>
```

- 定义两个画布，分别在画布上绘制相应的物体；
- canvas2 上绘制，背景、海葵、果实；
- canvas1 上绘制，大鱼、小鱼、显示文字、圆圈特效；

## js文件

```js
    function init(){
        can1 = document.getElementById('canvas1');     //画布
        ctx1 = can1.getContext('2d');   //画笔
        can2 = document.getElementById('canvas2');
        ctx2 = can2.getContext('2d');   //下面的canvas
    }
    function gameloop(){
        requestAnimFrame(gameLoop);
        //绘制物体...
    }
    var requestAnimFrame = (function() {
        return window.requestAnimationFrame || window.webkitRequestAnimationFrame || window.mozRequestAnimationFrame || window.oRequestAnimationFrame || window.msRequestAnimationFrame ||
        function(callback, element) {
            return window.setTimeout(callback, 1000 / 60);
        };
})();
```

- init函数初始化一些变量，比如海葵对象，大鱼、小鱼对象等等。
- gameloop函数用于绘制每一帧的页面。下面所介绍的所有绘制函数都是在这里执行。
- requestAnimFrame函数是为了兼容所有浏览器。

下面我们就开始绘制游戏中出现的东西，顺便看看都用到了哪些有趣的API函数。go！go！go！

# 绘制背景和海葵
背景是一张图，而海葵是一个类，它有x坐标，y坐标，个数等等属性，有初始化init和draw方法。

```js
    drawImage(image, x, y, width, height)
    ctx2.save();
    ctx2.globalAlpha = 0.7;
    ctx2.lineWidth = 20;
    ctx2.lineCap = 'round';
    ctx2.strokeStyle = '#3b154e';
    ctx2.beginPath();
    ctx2.moveTo(this.rootx[i], canHei);     //起始点
    ctx2.lineTo(this.rootx[i], canHei - 220,);  //结束点    ctx2.stroke();
    ctx2.restore();
```

- ctx2.drawImage(image, x, y, width, height)    //x,y代表坐标，width和height代表宽高
- ctx2.save();   //定义作用空间
- ctx2.globalAlpha = 0.7;      //定义线的透明度
- ctx2.lineWidth = 20;       //  宽度
- ctx2.lineCap = 'round';     // 圆角
- ctx2.strokeStyle = '#3b154e';   //定义绘制线条的颜色
- ctx2.beginPath();    //开始路径
- ctx2.moveTo(x,y);     //线的起点，x,y代表坐标（坐标原点在左上角）
- ctx2.lineTo(x,y);    // 线条从起点连接到这个点
- ctx2.stroke();       // 开始绘制线条
- ctx2.restore();      //作用空间结束

# 海葵产生果实
果实也是一个类，他的属性有：坐标、类型（黄色和蓝色）、大小、状态（显示还是隐藏）、速度（向上漂浮的速度）等等属性；他的方法有：初始化init、出生born和绘制draw。

draw方法：

```js
    for(var i =0;i< this.num; i++){
        if(this.alive[i]){
            //find an ane, grow, fly up...
            if(this.size[i] <= 16){   //长大状态
                this.grow[i] = false;
                this.size[i] += this.speed[i] * diffframetime * 0.8;
            }else{   //已经长大,向上漂浮
                this.grow[i] = true;
                this.y[i] -= this.speed[i] * 5 * diffframetime;
            }
            var pic = this.orange;
            if(this.type[i] == 'blue')   pic = this.blue;
            ctx2.drawImage(pic, this.x[i] - this.size[i] * 0.5, this.y[i] - this.size[i] * 0.5, this.size[i], this.size[i]);
            if(this.y[i] < 8){
                this.alive[i] = false;
            }
        }
    }
```

born方法：随机找到一个海葵的坐标，在海葵的坐标上出生一个果实。

# 绘制大鱼和小鱼
大鱼和小鱼都是一个类，它的属性有：坐标、旋转角度、尾巴摆动时间间隔、眨眼睛时间间隔、身体图片数组....等等

先把大鱼绘制出来，用canvas的drawImage方法。

比较难的是大鱼的动画，大鱼会随着鼠标移动而移动的动画，这里定义了两个函数：

```js
function lerpAngle(a, b, t) {     //计算每一帧旋转的角度
    var d = b - a;
    if (d > Math.PI) d = d - 2 * Math.PI;
    if (d < -Math.PI) d = d + 2 * Math.PI;
    return a + d * t;
}
function lerpDistance(aim, cur, ratio) {   //aim：目标 cur：当前 ratio：百分比  计算每一帧趋近的距离
    var delta = cur - aim;
    return aim + delta * ratio
}
this.momTailTimer += diffframetime;
if(this.momTailTimer > 50){
    this.momTailIndex = (this.momTailIndex + 1) % 8;      //根据时间间隔改变尾巴图片
    this.momTailTimer %= 50;
}
```

- lerpDistance 是计算每一帧大鱼趋紧到鼠标的距离。
- lerpAngle  用来计算大鱼每一帧向鼠标旋转的角度。 定义这两个函数，让大鱼动起来比较平滑。

获得了一个角度之后，怎么让大鱼旋转起来呢？这里又需要用到几个API了。
- ctx1.save();  //建议每次绘制都使用save和restore，可以避免定义样式，发生冲突。
- ctx1.translate(this.x, this.y);      //把原点变成(this.x , this.y);
- ctx1.rotate(this.angle);     //根据原点顺时针旋转一个角度

绘制小鱼跟大鱼是一样的，不做详述。但是需要注意的是绘制小鱼的时候有个判断，当小鱼的颜色变白的时候，游戏结束。

```js
this.babyBodyTimer += diffframetime;
if(this.babyBodyTimer > 550){   //身体图片变化的计数器 > 550ms
    this.babyBodyIndex += 1;     //身体图片变淡
    this.babyBodyTimer %= 550;
    scoreOb.strength = ((20 - this.babyBodyIndex)/2).toFixed(0);
    if(this.babyBodyIndex > 19){   //如果身体变成白色，game over；
        this.babyBodyIndex = 19;
        scoreOb.gameOver = true;
        can1.style.cursor = "pointer";
    }
}
```

# 大鱼吃果实
大鱼吃果实是根据距离来判断定的，如果大鱼和果实的距离小于30，则让果实消失，并且出现白色圆环，并且分值有一定的变化。

```js
    jzk.momEatFruit = function(){     //判断果实和大鱼之间的距离，小于30说明被吃掉
        for(var i = 0;i < fruitOb.num; i++ ){
            if(fruitOb.alive[i] && fruitOb.grow[i]){
                var len = calLength2(fruitOb.x[i], fruitOb.y[i], momOb.x, momOb.y);
                if(len < 30){
                    fruitOb.dead(i);    //如果距离小于30，则被吃掉
                    waveOb.born(i);     //吃掉的时候，产生圆圈
                    scoreOb.fruitNum ++;    //吃到的果实数量＋1
                    momOb.momBodyIndex = momOb.momBodyIndex == 7 ? momOb.momBodyIndex : (momOb.momBodyIndex + 1);      //大鱼的身体颜色红
                    if(fruitOb.type[i] == 'blue'){
                        scoreOb.doubleNum ++;  //吃到蓝色果实，倍数＋1
                    }
                }
            }
        }
    }
```

其中有一个calLength2函数，使用来计算两个点之间的距离的。

```js
function calLength2(x1, y1, x2, y2) {    //计算两个点之间的距离，，， 先求平方和，再开平方
    return Math.sqrt(Math.pow(x1 - x2, 2) + Math.pow(y1 - y2, 2));
}
```

大鱼吃到果实的时候，会产生一个白色的圆圈，这个效果怎么实现呢？

首先，我们定义一个waveObject类，它的属性有：坐标、数量、半径、使用状态。它的方法有：初始化、绘制和出生。

我们来看一下绘制圆圈的方法：

```js
for(var i = 0;i< this.num; i++){
    if(this.status[i]){     //如果圆圈是使用状态，则绘制圆圈
        this.r[i] += diffframetime * 0.04;
        if(this.r[i] > 60){
            this.status[i] = false;
            return false;
        }
        var alpha = 1 - this.r[i] / 60;
        ctx1.strokeStyle = "rgba(255, 255, 255, "+ alpha +")";
        ctx1.beginPath();
        ctx1.arc(this.x[i], this.y[i], this.r[i], 0, 2 * Math.PI);   //画圆，
        ctx1.stroke();
    }
}
```

一帧一帧的画每一个圆，圆的半径逐渐增大，透明度逐渐减小，直到半径大于60的时候，把状态设为false，让其回归物体池中。

这里又用到了一个新的方法：ctx1.arc(x,y,r,deg);   //画圆，x,y是中心圆点，r是半径，deg是角度，360度就是一个整圆。

再来看一下出生的方法：

```js
for(var i = 0; i< this.num; i++){
    if(!this.status[i]){
        this.status[i] = true;   //把圆圈状态设为使用状态
        this.x[i] = fruitOb.x[index];
        this.y[i] = fruitOb.y[index];
        this.r[i] = 10;
        return false;   //找到一个未使用的圆圈，就结束。
    }
}
```

圆圈出生的坐标就是被吃果实的坐标。

# 大鱼喂小鱼
大鱼喂小鱼同上，不再详述，这里喂小鱼之后，大鱼身体变白，小鱼随果实数量相应增多，另外需要注意的是，此时产生圆圈的坐标是小鱼的坐标。

# 游戏分值计算
定义一个数据类，它的属性有：吃到的果实数量、倍数、总分、力量值、游戏状态（是否结束）等；方法有：初始化、绘制分数。

这里我们需要在画布上绘制文字，又用到了新的API：
- ctx1.save();
- ctx1.font = '40px verdana';   定义文字的大小和字体；
- ctx1.shadowBlur = 10;    定义文字的阴影宽度
- ctx1.shadowColor = "white";   定义文字阴影的颜色；
- ctx1.fillStyle = "rgba(255, 255, 255, "+ this.alpha +")";  定义文字的颜色（rgba,a代表透明度）
- ctx1.fillText("GAME OVER", canWid _ 0.5, canHei _ 0.5 - 25);   绘制文字，第一个参数是字符串，支持表达式，后两个参数是坐标值。
- ctx1.font = '25px verdana';
- ctx1.fillText("CLICK TO RESTART", canWid _ 0.5, canHei _ 0.5 + 25);
- ctx1.restore();

# 总结
好啦，整个游戏的制作过程就分享完了，做的过程中有遇到过很多问题，不过都一一解决了，加深了很多以前模糊的概念，也学到了很多新的知识，比如使用rgba()来一起控制颜色和透明度，以前还真没用到过。

这个游戏本身功能比较简单，但是动画还算比较酷炫。这也算是一个比较基本的动画基础框架了，而比较不容易理解的地方也有很多，比如求趋近的角度函数`lerpAngle(a,b,c)`，还有`Math.atan2()`这个函数，等等。 欢迎大家提出bug或者改进建议～～～
