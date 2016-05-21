---
title: hexo搭建属于自己的博客
date: 2016-04-23T22:11:51.000Z
categories: 工作
tags:
  - hexo
  - 教程
toc: true
---

--------------------------------------------------------------------------------

一直都在博客园写博客，不过最近在逛园子的时候不小心看到了`hexo`,简直有种相见恨晚的感觉呀！在github上创建自己的开源博客，维护方便，主题多多，更重要的是，终于找到只属于自己的小窝啦！也希望自己能坚持写文^_^

为了引起读者的兴趣，先介绍一下，搭建完成之后，只需这几个简单常用的命令，就能够轻松维护自己的博客了：

```js
$ hexo new(n)    //写文章
$ hexo generate(g)    //把文章生成页面
$ hexo server(s)    //启动本地服务调试
$ hexo deploy(d)    //部署到github 可与hexo g合并为 hexo d -g
```

<!--more-->

折腾了大半天，终于搭建好了自己的博客。不过在这中途，也是历经了九九八十一难呀，所以，我怀着激动心情，把我在这个过程中遇到的坑作为第一篇博客一一记录下来，希望能帮到后面的学者。
- `备注1：如果喜欢以官方教程为主的，点这里`[hexo官方文档](https://hexo.io/zh-cn/docs/)。
- `备注2：网上大多教程是都是hexo2.x版本的，所以有很多坑。因此以下教程是针对hexo的版本为3.x以上的，大家放心使用。`

# 准备工作
- 安装node
- 到[Node.js](http://nodejs.org/)官网下载相应平台的最新版本，一路安装即可。
- 安装git
- 根据系统不同安装相应的git环境。mac系统不多说，windows一般安装msysgit。下面是我本地msysgit的截图，也还是挺好用的:
- ![](http://7xtawy.com2.z0.glb.clouddn.com/msysgit.png)
- 安装hexo

    使用以下命令安装hexo到全局

  ```js
    $ npm install -g hexo
  ```

    然后输入命令`hexo -v`输入hexo的版本号即为安装成功。

- github准备
  - 博客是在[github](https://github.com/)上托管维护的，所以当然需要一个github的账号了。然后创建一个名为`luckykun.github.io`的仓库。
  - 其中'luckykun'是我的账户名，下文同理。
  - 除此之外，相信大多数人都知道，要想使用git命令来和github进行提交部署等操作，需要进行一些配置，大概就是下面一些命令，如不明白请自行搜索。

    ```js
      git config --global user.email xxx@163.com
      git config --global user.name xxx
      ssh-keygen -t rsa -C xxx@163.com(邮箱地址)      // 生成ssh
      找到.ssh文件夹打开，使用cat id_rsa.pub    //打开公钥ssh串
      登陆github，settings － SSH keys  － add ssh keys（把上面的内容全部添加进去即可）
    ```

# 初始化
ok，环境都准备好了，开始激动人心的步骤了，搭建博客。

在某个地方新建一个项目文件夹（比如Blog），然后进入Blog目录，以下所有的命令行操作都是在这个文件夹下进行的。

```js
$ hexo init
```

# 生成静态页面
初始化完成之后，就已经生成一篇"hello word"的文章了，现在执行以下命令把文章编译为静态页面：

```js
$ hexo generate
```

# 本地启动
把文章变为页面之后，可以执行以下命令，本地启动服务，在浏览器中输入[http://localhost:4000/](http://localhost:4000/)查看生成的页面效果。

```js
$ hexo server
```

如果你看到了下面这个画面，恭喜你，你成功了！ ![](https://img.alicdn.com/tps/TB1.knpHVXXXXcmaXXXXXXXXXXX-1003-456.png)

# 更换主题
上面的博客效果是hexo自己默认的主题`landscape`，如果你满足与它，可以跳过这个步骤，直接进行下一步。但是我却还不够满足，因为我发现了一个页面交互人性化，并且完美兼容不同终端显示的主题[yilia](https://github.com/litten/hexo-theme-yilia)，貌似这个主题受欢迎程度很高呀，说明我的审美还是不错啊，哈哈~~

下面贴出github上star数量最多的前10个主题：
- [iissnan/hexo-theme-next](https://github.com/iissnan/hexo-theme-next)， 3510个star。
- [litten/hexo-theme-yilia](https://github.com/litten/hexo-theme-yilia)， 1703个star。
- [TryGhost/Casper](https://github.com/TryGhost/Casper)， 679个star。
- [wuchong/jacman](https://github.com/wuchong/jacman)， 503个star。
- [A-limon/pacman](https://github.com/A-limon/pacman)， 431个star。
- [daleanthony/uno](https://github.com/daleanthony/uno)， 416个star。
- [orderedlist/modernist](https://github.com/orderedlist/modernist)， 367个star。
- [AlxMedia/hueman](https://github.com/presscustomizr/hueman)， 336个star。
- [kathyqian/crisp-ghost-theme](https://github.com/kathyqian/crisp-ghost-theme)， 303个star。
- [xiangming/landscape-plus](https://github.com/xiangming/landscape-plus)， 287个star。

clone主题代码

在目录下执行下面的命令clone主题代码：

```js
    $ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

修改配置文件

`温馨提示：修改的时候，每个冒号后面都需要留一个英文空格，不然会出现很蛋疼的报错！`

修改`Blog/_config.yml`文件：

```java
    theme: yilia    //默认为landscape
```

修改`themes/yilia/_config.yml`文件：

```js
    # Header
    menu:
        主页: /
        所有文章: /archives
        丝茉茉: /categories/simomo/
        相册: /photos
    # SubNav
    subnav:
        github: "https://github.com/luckykun"
         weibo: "http://weibo.com/u/2732624311"
        zhihu: "#"
        rss: /atom.xml
    # Content
    excerpt_link: 阅读全文
    fancybox: true
    mathjax: true
    top: true
    # 是否开启动画效果
    animate: true
    # 是否在新窗口打开链接
    open_in_new: false
    # Miscellaneous
    google_analytics: ''
    favicon: /favicon.ico
    #你的头像url
    avatar: /img.png
    #是否开启分享
    share_jia: true
    share_addthis: false
    #是否开启多说评论，填写你在多说申请的项目名称 duoshuo: duoshuo-key
    #若使用disqus，请在博客config文件中填写disqus_shortname，并关闭多说评论
    duoshuo: jarson7426
    #是否开启云标签
    tagcloud: true
    #是否开启友情链接
    #不开启——
    friends: false
    #开启——
    #friends:
    #  百度一下: http://www.baidu.com
    #  淘宝商城: http://www.taobao.com
    #是否开启“关于我”。
    #不开启——
    #aboutme: false
    #开启——
    aboutme: true
```

查看效果

```
更改主题之后可以使用命令`hexo server`打开本地服务，查看效果。
```

# 部署到github
上面所有的操作完成之后，你就可以将你的Blog项目部署到github上了。
- 部署之前先修改`Blog/_config.yml`文件。

  ```js
    deploy:
        type: git
        repository: https://github.com/luckykun/luckykun.github.io.git  //luckykun替换为你自己的用户名
        branch: master
  ```

    `备注：在hexo3.x版本下，这里的type应该填git，不是github；另外冒号后面都有一个英文的空格，不然会报错的。`

- 然后使用以下命令进行部署。

  ```js
    $ hexo deploy
  ```

    `备注：如果执行上述命令报错，你可以试试下面这个命令再试。`

  ```js
    $ npm install hexo-deployer-git --save
  ```

  另外，部署的时候会提示输入github的账号和密码。由于每次部署都会提示，很烦人，也很影响效率，所以你可以试着这样做：
  - 在系统环境变量中设置一个环境变量

    ![](http://7xtawy.com1.z0.glb.clouddn.com/home.png)

  - 然后在你的用户目录（C:\Users\username）下新建一个叫 _netrc的文件，内容如下：

    ```js
    machine github.com
    login your name
    password xxxxxx
    ```

    然后你可以再部署试试，就不需要输入用户和密码，方便友好多了^_^

部署成功后，你在浏览器中输入[luckykun.github.io](http://luckykun.github.io/)，就能看到和本地一样的效果了。

# 结语
可能会有同学发现，我除了使用了新的主题外，还加了很多新的东西。
- 鼠标移动头像上会有旋转效果。
- 给文章添加打赏功能。
- 添加回到顶部的功能。
- 添加总站访问量和文章阅读量
- 添加文章目录显示
- 添加相册功能
- 给网站配置独立的域名

其实修改这些非常简单，只要你有html、css、js的基础就可以。 思路：因为整个网站都是依赖yilia主题。所以应该在theme/yilia文件夹下，修改对应的dom结构，然后添加对应的效果或者事件绑定就可以实现了。

如果大家还有问题，欢迎留言，我会很乐意为大家解答的。

好了，到这里搭建github博客的步骤就结束了。快快enjoy it吧！！！

--------------------------------------------------------------------------------
