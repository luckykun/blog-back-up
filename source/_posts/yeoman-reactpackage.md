---
title: 开发自己的yeoman脚手架（generator-reactpackage）
date: 2016-09-01T11:55:33.000Z
categories: 工作
tags:
  - yeoman
  - react
  - webpack
  - 构建
  - 教程
toc: true
---

--------------------------------------------------------------------------------

自从前后端开始分离之后，前端项目工程化也显得越来越重要了，之前写过一篇搭建基于`Angular+Requirejs+Grunt`的前端项目教程，有兴趣的可以[点这里去看](http://luckykun.com/work/2016-05-02/grunt-angular.html)

但是有些项目可以使用这种方式，但有些却不太适合，或者我们就是想要去尝试新的框架。比如最近我就尝试着使用了`webpack+react+es6`的方式开发项目，感觉很不错，然后很多项目都用了这种方式。所以为了不需要每次开发的时候都从头开始新建文件，就想着能不能弄个工具，使用命令能够快速的生成这样一套跑的通的项目模版，正好，有个工具叫`yeoman`。

<!--more-->

本篇文章看点：
1. 教你使用`yeoman`快速开发自己的脚手架。
2. 介绍作者编写的`generator-reactpackage`脚手架模版，此项目的开发基于`webpack+react+es6`，项目功能包含：
  - 启动本地服务，默认监听端口8888
  - css文件能自动补全css3属性的前缀
  - 包含路由功能(react-router)
  - 使用命令`npm run dev`启动服务，修改保存文件的时候浏览器会自动刷新
  - 如果不想要实时刷新的功能，将webpack.config.js文件的devServer配置改为inline: false
  - 使用`npm run build`打包文件，js和css分开打包，并且默认会压缩文件

# 开发脚手架
## 环境准备
安装或者更新一下你的node和npm

```js
npm install -g n  //首先安装n模块
n stable   //升级node.js到最新稳定版
n 5.0.0   //或者指定版本升级
node -v   //检查更新是否成功
```

然后安装yeoman

```js
npm install -g yo
```

## 创建目录
新建一个名为`generator-xxx`（yeoman脚手架命名规范）的文件夹，我这里叫`generator-reactpackage`。然后在目录下执行`npm init`创建package.json文件。修改为：

```js
{
  "name": "generator-reactpackage",
  "version": "0.0.4",
  "description": "基于ract+webpack的项目目录快速生成器",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/luckykun/generator-reactpackage.git"
  },
  "keywords": [
    "yeoman-generator"
  ],
  "author": "luckykun",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/luckykun/generator-reactpackage/issues"
  },
  "homepage": "https://github.com/luckykun/generator-reactpackage",
  "dependencies": {
    "chai": "^3.3.0",
    "chalk": "^1.1.1",
    "fs-extra": "^0.24.0",
    "mocha": "^2.3.3",
    "yeoman-generator": "^0.24.1",
    "yosay": "^1.0.5"
  }
}
```

注意：package.json的信息一定要尽可能完整，不然可能上传不到[generator-lists](http://yeoman.io/generators/)

然后在此目录下新建`generators->app->index.js`，`generators-app-templates`，如下图所示：

![](https://img.alicdn.com/tps/TB1_c8XNXXXXXcnaXXXXXXXXXXX-245-625.png)
- `generator-reactpackage`是整个npm包的项目文件夹。
- `templates目录`里面就是我们最后要用到的项目模版文件，里面的内容是一个完整的前端项目，可以自定义。
- `index.js`是开发脚手架的主要逻辑文件。

## 开始开发
然后编辑index.js文件：

```js
var path = require('path');
var chalk = require('chalk');    //不同颜色的info
var util = require('util');
var yeoman = require('yeoman-generator');
var yosay = require('yosay');    //yeoman弹出框
var path = require('path');
var Reactpackage = yeoman.Base.extend({
    info: function() {
        this.log(chalk.green(
            'I am going to build your app!'
        ));
    },
    generateBasic: function() {  //按照自己的templates目录自定义
        this.directory('src', 'src');    //拷贝目录
        this.directory('data', 'data');
        this.copy('package.json', 'package.json');   //拷贝文件
        this.copy('index.html', 'index.html');
        this.copy('README.md', 'README.md');
        this.copy('webpack.config.js', 'webpack.config.js');
    },
    generateClient: function() {
        this.sourceRoot(path.join(__dirname, 'templates'));
        this.destinationPath('./');
    },
    install: function() {      //安装依赖
        this.installDependencies({
            skipInstall: this.options['skip-install']
        });
    },
    end: function() {
        this.log(yosay(
            'Your app has been created successfully!'
        ));
    }
});
module.exports = Reactpackage;
```

上面这个文件就是主要逻辑部分了。至于具体的语法，可以参考这篇文章。[快速搭建基于yeoman快速编写脚手架工具](http://www.07net01.com/2016/01/1179066.html)

## 上传
开发完成之后，我们就可以将`generator-reactpackage`这个项目上传到npm官网。步骤如下：

```js
npm adduser  //如果没有账号，用此命令注册
npm login   //如果有账号，用此命令登陆
npm publish --access=public     //上传到npm官网
```

上传成功后会提示：

```js
+ generator-reactpackage@0.0.4
```

然后你可以访问[http://yeoman.io/generators/](http://yeoman.io/generators/)这里去搜索一下自己的包，有没有上传成功，比如搜索`reactpackage`就会出现我上传的脚手架。如下图：

![](https://img.alicdn.com/tps/TB1OtpiNXXXXXaKXVXXXXXXXXXX-1136-337.png)

注意：
- 上传到npm官网之前需要先将脚手架项目上传到github
- 脚手架项目的package.json文件一定要尽可能详细，比如git主页，readme文件链接等等

如果你能搜到自己上传的脚手架了，OK，开发基于yeoman的脚手架工具就到这里结束了，你可以随时随地使用自己的脚手架快速生成项目模板了。有兴趣的同学可以去看看我编写的[generator-reactpackage](https://github.com/luckykun/generator-reactpackage)源码，喜欢的同学顺便来个star～～哈哈，感谢～

# 使用脚手架（generator-reactpackage）
首先确保自己安装了nodejs，然后全局安装yeoman

```js
npm install -g yo
```

然后直接安装脚手架

```js
npm install -g generator-reactpackage
```

在合适的地方新建一个文件夹，在文件夹下运行：

```js
yo reactpackage
```

然后就会在此目录下生成以下目录结构：

```
├── data
│   └── test.json
├── src
│   ├── components
│   │   └── App.js
│   ├── images
│   │   └── yeoman.png
│   ├── styles
│   │   └── app.scss
│   ├── vendor
│   │   └── jquery.js
│   ├── views
│   │   └── home.html
├── node_modules
├── index.html
├── package.json
└── webpack.config.js
```

细心的同学可能已经发现，其实这里生成的内容就是我们脚手架中定义的`templates目录`下的内容。

然后使用以下命令：

```js
npm run dev    //项目开发过程使用，启动服务，实时刷新
npm run build    //开发完成之后打包文件（js、css分开打包）
```

注意：
- 本项目默认监听端口是8888，所以在浏览器输入 [http://localhost:8888](http://localhost:8888) 就能看到效果了
- 如果执行上述命令提示错误：`Error: getaddrinfo ENOTFOUND localhost`，在host文件里面添加`127.0.0.1 localhost`即可
- 监听端口和实时刷新的功能都能在`webpack.config.js`文件中修改配置
- 如果项目运行正常，会看到如下效果：

  ![](https://img.alicdn.com/tps/TB1VKFhNXXXXXXCaXXXXXXXXXXX-884-217.png)

# 结语
可以看到，定义一个自己常用的脚手架骑士挺简单的，还有更多的功能有待探索。`generator-reactpackage`是一个基于webpack+react+es6开发的项目模版，有需要用到这个模版的同学就赶快安装用起来吧。

另外，它的源码已经上传到github上，喜欢[generator-reactpackage](https://github.com/luckykun/generator-reactpackage)的同学顺便给个star，多谢～～～
