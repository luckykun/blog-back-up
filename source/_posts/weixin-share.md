---
title: 简单封装微信分享插件
date: 2016-05-14T10:33:46.000Z
categories: 工作
tags:
  - 微信分享
  - javascript
  - 插件
toc: true
---

--------------------------------------------------------------------------------

最近要慢慢开始做移动端的一些页面了，并且大多数都是活动页面，需要在微信里面分享、传播。

如果只是单单传播一下页面，扩散一些信息还好，那就跟pc差不多，一刀一刀切页面就完了。。。但是我们在传播的时候往往需要分享出去，并且自定义传播信息的标题、链接或者图片，有时候一些小游戏还需要获取用户的信息等等，那么，就需要调用微信分享的接口了...
<!--more-->

我跟着[微信官方文档](http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html)开发了两次，其实如果搞清楚了流程，本身步骤也还算简单。但是我发现每次要写很多重复的代码，并且因为时间间隔，每次开发的时候又要重新看看文档，才能回忆起该怎么调用＝＝，所以我写这个微信分享的小插件，就是为了自己以后再做这样的需求时，分分钟搞定。顺便在这里做一个简单的记录，如果文中有错，欢迎大家指出^_^。

# 使用条件
首先解释一下，这里的使用条件，不是说插件的使用条件，而是调用微信分享接口的使用条件。

不是每个人随便写个页面都能拿到微信里，互相传播，让用户授权，获取用户信息的。你需要拥有一个已经认证过的微信公众号，进入"公众号设置"的"功能设置"里填写"JS接口安全域名"，来绑定一个能访问的域名，另外还需要一个与公众号对应的唯一的appId，这个id能从公众号后台查看。

# 调用过程
其实使用的过程在微信官方文档中已经说的非常清楚了，但是其中其实有很多的都是我们用不到的，所以我把分享页面的开发过程再简单的总结一遍:
- 引入官方分享需要的js文件：<a href="http://res.wx.qq.com/open/js/jweixin-1.0.0.js" target="_blank">http://res.wx.qq.com/open/js/jweixin-1.0.0.js</a>
- 调用后台接口，获取签名信息(服务端提供)。
- 所有需要使用JS-SDK的页面必须先注入配置信息，如下配置之后(signature,nonceStr,timestamp就是上一步获取到的信息)，方能调用分享接口。

  ```js
  wx.config({
      debug: true,    //开启调试模式，会实时的提示信息
      appId: self.config.appId, // 必填，公众号的唯一标识
      timestamp: d.timestamp, // 必填，生成签名的时间戳
      nonceStr: d.conststr, // 必填，生成签名的随机串
      signature: d.signature,// 必填，签名，见附录1
      jsApiList: ['onMenuShareTimeline', 'onMenuShareAppMessage']
  });
  ```

- 如果上一步的配置有错，则执行：

  ```js
  wx.error(function(){
        alert('error');
  })
  ```

- 如果配置成功，则执行

  ```js
  wx.ready(function(){
        //...
  })
  ```

- 然后在配置成功的回调函数里，即可调用分享的函数了。

  ```js
  wx.ready(function(){
      wx.onMenuShareTimeline({   //分享到朋友圈
          title: ‘标题’,
          link: 'http://www.taobao.com',
          imgUrl: 'imgurl',
          success: function () {
              self.config.circleFunction();      // 分享成功的回调函数
          },
          cancel: function () {
              // 取消分享
          }
      });
      wx.onMenuShareAppMessage({   //分享给朋友
          title: self.config.title,
          link: self.config.link,
          desc: '为什么这里没有起作用？？',
          imgUrl: self.config.imgUrl,
          success: function () {
              self.config.friendFunction();         // 分享成功的回调函数
          },
          cancel: function () {
              // 取消分享
          }
      });
  })
  ```

  `值得一提的是，这里用到的函数，都需要在配置项的'jsAplilist'中添加进去。` jsApiList: ['onMenuShareTimeline', 'onMenuShareAppMessage']

# 插件代码

```js
function WxShare(conf){
    this.getURLParam = function(name) {
        return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)', "ig").exec(location.search) || [, ""])[1].replace(/\+/g, '%20')) || null;
    };
    this.config = {
        appId: conf && conf.appId || '',
        url: conf && conf.url || '',   //接口地址
        data: {    //接口的参数
            code: this.getURLParam('code'),
            state: this.getURLParam('state'),
            url: location.href.split('#')[0],   // zepto 自动编码对location.href进行encodeURIComponent编码
            method: 0
        },
        imgUrl: conf && conf.imgUrl || '',
        title: conf && conf.title || '',
        link: conf && conf.link || '',
        desc: conf && conf.desc || '',
        circleFunction: conf && conf.circleFunction || function() {},
        friendFunction: conf && conf.friendFunction || function() {},
        callback: conf && conf.callback || function(d){}
    };
    this.init();
}
WxShare.prototype.init = function(){  //获取用户信息
    var self = this;
    $.ajax({
        url: self.config.url,
        data: self.config.data,
        dataType: 'jsonp',
        jsonp: 'callback',
        success: function (d) {    // 成功获取到用户信息，然后配置sdk
            self.config.callback(d);   //处理用户信息
            wx.config({
                debug: false,
                appId: self.config.appId, // 必填，公众号的唯一标识
                timestamp: d.timestamp, // 必填，生成签名的时间戳
                nonceStr: d.conststr, // 必填，生成签名的随机串
                signature: d.signature,// 必填，签名，见附录1
                jsApiList: ['onMenuShareTimeline', 'onMenuShareAppMessage']
            });
            wx.ready(function(){
                wx.onMenuShareTimeline({   //分享到朋友圈
                    title: self.config.title,
                    link: self.config.link,
                    imgUrl: self.config.imgUrl,
                    success: function () {
                        self.config.circleFunction();      // 确认分享
                    },
                    cancel: function () {
                        // 取消分享
                    }
                });
                wx.onMenuShareAppMessage({   //分享给朋友
                    title: self.config.title,
                    link: self.config.link,
                    desc: self.config.desc,
                    imgUrl: self.config.imgUrl,
                    success: function () {
                        self.config.friendFunction();         // 确认分享
                    },
                    cancel: function () {
                        // 取消分享
                    }
                });
            });
        },
        error: function (a, b, c) {
            // alert('error');
        }
    });
}
```

说明：
- 因为我最常见的就是分享朋友圈和分享给朋友这两个需求，所以插件只是实现了这样个功能，官方文档中还有其他很多功能强大的接口，比如分享到微博，分享到qq等等，大家如果有需要，可以自行添加。
- 另外，分享给朋友这个接口还有一些配置项因为不常用，也没有列出来，比如：文件类型type，文件链接dataUrl（如果类型为音乐或视频）。

# 使用文档

```js
        var linkUrl = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx60d2d83ca82005ec&redirect_uri='+ location.href +'&response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect';
        var wxShare = new WxShare({
            appId: 'xxxxxxxxxxxxxxxxxx',
            url: url,
            imgUrl: 'http://pic24.nipic.com/20120831/10132780_100453579000_2.jpg',
            title: '云盾双11-精彩不断！',
            desc: '这时发送给朋友的描述信息',
            link: linkUrl,
            circleFunction: function(){
            },
            callback: function(data){   //处理获得的用户信息
                $('.page1 .logo').html(data.nickname);
            }
        });
```

配置项：
- appId： 必填，公众号的唯一标识。
- url：后端接口地址（不需要配置接口参数，因为插件已经传入了需要的参数code、state、url、method）。
- imgUrl：分享出去的图片地址。
- title：分享出去的标题。
- link：分享出去的链接（注意：如果希望别人点击你分享的链接也需要授权，则需要微信客户端的链接格式。）
- desc：分享给朋友的的描述信息。
- circleFunction：分享到朋友圈成功的回调函数。
- friendFunction：分享给朋友成功的回调函数。
- callback：参数d(用户信息)，接口调用成功，处理用户信息的回调函数。

# 总结
因为平时用到的只有分享朋友圈和朋友这两个接口，所以这个插件也只是实现了这两个功能。并且标题，链接，图片地址都是公用的。以后如有需要，可以继续增加功能。

---
