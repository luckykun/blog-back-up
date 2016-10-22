---
title: CSS3 3D立方体效果－transform也不过如此
date: 2016-07-23T17:41:57.000Z
categories: 工作
tags:
  - CSS3
  - 教程
  - 3D效果
toc: true
---

--------------------------------------------------------------------------------

CSS3系列已经学习了一段时间了，第一篇文章写了一些css3的奇技淫巧，[原文戳这里](http://luckykun.com/work/2016-07-04/css3-study01.html)，还获得了较多网友的支持，在此谢过各位，你们的支持是我写文章最大的动力^_^。

那么这一篇文章呢，主要是通过一个3D立方体的效果实例来深入了解css3的`transform属性`，下面是这个实例的截图，加上动画还能旋转起来哟，是不是很酷炫？换上你喜欢的女生的照片，就可以大胆的撩妹了，哈哈！ `想要查看demo，请点击这里`，[3D transform立方体效果](http://luckykun.com/work/2016-07-24/rect-grid-demo.html)

![](http://7xtawy.com1.z0.glb.clouddn.com/rect.png)

<!--more-->


# 初识transform
顾名思义：变换。就可以想到它可以做很多很多的事情了，这个属性有很多的值，在这里简单列举一下：
- translate(x,y)、translateX(x)、translateY(y)、translateZ(z)、translate3d(x,y,z)：定义位置的`移动距离`。
- scale(x,y)、scaleX(x)、scaleY(y)、scaleZ(z)、scale3d(x,y,z)：定义元素的`缩放比例`。
- rotate(angle)、rotateX(a)、rotateY(a)、rotateZ(a)、rotate3d(x,y,z,angle)：定义元素的`旋转角度`。
- skew(x-angle,y-angle)、skewX(angle)、skewY(angle)：定义元素的`倾斜角度`。

# 3D效果的认知
![](http://7xtawy.com1.z0.glb.clouddn.com/weidu.png)

我们可以看到这是一个三维的空间图，不要被第一眼的复杂感吓到，仔细分解一下其实还是很清晰的：反正就X轴、Y轴、Z轴三个方向嘛。

想象一下，假如你现在坐在电脑面前，`电脑屏幕中心是原点，原点往右就是X轴正方向，往下就是Y轴正方向，往屏幕前方（也就是往人脸）的方向就是Z轴的正方向了`。把坐标轴的方向搞清楚了，上面的方法就能正确的使用了。

如果你觉得上面的解释仍然太过于枯燥抽象，那就来举个生活中的例子来对应一下三个rotate属性（rotateX、rotateY、rotateZ）吧：

如下图：体操表演－绕着X轴旋转就是`rotateX`（单杠就是X轴）

![](http://7xtawy.com1.z0.glb.clouddn.com/x.png)

如下图：钢管舞表演－绕着Y轴旋转就是`rotateY`（钢管就是Y轴）

![](http://7xtawy.com1.z0.glb.clouddn.com/y.png)

如下图：转盘旋转－绕着Z轴在旋转就是`rotateZ`（想象有一根绳子从转盘的中心穿过，图不好找＝＝）

![](http://7xtawy.com1.z0.glb.clouddn.com/Z.png)

# perspective属性
> perspective的中文意思是：透视，视角！该属性用来激活一个3D空间。

当为元素定义perspective属性时，其子元素都会获得透视效果（使用了3D变换的元素）。所以一般来说perspective属性都应用在父元素上，我们可以把这个父元素称为`舞台元素`。

只看解释可能还是难以理解，我们还是用实例说话吧：

![](http://7xtawy.com1.z0.glb.clouddn.com/test9.gif)

从上图可以看出，div1是div2的父元素，开始我们给div2元素增加旋转`transform:rotateX(50deg)`的时候，只感觉div2在平面上被'压缩'了，没有3D的效果，然后当我们给父元素div1增加`perspective: 150px;`的时候，立马就能看到3D的效果了，感受到他的神奇之处了吧。

`另外，perspective的取值也一直是个谜，经过我的多次查阅和测试，得出了一下几个结论`：
- 取值为none或不设置，就没有3D空间。
- 取值越小，3D效果就越明显，也就是你的眼睛越靠近真3D。
- 貌似当取值为元素的宽度时，视觉效果比较好。

# transform-style
> transform-style指定嵌套元素如何在3D空间中呈现。

```js
transform-style: flat | preserve-3d
```

`flat`是默认值，表示所有子元素在2D平面呈现；`preserve-3d`表示所有子元素在3D空间中呈现。

因此，我们想要实现一些3D效果的时候，`transform-style: preserve-3d`是少不了的。一般而言，该声明应用在3D变换的兄弟元素们的父元素上，我们可以叫它`容器`。

# transform-origin
> transform-origin用来改变元素的原点位置。

它的取值方式有很多种，下面我们通过实例（把背景为黄色的div顺时针旋转45deg）来介绍一下它的常用取值方式：
- `transform-origin:center`（默认值，等价于：center center/ 50% 50%）

    ![](http://7xtawy.com1.z0.glb.clouddn.com/center.png)

- `transform-origin:top`（等价于：top center/center top）

    ![](http://7xtawy.com1.z0.glb.clouddn.com/1.png)

- `transform-origin:bottom`（等价于：bottom center/center bottom）

    ![](http://7xtawy.com1.z0.glb.clouddn.com/bottom.png)

- `transform-origin:right`（等价于：right center/center right）

    ![](http://7xtawy.com1.z0.glb.clouddn.com/right.png)

- `transform-origin:left`（等价于：left center/center left）

    ![](http://7xtawy.com1.z0.glb.clouddn.com/left.png)

- `transform-origin:top left`（等价于：left top）

    ![](http://7xtawy.com1.z0.glb.clouddn.com/top-left.png)

- 同理，还可以设置为：`transform-origin:top right（右上角为原点）`、`transform-origin:bottom right（右下角为原点）`、`transform-origin:bottom left（左下角为原点）`

# 绘制立方体效果
各位看官等不及了吧，讲了那么多'废话'，本篇文章的`重头戏`终于来了！

## dom结构

```js
<div class="rect-wrap">   //舞台元素，设置perspective，让其子元素获得透视效果。
    <div class="container">    //容器，设置transform-style: preserve-3d，让其子元素在3D空间呈现
        <div class="top slide"></div>   //立方体的六个面
        <div class="bottom slide"></div>
        <div class="left slide"></div>
        <div class="right slide"></div>
        <div class="front slide"></div>
        <div class="back slide"></div>
    </div>
</div>
```

## css代码
- 对于舞台元素

  ```js
    .rect-wrap {
        position: relative;
        perspective: 1600px;
    }
  ```

- 对于容器

  ```js
    .container {
        width: 800px;
        height: 800px;
        transform-style: preserve-3d;
        transform-origin: 50% 50% 200px; //设置3d空间的原点在平面中心再向Z轴移动200px的位置
    }
  ```

- 立方体的每个面

  ```js
    .slide {
        width: 400px;
        height: 400px;
        position: absolute;  //定位
    }
  ```

- 立方体顶面

  ```js
    .top {
        left: 200px;
        top: -200px;
        transform: rotateX(-90deg);
        transform-origin: bottom;
    }
  ```

- 立方体底面

  ```js
    .bottom {
        left: 200px;
        bottom: -200px;
        transform: rotateX(90deg);
        transform-origin: top;
    }
  ```

- 立方体左面

  ```js
    .left {
        left: -200px;
        top: 200px;
        transform: rotateY(90deg);
        transform-origin: right;
    }
  ```

- 立方体右面

  ```js
    .right {
        left: 600px;
        top: 200px;
        transform: rotateY(-90deg);
        transform-origin: left;
    }
  ```

- 立方体前面

  ```js
    .front {
        left: 200px;
        top: 200px;
        transform: translateZ(400px);  //立方体前面正对着屏幕，所以不用旋转，只需向Z轴前移动距离
    }
  ```

- 立方体后面

  ```js
    .back {
        left: 200px;
        top: 200px;
        transform: translateZ(0);   //立方体后面正对着屏幕，所以不用旋转，只需向Z轴后移动距离
    }
  ```

    最后别忘了给每个不同面加上图片，位置什么的再稍微调一下，这个酷炫的立方体就大功告成了。

## 加动画
最后我们还想要这个立方体自己动起来，我定义了一个动画，看官们可以试试。

```js
@keyframes rotate-frame {
    0% {
        transform: rotateX(0deg) rotateY(0deg);
    }
    10% {
        transform: rotateX(0deg) rotateY(180deg);
    }
    20% {
        transform: rotateX(-180deg) rotateY(180deg);
    }
    30% {
        transform: rotateX(-360deg) rotateY(180deg);
    }
    40% {
        transform: rotateX(-360deg) rotateY(360deg);
    }
    50% {
        transform: rotateX(-180deg) rotateY(360deg);
    }
    60% {
        transform: rotateX(90deg) rotateY(180deg);
    }
    70% {
        transform: rotateX(0) rotateY(180deg);
    }
    80% {
        transform: rotateX(90deg) rotateY(90deg);
    }
    90% {
        transform: rotateX(90deg) rotateY(0);
    }
    100% {
        transform: rotateX(0) rotateY(0);
    }
}
```

最后把这个动画用到这个立方体的容器元素上，就OK了：

```js
.container{
    animation: rotate-frame 30s linear infinite;
}
```

# 总结
总而言之，在我学习CSS3的过程中，见到了不少的新特性，也学会了如何使用，但是我想说的是我们不管要学会怎么使用，更要去理解每一行代码为什么产生对应的效果，特别是对于`3D transform`，我们要从根本去理解了`3D空间`，才能更好的去掌握它的每一个属性值能够带来的效果。

这篇文章内容稍微有点多，自己记录这篇文章的同时又对这个效果学习巩固了一篇，还是蛮开心哒。同时也希望能对各位看官在以后学习`3D transform`的道路上起到一点点作用！



--------------------------------------------------------------------------------
