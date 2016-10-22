---
title: 总结ES6常用的新特性
date: '2016-05-010 23:11:23'
categories: 工作
tags:
  - es6
  - javascript
toc: true
---

--------------------------------------------------------------------------------

ES6是即将到来的新版本JavaScript语言的标准，他给我们带来了更"甜"的语法糖（一种语法，使得语言更容易理解和更具有可读性，也让我们编写代码更加简单快捷），如箭头函数（=>）、class等等。用一句话来说就是：

> ES6给我们提供了许多的新语法和代码特性来提高javascript的体验

<!--more-->

不过遗憾的是，现在还没有浏览器能够很好的支持es6语法，[点这里](http://kangax.github.io/compat-table/es6/)查看浏览器支持情况，所以我们在开发中还需要用babel进行转换为CommonJS这种模块化标准的语法。

因为下面我会讲到一些es6新特性的例子，如果想要运行试试效果的话，可以[点这里](http://google.github.io/traceur-compiler/demo/repl.html)去测试es6的代码。

# es6常用特性列举
然后我下面简单的介绍一些很常用的语法特性，如果想完整的了解ES6，我推荐大家[点这里](http://gank.io/post/564151c1f1df1210001c9161)

## 定义函数
我们先来看一个基本的新特性，在javascript中，定义函数需要关键字function，但是在es6中，还有更先进的写法，我们来看：

es6写法：

```js
var human = {
    breathe(name) {   //不需要function也能定义breathe函数。
        console.log(name + ' is breathing...');
    }
};
human.breathe('jarson');   //输出 ‘jarson is breathing...’
```

转成js代码：

```js
var human = {
    breathe: function(name) {
      console.log(name + 'is breathing...');
    }
};
human.breathe('jarson');
```

很神奇对不对？这样一对比，就可以看出es6的写法让人简单易懂。别着急，下面还有更神奇的。

## 创建类
我们知道，javascript不像java是面向对象编程的语言，而只可以说是基于对象编程的语言。所以在js中，我们通常都是用function和prototype来模拟`类`这个概念。

但是现在有了es6，我们可以像java那样'明目张胆'的创建一个类了：

```js
class Human {
    constructor(name) {
        this.name = name;
      }
     breathe() {
        console.log(this.name + " is breathing");
      }
}
var man = new Human("jarson");
man.breathe();    //jarson is breathing
```

上面代码转为js格式：

```js
function Human(name) {
    this.name = name;
    this.breathe = function() {
        console.log(this.name + ' is breathing');
    }
}
var man = new Human('jarson');
man.breathe();    //jarson is breathing
```

所以我们看到，我们可以像java那样语义化的去创建一个类。另外，js中的继承父类，需要用prototype来实现。那么在es6中，又有什么新的方法来实现类的继承呢？继续看：

假如我们要创建一个Man类继承上面的Human类，es6代码：

```js
class Man extends Human {
      constructor(name, sex) {
        super(name);
          this.sex = sex;
      }
      info(){
          console.log(this.name + 'is ' + this.sex);
    }
}
var xx = new Man('jarson', 'boy');
xx.breathe();   //jarson is breathing
xx.info();   //arsonis boy
```

代码很简单，不作赘述，可以使用文章里提到的在线工具去试试效果就能明白了。需要注意的是：`super()`是父类的构造函数。

## 模块
在ES6标准中，javascript原生支持module了。将不同功能的代码分别写在不同文件中，各模块只需`导出(export)`公共接口部分，然后在需要使用的地方通过模块的`导入(import)`就可以了。下面继续看例子：

### 内联导出
ES6模块里的对象可在创建它们的声明中直接导出，一个模块中可无数次使用export。

先看模块文件`app.js`：

```js
export class Human{
    constructor(name) {
        this.name = name;
    }
    breathe() {
        console.log(this.name + " is breathing");
    }
}  
export function run(){  
    console.log('i am runing');
}
function eat() {
    console.log('i am eating');
}
```

例子中的模块导出了两个对象：Human类和run函数， eat函数没有导出，则仍为此模块私有，不能被其他文件使用。

### 导出一组对象
另外，其实如果需要导出的对象很多的时候，我们可以在最后统一导出一组对象。

更改`app.js`文件：

```js
class Human{
    constructor(name) {
        this.name = name;
    }
    breathe() {
        console.log(this.name + " is breathing");
    }
}  
function run(){  
    console.log('i am runing');
}
function eat() {
    console.log('i am eating');
}
export {Human, run};
```

这样的写法功能和上面一样，而且也很明显，在最后可以清晰的看到导出了哪些对象。

### Default导出
导出时使用关键字default，可将对象标注为default对象导出。default关键字在每一个模块中只能使用一次。它既可以用于内联导出，也可以用于一组对象导出声明中。

查看导出default对象的语法：

```js
...   //创建类、函数等等
export default {  //把Human类和run函数标注为default对象导出。
    Human,  
    run  
};
```

### 无对象导入
如果模块包含一些逻辑要执行，且不会导出任何对象，此类对象也可以被导入到另一模块中，导入之后只执行逻辑。如：

```js
import './module1.js';
```

### 导入默认对象
使用Default导出方式导出对象，该对象在import声明中将直接被分配给某个引用，如下例中的"app"。

```js
import app from './module1.js';
```

上面例子中，默认`./module1.js`文件只导出了一个对象；若导出了一组对象，则应该在导入声明中一一列出这些对象，如：

```js
import {Human, run} from './app.js'
```

## let与const
在我看来，在es6新特性中，在定义变量的时候统统使用`let`来代替`var`就好了，`const`则很直观，用来定义常量，即无法被更改值的变量。

```js
for (let i=0;i<2;i++) {
    console.log(i);  //输出: 0,1
}
```

## 箭头函数
ES6中新增的箭头操作符`=>`简化了函数的书写。操作符左边为输入的参数，而右边则是进行的操作以及返回的值，这样的写法可以为我们减少大量的代码，看下面的实例：

```js
let arr = [6, 8, 10, 20, 15, 9];
arr.forEach((item, i) => console.log(item, i));
let newArr = arr.filter((item) => (item<10));
console.log(newArr); //[6, 8, 9];
```

上面的`(item, i)`就是参数，后面的`console.log(item, i)`就是回到函数要执行的操作逻辑。

上面代码转为js格式：

```js
var arr = [6, 8, 10, 20, 15, 9];
arr.forEach(function(item, i) {
    return console.log(item, i);
});
var newArr = arr.filter(function(item) {
    return (item < 10);
});
console.log(newArr);
```

## 字符串模版
ES6中允许使用反引号 ` 来创建字符串，此种方法创建的字符串里面可以包含由美元符号加花括号包裹的变量${vraible}。看一下实例就会明白了：

```js
//产生一个随机数
let num = Math.random();
//将这个数字输出到console
console.log(`your num is ${num}`);
```

## 解构
若一个函数要返回多个值，常规的做法是返回一个对象，将每个值做为这个对象的属性返回。在ES6中，利用解构这一特性，可以直接返回一个数组，然后数组中的值会自动被解析到对应接收该值的变量中。我们来看例子：

```js
function getVal() {
    return [1, 2];
}
var [x,y] = getVal(); //函数返回值的解构
console.log('x:'+x+', y:'+y);   //输出：x:1, y:2
```

## 默认参数
现在可以在定义函数的时候指定参数的默认值了，而不用像以前那样通过逻辑或操作符来达到目的了。

```js
function sayHello(name){
    var name=name||'tom';    //传统的指定默认参数的方式
    console.log('Hello '+name);
}
//运用ES6的默认参数
function sayHello2(name='tom'){  //如果没有传这个参数，才会有默认值，
    console.log(`Hello ${name}`);
}
sayHello();//输出：Hello tom
sayHello('jarson');//输出：Hello jarson
sayHello2();//输出：Hello tom
sayHello2('jarson');//输出：Hello jarson
```

注意： `sayHello2(name='tom')`这里的等号，意思是没有传这个参数，则设置默认值，而不是给参数赋值的意思。

## Proxy
Proxy可以监听对象身上发生了什么事情，并在这些事情发生后执行一些相应的操作。一下子让我们对一个对象有了很强的追踪能力，同时在数据绑定方面也很有用处。

```js
//定义被监听的目标对象
let engineer = { name: 'Joe Sixpack', salary: 50 };
//定义处理程序
let interceptor = {
      set(receiver, property, value) {
        console.log(property, 'is changed to', value);
        receiver[property] = value;
      }
};
//创建代理以进行侦听
engineer = new Proxy(engineer, interceptor);
//做一些改动来触发代理
engineer.salary = 70;//控制台输出：salary is changed to 70
```

对于处理程序，是在被监听的对象身上发生了相应事件之后，处理程序里面的方法就会被调用。

# 结语
总的来说，虽然支持es6的情况到目前还不是很乐观，但es6的新语法特性让前端和后端的差异越来越小了，这是一个新时代的开始，我们必须要了解这些新的前沿知识，才能跟上时代的步伐。


--------------------------------------------------------------------------------
