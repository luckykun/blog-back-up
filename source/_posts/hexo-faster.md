---
title: hexo博客进阶－性能优化
date: 2016-07-10T17:47:20.000Z
categories: 工作
tags:
  - hexo系列
  - 教程
toc: true
---

--------------------------------------------------------------------------------

刚开始搭建博客的时候觉得很好玩，可是玩的久了，问题慢慢就出来了，就跟谈恋爱一样＝＝。比如现在我访问博客的时候就感觉慢的要死，不可否认，使用hexo搭建服务器方便快捷，但是由于github作为服务器，也会有速度稍慢的代价产生。既然我不能放弃github，那就只有另寻出路了，作为一名前端，有哪些地方我们可以自己来优化呢？

先来说说我们在面试中经常遇到的一个问题，`在浏览器输入url到页面打开，都做了些什么？`

<!--more-->

- 浏览器里输入网址
- 浏览器查找域名对应的IP地址
    - 这一步包括DNS具体的查找过程，包括：浏览器缓存->系统缓存->路由器缓存...
- 浏览器向web服务器发送一个HTTP请求
- 服务器的永久重定向响应（从`http://example.com` 到 `http://www.example.com`）
   - 关于为什么要重定向。其中一个原因跟`搜索引擎`排名有关。如果一个页面有两个地址，就像`http://example.com/`和`http://www.example.com/`，搜索引擎会认为它们是两个网站，结果造成每一个的搜索链接都减少从而降低排名。所以要把带www的和不带www的地址归到同一个网站排名下。还有一个原因是用不同的地址会造成`缓存友好性变差`。
- 浏览器跟踪重定向地址，发起GET请求
- 服务器"处理"请求，向浏览器发回一个HTML响应
- 浏览器解析显示HTML
- 浏览器发送请求获取嵌入在 HTML 中的资源（如图片、音频、视频、CSS、JS等等）
- 浏览器发送异步请求（ajax请求等）

# 分析
从上面的过程可以看出，其实大部分过程我们是控制不了的，我们只能从浏览器端入手来找一些可以做的事情。那么，我们可以做些什么呢？
- 少发送请求
   - 把要加载的js文件（css文件同理）合并成一个（尽量少）文件，则可以向服务器少发送请求，从而减少等待时间。
- 压缩文件
   - 使用压缩之后的js、css、img文件，同样可以减少请求时间。
- Css Sprite
   - 这是css的一项技术，将图片尽可能多的合并成一个图片文件，第一次使用的时候加载这张图片，然后浏览器会缓存下来，其他地方再使用的时候就不需要重新请求了。
- js／css位置
   - css引用建议放在head标签里面；js脚本建议放到body内容的最后，原因：等待js加载或者脚本有错误的时候不会影响html页面的展示。
- 待发现...^_^

# 博客优化
## 优化之前
优化之前，大家可以利用百度统计这个平台（界面太丑，不过功能还行＝＝）给自己的网站做一些评测，比如可以测速，并给出优化建议，下面是我测试的结果：

![](http://7xtawy.com1.z0.glb.clouddn.com/111.png)

他还会告诉我们导致网站访问速度慢的原因，以便我们对症下药，如下图：

![](http://7xtawy.com1.z0.glb.clouddn.com/2.png)

## 使用gulp优化
从上图可以看出，慢的不要不要的，不优化根本不能接受，所以，我尝试着做了一些优化，主要是利用gulp和它的一些插件来压缩js、css、img等文件，下面是我添加的gulpfile.js文件:

```js
var gulp = require('gulp'),
    uglify = require('gulp-uglify'),
    rename = require('gulp-rename'),
    cssmin = require('gulp-minify-css'),
    imagemin = require('gulp-imagemin');
//JS压缩
gulp.task('uglify', function() {
    return gulp.src('././public/js/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('././public/js/'));
});
//public-fancybox-js压缩
gulp.task('fancybox:js', function() {
    return gulp.src('././public/fancybox/jquery.fancybox.js')
        .pipe(uglify())
        .pipe(gulp.dest('././public/fancybox/'));
});
//public-fancybox-css压缩
gulp.task('fancybox:css', function() {
    return gulp.src('././public/fancybox/jquery.fancybox.css')
        .pipe(cssmin())
        .pipe(gulp.dest('././public/fancybox/'));
});
//CSS压缩
gulp.task('cssmin', function() {
    return gulp.src('././public/css/style.css')
        .pipe(cssmin())
        .pipe(gulp.dest('././public/css/'));
});
//图片压缩
gulp.task('images', function() {
    gulp.src('././public/img/*.*')
        .pipe(imagemin({
            progressive: false
        }))
        .pipe(gulp.dest('././public/img/'));
});
gulp.task('build', ['uglify', 'cssmin', 'images', 'fancybox:js', 'fancybox:css']);
```

# 博客优化
优化之后，然后每次添加文章之后，编译发布之间需要多一个命令来压缩这些文件，总结了一下，详细如下：

```js
hexo clean      //清除public文件夹
hexo g       //编译文章，生成public文件夹
gulp build     //压缩js、css、img文件
hexo d      //部署到github
```

最后再去测试一下网站的访问速度，可以看出比之前已经快了不少了，截图如下：
![](https://img.alicdn.com/tps/TB1iMayKVXXXXX.XXXXXXXXXXXX-795-86.png)


--------------------------------------------------------------------------------
