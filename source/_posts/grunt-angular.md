---
title: 前后端分离之前端项目构建（grunt+require+angular）
date: 2016-05-02 22:33:32
categories: 工作
tags: [grunt, angular, 构建, 教程]
toc: true
---

---

前段时间在公司做了一个项目，前后端的合作方式是这样的：前端开发页面，然后把代码给到后端同学，后端同学通过java的vm模板来渲染页面。慢慢的我发现，这种方式简直是太low了，因为所有的前端代码都揉在服务端同学那里，而每次项目需要更新，甚至只是页面微调，只要服务端同学说他不懂，或者是没有时间，我们前端同学就要屁颠屁颠的跑去他那里配合他修改代码，维护成本高，卖力不讨好＝＝

可能有人会说了，使用svn或者git这种版本控制工具呀，大家down一套代码，在一起开发不就好了。我想说的是，代码虽然有了，但是对于服务端同学的那一套复杂的环境，有兴趣或者涉猎还好，如果没有，自己去启个java环境估计都得花上半天时间吧。

此时你会深深的感受到了那句名言的魅力，`时间就是金钱！`后来我发现，现在的web开发，谁还用这种低级的方法，大家都已经开始搞`前后端分离了`！


<!--more-->


# 前后端分离的目的和作用


>要弄清前后端分离的目的和作用，首先要知道什么是前后端分离。

现在的web前端越来越偏向于独立的技术种类，在不久的将来，服务端的活都会被我们给承包了。我曾经在某个网站读到这样一句话，假如有一个大型网站，例如淘宝网，它肯定不止是一个web项目，而是多个web项目的集合，那么如果前端不作整合、封装，那么不同的项目开发必然会有大量的重复劳动。从这句话和我在前言中举的例子中可以看出，前端开发单独封装组件，单独开发项目，单独维护，前端代码不和服务端逻辑揉在一起，这就是我理解的前后端分离。唯一需要和服务端交互的，就是通过ajax去请求他们提供的接口。

所以，从另一个角度看，我们在开发的时候，只要和服务端约定好接口格式，从项目开工到结束，我们都不需要和服务端开发打交道，这无疑提高了项目质量和开发效率。前后端分离的终极目标应该是前端和服务端是完全独立的项目，一个项目开始之后，前端开发前端的，服务端开发服务端的，并且最后还需要独立部署，这样才真正实现了前后端解耦分离，前后端的沟通主要集中在数据接口的格式上。


# 前端项目构建

现在要做到前端项目独立，这时候为了项目便于管理维护，我们就需要项目化，工程化，开发规范，自动化压缩混淆，自动化发布，前端优化等等。

现在前端框架这么丰富，一个项目要引入哪些框架，就因人而异了，工作以来一直在学习angular，所以最近自己尝试着搭了一套基于grunt+requireJs+angularJs的应用。

- grunt: 操作项目文件：比如文件转换、压缩、打包部署等等。

- requireJs: js库加载管理，支持按需加载，模块化引入。

- angularJs: js前端MVC框架，支持依赖注入、双向绑定等主要特性。


这几个库是现在中大型前端项目比较适合的搭配，很有必要去学习并掌握他们。



## 创建项目目录


```js
mkdir myProject
cd myProject
```

创建项目文件夹，然后进入文件夹，一下操作均在此文件夹下执行。


## 创建package.json

`首先我们需要为npm提供一个package.json，告诉它我们的项目信息，特别是项目中将会使用的插件。`

可以用命令生成，后续也能够手动修改。

```js
npm init
```

他会问我们一些问题，一路使用默认值，创建完毕。


## 安装grunt

```js
npm install grunt --save-dev
```

使用npm安装grunt插件，它将被安装到根目录的`node_modules`文件夹下，所有npm插件都会放到这里。

`--save-dev`： 意思是安装插件的同时，也把它添加到项目信息文件`package.json`中的`devDependencies`字段里，意思是这个项目依赖于这些插件。下次使用`npm install`的时候就会自动安装这些插件。


## 为grunt创建配置文件Gruntfile.js

Gruntfile.js文件用于定义任务、任务组。它可以用来执行文件的类型转换、压缩、合并等等操作，为开发大大提高了效率。

- 安装grunt-init

	```js
	npm install grunt-init -g      //全局安装grunt-init
	```

- 下载grunt模板

	```js
	git clone https://github.com/gruntjs/grunt-init-gruntfile.git ~/.grunt-init/gruntfile
	```

- 生成Gruntfile

	```js
	grunt-init gruntfile  
	```

	在项目根目录下生成Gruntfile.js文件，跟package.json文件一下，按需回答一些文件，就创建好了，后续能手动修改（其实大部分都是自己手写的）。


## 安装bower

bower是用来管理js库的一个工具，比如下载jquery、angularjs等库。并且下载的时候还能指定库的版本。

同样适用npm进行安装。

```js
npm install bower -g
```

## 为bower生成配置文件bower.json

整个项目的信息文件是`package.json`，执行任务插件grunt的信息文件是`Gruntfile.js`，那么bower当然也有自己的信息文件了，那就是`bower.json`。

```js
bower init
```

不过我觉得bower.json基本没有什么作用，它最大的作用就是用来下载我们需要的各种技术库。

比如使用bower下载angularjs：

```js
bower install angularjs
```

这行命令将会把angular下载下来，放在根目录下的`bower_components`文件夹下，不过它默认下载的是angular的最新版，有时候，我们可能不需要最新版。假如，我想下载angularjs 1.2.2版本该怎么办呢？

```js
bower install angularjs#1.2.2
```

另外，bower只负责下载文件到`bower_components`目录下，但是我们项目中可能并不想把库放在这里，所以我们可以选择使用grunt的插件`grunt-bower-task`，在Gruntfile.js中定义一个任务来移动文件到想要的目录下。


## 关于Requirejs

官网上是这样说的：

> RequireJS的目标是鼓励代码的模块化。

它使用了不同于传统的脚本加载步骤。可以用它来加速、优化代码，但其主要目的还是为了代码的模块化，按需加载。



## 使用Requirejs


`<script data-main="scripts/main" src="scripts/require.js"></script>`

一般在首页加载requiejs文件，然后属性`data-main`指定的文件就是接下来要加载的文件，然后我们再看`main.js`文件:


```js
require.config({
	baseUrl: 'script/lib',
	paths: {
		app: 'app',
		jquery: '/jquery/jquery-min',
		angular: '/angularjs/angular-min'
	},
	shim: {
		'angular': {
            exports: 'angular'
        }
	}
});
require([
	'app'
], function(app) (
	app.hello();
))
```


- 我们在`paths`中声明了3个模块，app、jquery和angular，后面的路径是模块对应的文件路径。

- `shim`中用来处理一些没有遵守requirejs规范的js库，比如angularjs库，所以要手动配置一个叫`angular`的模块。

- 最后用require来导入我们自己的模块，可在后面的callback中拿到对应模块的实例，并对它进行一些操作，比如我们调用了`app.hello()`方法。


# AngularJs实例

上面的步骤只是简单说了下大体步骤，纸上谈兵之后，就可以开始实际操作了。


## 目录结构

项目文件夹结构如下图（当然目录结构因人而异）：

![](https://img.alicdn.com/tps/TB1bzUGJXXXXXcOXpXXXXXXXXXX-251-584.png)

- `node_modules`: 用来存放项目依赖的grunt插件。

- `bower_components`: 用来存放bower下载的库。

- `build`: 用来存放经过build处理之后的js文件。

- `app`: 是真正的开发需要的文件。

	- `data`: 用来存放mock数据用的json文件。

	- `images`: 用来存放静态图片。

	- `scripts`: 用来存放所有js文件。

		- `controller`: 用来存放控制器文件。

		- `directive`: 定义的指令文件。

		- `filter`: 定义的过滤器文件。

		- `route`: 路由文件。

		- `service`: 服务文件。

		- `vendor`: 公共库文件，比如angular,jquery,bootstrap等等。

	- `styles`目录用来存放样式文件。

	- `views`目录用来存放`页面`html文件。

	- `partials`目录用来存放`页面片段`的html文件。


目录创建好了，现在来写一些代码，让项目能够运行起来。

## views/index.html


```html
<html>
<head>
    <meta charset="UTF-8">
    <title>grunt+requirejs+angular项目构建</title>
    <script src="../scripts/vendor/requirejs/require.js" data-main="../scripts/config"></script>    <!-- 先引入requirejs, 然后引入config.js -->
</head>
<body>
    <div ng-controller="testController">
        {{app}}
    </div>
</body>
</html>
```

上述index中，先引入了require.js，然后加载data-main对应的config.js文件。


## scripts/config.js

```js
var vendorPath = 'vendor/';
require.config({    //配置模块
    baseUrl: '../scripts/',
    paths: {
        'app': 'app',
        'angular': vendorPath + 'angular/angular',
        'jquery': vendorPath + 'jquery/jquery'
    },
    shim: {   //处理没有遵守requirejs规范的js库
        'angular': {
            exports: 'angular'
        }
    }
});
require(['./bootstrap'], function(bootstrap) {
    // ...
});
```

然后通过require引入了bootstrap.js文件。


## scripts/bootstrap.js

```js
define([
    'angular',
    'app',
    'jquery',
    './controller/_base'
], function(angular) {
    // 手动将angular模块绑定到document对象
    angular.element(document).ready(function() {
        angular.bootstrap(document, ['myProject']);
    });
});
```

`注意：function的内容是requirejs引入所有文件之后的回调函数。`

bootstrap是angular对象的一个方法，用于手动启动。上述代码除了引入了angular等js库外，还引入了app.js文件。


## scripts/app.js

```js
define([
    'angular',
    './controller/controllers'
], function(angular) {
    //定义将要绑定到document上的模块名称
    return angular.module('myProject', ['projectController']);
});
```

定义模块名`myProject`的时候，同时指定它依赖于另一个模块`projectController`。

同时在`./controller/controllers.js`中定义这个模块：

```js
define(['angular'], function(angular) {
    return angular.module('projectController', []);
});
```

这里是给所有的控制器定义了一个单独的模块，然后让主要模块依赖于这个模块，这样做的目的是方便管理维护，我们还可以给所有的服务、路由、过滤器都添加一个单独的模块，然后在app.js中添加注入即可。


## controller/testController.js

如果想要让视图文件`views/index.html`能够看到效果，我们就要开始编写控制器文件了，

```js
define([
    './controllers'
], function(mod) {
    var controllerFn = function($scope, $location) {
        $scope.app = 'hello, world';
    };
    controllerFn.$inject = ['$scope', '$location'];
    mod.controller('testController', controllerFn);
});
```

引入控制器的单独模块文件'./controllers',然后在模块上新建一个名为`testController`的控制器，并在$scope上绑定了一个值为‘hello, world’的变量`app`。

## 查看效果

使用`python -m SimpleHTTPServer`启动一个简单的web服务器，默认监听8000端口。在浏览器输入正确地址就能看到效果了。


```js
hello, world
```


# 结语


现在，我们需要做的就是，和服务端约定好接口格式，自己根据格式mock需要的数据，然后，我们可以随心所欲的开发我们的项目了，不用再和人打交道了。开发完毕之后，再通过grunt压缩、合并、打包文件等操作，暴露给外部的只需一个js文件，另外还可以通过git等版本管理工具来迭代项目。有了这种开发方式，你再也不用担心服务端开发随时来烦你，自己也有更多时间来钻研前端本身的技术了，哈哈。
