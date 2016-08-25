---
title: 两种文件上传的实现－Ajax上传和form+iframe
date: 2016-08-16T19:41:57.000Z
categories: 工作
tags:
  - 文件上传
  - javascript
  - ajax
toc: true
---

--------------------------------------------------------------------------------

# 前言
话说现在很多很多项目需要用到文件上传，自从有了HTML5之后，上传就变的超级简单了。HTML5支持多图片上传，而且支持ajax上传！而且支持上传之前图片的预览！而且支持图片拖拽上传！而且还是纯粹利用file控件实现！而且代码还超级简单！！！原谅我这个没见过世面的人这么激动==，但是说真的，有这么多优点，想不让人称赞都难啊！

<!--more-->

# HTML5 Ajax上传
html5的上传实现，是需要file控件以及XMLHttpRequest请求。下面是我使用原生js封装的一个上传插件：

```js
    function fileUpload(options) {
        var opts = options || {};
        var func = function() {};
        this.fileInput = opts.fileInput || null;
        this.url = opts.url || '';
        this.fileList = [];
        this.onFilter = opts.onFilter || function(f) {return f;};        //选择文件组的过滤方法
        this.onSelect = opts.onSelect || func;            //文件选择后
        this.onProgress = opts.onProgress || func;        //文件上传进度
        this.onSuccess = opts.onSuccess || func;        //文件上传成功时
        this.onFailure = opts.onFailure || func;        //文件上传失败时;
        this.onComplete = opts.onComplete || func;        //文件全部上传完毕时
        this.init();
    }
    fileUpload.prototype = {
        dealFiles: function(e) {     //获取要上传的文件数组（用户选择文件后执行）
            var files = e.target.files || e.dataTransfer.files;
            this.fileList = this.onFilter(files);
            for(var i = 0, file; file = this.fileList[i]; i++){   //增加唯一索引值
                file.index = i;
            }
            this.onSelect(this.fileList);
            return this;
        },
        removeFile: function(fileDelete) {     //删除某一个文件
            var arrFile = [];
            for(var i = 0, file; file = this.fileList[i]; i++){
                if (file != fileDelete) {
                    arrFile.push(file);
                }
            }
            this.fileList = arrFile;
            return this;
        },
        removeAll: function() {     //清空文件队列
            this.fileList = [];
            return this;
        },
        uploadFile: function() {     //上传文件
            var me = this;
            for(var i = 0, file; file = this.fileList[i]; i++){
                (function(file) {
                    var formData = new FormData();
                    var xhr = new XMLHttpRequest();
                    if (xhr.upload) {
                        xhr.upload.addEventListener("progress", function(e) {   // 上传中
                            me.onProgress(file, e.loaded, e.total);
                        }, false);
                        xhr.onreadystatechange = function(e) {      // 文件上传成功或是失败
                            if (xhr.readyState == 4) {
                                if (xhr.status == 200) {
                                    me.onSuccess(file, xhr.responseText);
                                    me.removeFile(file);
                                    if (!me.fileList.length) {
                                        me.onComplete();   //上传全部完毕。执行回调
                                    }
                                } else {
                                    me.onFailure(file, xhr.responseText);
                                }
                            }
                        };
                        // 开始上传
                        formData.append('file', file);
                        xhr.open("POST", me.url, true);
                        xhr.send(formData);
                    }
                })(file);
            }
        },
        init: function() {
            var me = this;
            //文件选择控件选择
            if (me.fileInput) {
                me.fileInput.addEventListener("change", function(e) { me.dealFiles(e); }, false);
            }
        }
    };
```

相信大家也看到了，代码中出现了`FormData`,这就是html5的神奇之处了。借助FormData轻松实现异步无刷新支持预览图片的多文件上传功能。而且，令人欣慰的是，现在已经有很多浏览器都已经支持HTML5了。

但是！！！IE9以下的版本不支持呀！！

除此之外，上面的方法还有一个弊端，因为使用了ajax的上传方式，所以不能支持跨域上传，如果必须要满足这两个业务场景，那就试试下面的方法吧，借助form和iframe来实现上传。下面来详细看一下：

# form表单提交到iframe
html代码:

```js
    <iframe name="demoIframe" style="display:none"></iframe>
    <form target="demoIframe" action="upload.php" method="post" enctype="multipart/form-data">
        <input class="filename" type="file" name="fileLabel">
        <input type="submit" value="提交">
    </form>
```

我们点击提交,可以看到下面的请求：

![](https://img.alicdn.com/tps/TB1DHQaMpXXXXcaXVXXXXXXXXXX-794-199.png)

已经把文件上传。那么，加入这个upload.php接口可用，而且假如上传成功后，会返回：

```js
{
    "code": "200",
    "success": true,
    "data": {
        ...
    }
}
```

我们要怎么去获取返回值，从而进行下一步的操作呢？因为我们是上传到了iframe中，所以我们只需要监听iframe的load事件，如果有返回值了，我们就能获取到，从而进行进一步处理。看js代码：

```js
$('iframe').on('load', function() {
    var responseText = $('iframe')[0].contentDocument.body.textContent;
    var responseData = JSON.parse(responseText) || {};
    if (responseData.isSuccess == true || responseData.code == 200) {
        //success
    } else {
        //error   
    }
});
```

这样我们就完成了一个支持所有浏览器的上传文件操作了。还是很简单的吧^_^

# 结语

以上两种方法就可以上实现所有场景下的文件上传需求了。第一种方法便于理解，代码简单，便于理解，但是IE9及以上才支持；第二种方法是很原始的方法，支持所有浏览器，但是代码稍显复杂，看官们可以根据自己的业务需求来选择不同的方案。本文结束，谢谢大家！


--------
