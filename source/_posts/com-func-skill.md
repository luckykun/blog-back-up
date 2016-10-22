---
title: 总结js常用函数和常用技巧（持续更新）
date: 2016-10-11T23:16:27.000Z
categories: 工作
tags:
  - javascript
  - 干货总结
toc: true
---

--------------------------------------------------------------------------------

学习和工作的过程中总结的干货，包括常用函数、常用js技巧、常用正则表达式、git笔记等。为刚接触前端的童鞋们提供一个简单的查询的途径，也以此来缅怀我的前端学习之路。
PS：此文档，我会持续更新。
<!--more-->

# Ajax请求
## jquery ajax函数
我自己封装了一个ajax的函数，代码如下：

```js
var Ajax = function(url, type success, error) {
    $.ajax({
        url: url,
        type: type,
        dataType: 'json',
        timeout: 10000,
        success: function(d) {
            var data = d.data;
            success && success(data);
        },
        error: function(e) {
            error && error(e);
        }
    });
};
// 使用方法：
Ajax('/data.json', 'get', function(data) {
    console.log(data);
});
```

## jsonp方式
有时候我们为了跨域，要使用jsonp的方法，我也封装了一个函数：

```js
function jsonp(config) {
    var options = config || {};   // 需要配置url, success, time, fail四个属性
    var callbackName = ('jsonp_' + Math.random()).replace(".", "");
    var oHead = document.getElementsByTagName('head')[0];
    var oScript = document.createElement('script');
    oHead.appendChild(oScript);
    window[callbackName] = function(json) {  //创建jsonp回调函数
        oHead.removeChild(oScript);
        clearTimeout(oScript.timer);
        window[callbackName] = null;
        options.success && options.success(json);   //先删除script标签，实际上执行的是success函数
    };
    oScript.src = options.url + '?' + callbackName;    //发送请求
    if (options.time) {  //设置超时处理
        oScript.timer = setTimeout(function () {
            window[callbackName] = null;
            oHead.removeChild(oScript);
            options.fail && options.fail({ message: "超时" });
        }, options.time);
    }
};
// 使用方法：
jsonp({
    url: '/b.com/b.json',
    success: function(d){
        //数据处理
    },
    time: 5000,
    fail: function(){
        //错误处理
    }       
});
```

# 常用正则验证表达式
## 手机号验证

```js
var validate = function(num) {
    var exp = /^1[3-9]\d{9}$/;
    return exp.test(num);
};
```

## 身份证号验证

```js
var exp = /^[1-9]{1}[0-9]{14}$|^[1-9]{1}[0-9]{16}([0-9]|[xX])$/;
```

## ip验证

```js
var exp = /^(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])$/;
```

# 常用js函数
## 返回顶部

```js
$(window).scroll(function() {
    var a = $(window).scrollTop();
    if(a > 100) {
        $('.go-top').fadeIn();
    }else {
        $('.go-top').fadeOut();
    }
});
$(".go-top").click(function(){
    $("html,body").animate({scrollTop:"0px"},'600');
});
```

## 阻止冒泡

```js
function stopBubble(e){
    e = e || window.event;  
    if(e.stopPropagation){
        e.stopPropagation();  //W3C阻止冒泡方法  
    }else {  
        e.cancelBubble = true; //IE阻止冒泡方法  
    }  
}
```

## 全部替换replaceAll

```js
var replaceAll = function(bigStr, str1, str2) {  //把bigStr中的所有str1替换为str2
    var reg = new RegExp(str1, 'gm');
    return bigStr.replace(reg, str2);
}
```

## 获取浏览器url中的参数值

```js
var getURLParam = function(name) {
    return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)', "ig").exec(location.search) || [, ""])[1].replace(/\+/g, '%20')) || null;
};
```

## 深度拷贝对象

```js
function cloneObj(obj) {
    var o = obj.constructor == Object ? new obj.constructor() : new obj.constructor(obj.valueOf());
    for(var key in obj){
        if(o[key] != obj[key] ){
            if(typeof(obj[key]) == 'object' ){
                o[key] = mods.cloneObj(obj[key]);
            }else{
                o[key] = obj[key];
            }
        }
    }
    return o;
}
```

## 数组去重

```js
var unique = function(arr) {
    var result = [], json = {};
    for (var i = 0, len = arr.length; i < len; i++){
        if (!json[arr[i]]) {
            json[arr[i]] = 1;
            result.push(arr[i]);  //返回没被删除的元素
        }
    }
    return result;
};
```

## 判断数组元素是否重复

```js
var isRepeat = function(arr) {  //arr是否有重复元素
    var hash = {};
    for (var i in arr) {
        if (hash[arr[i]]) return true;
        hash[arr[i]] = true;
    }
    return false;
};
```

## 生成随机数

```js
function randombetween(min, max){
    return min + (Math.random() * (max-min +1));
}
```

## 操作cookie

```js
own.setCookie = function(cname, cvalue, exdays){
    var d = new Date();
    d.setTime(d.getTime() + (exdays*24*60*60*1000));
    var expires = 'expires='+d.toUTCString();
    document.cookie = cname + '=' + cvalue + '; ' + expires;
};
own.getCookie = function(cname) {
    var name = cname + '=';
    var ca = document.cookie.split(';');
    for(var i=0; i< ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') c = c.substring(1);
        if (c.indexOf(name) != -1) return c.substring(name.length, c.length);
    }
    return '';
};
```

# 知识技巧总结
## 数据类型
underfined、null、0、false、NaN、空字符串。他们的逻辑非结果均为true。

## 闭包格式
好处：避免命名冲突（全局变量污染）。

```js
(function(a, b) {
    console.log(a+b);  //30
})(10, 20);
```

## 截取和清空数组

```js
var arr = [12, 222, 44, 88];
arr.length = 2;   //截取，arr = [12, 222];  
arr.length = 0;   //清空，arr will be equal to [].
```

## 获取数组的最大最小值

```js
var numbers = [5, 45822, 120, -215];
var maxInNumbers = Math.max.apply(Math, numbers);   //45822
var minInNumbers = Math.min.apply(Math, numbers);   //-215
```

## 浮点数计算问题

```js
0.1 + 0.2 == 0.3   //false
```

为什么呢？因为0.1+0.2等于0.30000000000000004。JavaScript的数字都遵循IEEE 754标准构建，在内部都是64位浮点小数表示。可以通过使用`toFixed()`来解决这个问题。

## 数组排序sort函数

```js
var arr = [1, 5, 6, 3];    //数字数组
arr.sort(function(a, b) {
    return a - b;   //从小到大排
    return b - a;   //从大到小排
    return Math.random() - 0.5;   //数组洗牌
});
```

```js
var arr = [{   //对象数组
    num: 1,
    text: 'num1'
}, {
    num: 5,
    text: 'num2'
}, {
    num: 6,
    text: 'num3'
}, {
    num: 3,
    text: 'num4'
}];   
arr.sort(function(a, b) {
    return a.num - b.num;   //从小到大排
    return b.num - a.num;   //从大到小排
});
```

## 对象和字符串的转换

```js
var obj = {a: 'aaa', b: 'bbb'};
var objStr = JSON.stringify(obj);    // "{"a":"aaa","b":"bbb"}"
var newObj = JSON.parse(objStr);     // {a: "aaa", b: "bbb"}
```

# git笔记
## git使用之前的配置

```js
1.git config --global user.email xxx@163.com
2.git config --global user.name xxx
3.ssh-keygen -t rsa -C xxx@163.com(邮箱地址)      // 生成ssh
4.找到.ssh文件夹打开，使用cat id_rsa.pub    //打开公钥ssh串
5.登陆github，settings － SSH keys  － add ssh keys （把上面的内容全部添加进去即可）
```

`说明：然后这个邮箱（xxxxx@gmail.com）对应的账号在github上就有权限对仓库进行操作了。可以尽情的进行下面的git命令了。`

## git常用命令

```js
1、git config user.name  /  user.email     //查看当前git的用户名称、邮箱
2、git clone https://github.com/jarson7426/javascript.git  project  //clone仓库到本地。
3、修改本地代码，提交到分支:  git add file   /   git commit -m “新增文件”
4、把本地库推送到远程库:  git push origin master
5、查看提交日志：git log -5
6、返回某一个版本：git reset --hard 123
7、分支：git branch / git checkout name  / git checkout -b dev
8、合并name分支到当前分支：git merge name   /   git pull origin
9、删除本地分支：git branch -D name
10、删除远程分支： git push origin  :daily/x.x.x
11、git checkout -b mydev origin/daily/1.0.0    //把远程daily分支映射到本地mydev分支进行开发
12、合并远程分支到当前分支 git pull origin daily/1.1.1
13、发布到线上：
    git tag publish/0.1.5
    git push origin publish/0.1.5:publish/0.1.5
14、线上代码覆盖到本地：
    git checkout --theirs build/scripts/ddos
    git checkout --theirs src/app/ddos
```

# 结语

希望本文总结的内容能给各位看官带来焕然一新的感觉。另外，如果你们有什么值得推荐的js技巧，欢迎在评论中补充，我可以收纳在本文中。

PS：此文档会持续新增内容。

--------------------------------------------------------------------------------
