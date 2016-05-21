---
title: hexo博客进阶－相册和独立域名
date: '2016-05-20 23:11:51'
categories: 工作
tags:
  - hexo系列
  - 教程
toc: true
---

--------------------------------------------------------------------------------

之前我已经写了一篇文章详细的讲述了如何使用hexo搭建github博客。如果还没有看的可以去看看，[hexo搭建博客](/work/2016-04-23/heoll-hexo.html)

其实，根据这篇文章的过程我们就能够搭建一个专属于自己，并且非常美观的博客了。但是如果你像我一样喜欢折腾，喜欢做到极致，比如添加打赏，添加文章导航，以及文章阅读次数，这些都很简单，按照主题文件的模版一点点修改，就能实现自己想要的效果了。

这篇文章主要记录我折腾的两个功能： `相册`和`独立域名`。


<!--more-->

# 相册
首先，看看我们实现后的效果，[点这里看我的相册](http://luckykun.com/photos/)

## 分析
然后我们分析一下，想要获取相册图片，首先得拿到图片得线上地址，其次，我们可以想到，既然我们的博客内容都放在了github得服务器，那么图片又何尝不能呢？

使用hexo搭建博客的同学都知道，github上会建立一个名为`username.github.io`的仓库，而且仓库只存储了整个项目的一部分（source文件夹的内容），而且貌似这个仓库有大小限制，总之就是说不能把图片放在这个仓库里。

我们再考虑另一个问题，假如我们有一天需要在另一台电脑写博客怎么办？所以博客备份就是需要的了。那么好，我们在Github 上再建一个仓库，用来存储整个博客项目的文件，假如某一天换个环境，直接全部down下来，就能拿到所有文章的源文件了，所以正好，我们可以在根目录下新建一个photos文件夹用来存放相册需要展示的图片文件了。

ok，分析结束，敲定技术方案： 我们新建文件夹photos用来存放图片文件，然后新建一个基于nodejs的js文件用来读取所有图片再生成一个json接口文件供相册页面的js文件使用。

## 存放照片
在博客的根目录下创建一个`photos`的文件夹，里面存放你想要展示的照片文件。然后把整个项目部署到github上，才能访问到图片的线上地址。

## 生成json文件
在根目录下创建一个nodejs文件`tool.js`，内容如下：

```js
"use strict";
var fs = require("fs");
var path = "./photos/";
fs.readdir(path, function (err, files) {
    if (err) {
        return;
    }
    var arr = [];
    (function iterator(index) {
        if (index == files.length) {
            fs.writeFile("./source/photos/data.json", JSON.stringify(arr, null, "\t"));
            console.log('get img success!');
            return;
        }
        fs.stat(path + files[index], function (err, stats) {
            if (err) {
                return;
            }
            if (stats.isFile()) {
                arr.push(files[index]);
            }
            iterator(index + 1);
        })
    }(0));
});
```

运行这个文件`node tool.js`，运行之后，会在`/source/photos/`目录下创建一个data.json文件，内容如下：

```js
[
    "IMG_0011.JPG",
    "IMG_0019.JPG",
    "IMG_0020.JPG",
    "IMG_0022.JPG",
    "IMG_0045.JPG",
    "IMG_0099.JPG",
    "IMG_0106.JPG",
    "IMG_0191.JPG",
    "IMG_0306.JPG",
    "IMG_0438.JPG",
    "IMG_0451.JPG",
    "IMG_0648.JPG",
    "IMG_0670.JPG",
    "IMG_0842.JPG",
    "IMG_1078.JPG",
    "IMG_1239.JPG",
    "IMG_1429.JPG",
    "IMG_1634.JPG"
]
```

每次修改photos文件夹里的照片，都得运行一下这个js文件，以更新生成的data.json文件。

## 创建相册页面
在根目录下执行命令：

```js
hexo page photos
```

会在`source/photos`目录下生成一个index.md文件，编辑文件内容如下：

```js
<link type="text/css" href="/fancybox/jquery.fancybox.css" rel="stylesheet">
<div class="instagram">
    <section class="archives album">
        <ul class="img-box-ul"></ul>
    </section>
</div>
```

## 修改`themes/yilia/js/photo.js`：

```js
...
init: function () {
    var that = this;
    $.getJSON("/photos/data.json", function (data) {
        that.render(that.page, data);
        that.scroll(data);
    });
},
render: function (page, data) {
    var begin = (page - 1) * this.offset;
    var end = page * this.offset;
    if (begin >= data.length) return;
    var html, li = "";
    for (var i = begin; i < end && i < data.length; i++) {
        li += '<li><div class="img-box">' + '<a class="img-bg" rel="example_group" href="https://raw.githubusercontent.com/jarson7426/blog-back-up/master/photos/' + data[i] + '"></a>' + '<img lazy-src="https://raw.githubusercontent.com/jarson7426/blog-back-up/master/photos/' + data[i] + '" />' + '</li>';
    }
    $(".img-box-ul").append(li);
    $(".img-box-ul").lazyload();
    $("a[rel=example_group]").fancybox();
},
...
```

上述文件读取json文件，将图片的线上url拼接起来渲染在页面上，相册样式使用yilia主题提供。然后上传这些文件，根据自定义的路径访问url，就能看到相册了。那么，到这里就基本完成了相册的制作。

# 独立域名
到目前为止，我们的博客都是使用github分配的二级域名`luckykun.github.io`来访问的， 那么我们会想如果能自己设置个域名，是多么cool的事！要怎么办呢？那就继续往下看吧！

## 购买域名
去卖域名的网站去买一个域名，我是在[万网](https://wanwang.aliyun.com/domain/searchresult/)买的域名，如图所示： ![](http://7xtawy.com1.z0.glb.clouddn.com/dmain.png)

找到自己喜欢的域名，购买就好了。

## 域名解析
有了自己的域名之后，luckykun.github.io替换成luckykun.com，只要设置下解析即可，进入万网的云解析页面，添加如下解析： ![](http://7xtawy.com1.z0.glb.clouddn.com/domain22.png)

说明：`192.30.252.154`和`192.30.252.153`是github服务器对应的ip地址，这步一定要设置，否则访问不了。

## 添加CNAME
然后回到博客项目根目录，在`source/`下新建一个名为`CNAME`的文件，里面的内容写入`luckykun.com`即可。

然后在浏览器输入luckykun.com，即可代替之前的github.io的域名，访问到自己的博客页面了。实在是台、太cool了！

# 结语
好的，文章写道这里就结束了，感谢大家的阅读，如果文章能给大家带来帮助就最好了，如果读者还有任何疑问，欢迎留言，我会尽力解答。再见啦～～
