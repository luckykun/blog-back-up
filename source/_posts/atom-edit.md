---
title: 优美的编辑器－Github Atom
date: 2016-05-13T10:11:00.000Z
categories: 工作
tags:
  - 编辑器
toc: true
---

--------------------------------------------------------------------------------

周末闲着没事，逛论坛发现了一个新的编辑器，由github发布的Atom编辑器。瞬间被吸引了，所以就去尝试着折腾了一下，后来发现这个编辑器确实很不错，他的特点就是两个字：`优美！！！`

下载地址，官方网站下载速度太慢太慢了。。。这里有个github的下载地址。要快很多  [下载编辑器点这里](https://github.com/atom/atom/releases/tag/v1.0.19)

<!--more-->

# Atom介绍
Github的员工Nathan Sobo在Atom的博客中提到："Sublime和TextMate十分方便，但是扩展性不足；另一方面，Emacs和 Vim扩展性很强却需要学习日程工作中很少用到的脚本语言。"因此，他们希望找到一个平衡点，于是就有了Atom这个项目。

Atom 代码编辑器支持 Windows、Mac、Linux 三大桌面平台，完全免费，并且已经在 GitHub 上开放了全部的源代码。它支持各种编程语言的代码高亮(`HTML` / `CSS` / `Javascript` / PHP / Python / C / C++ / Objective C / Java / JSON / Perl / CoffeeScript / Go / Sass / YAML / `Markdown` 等等)。

下面是优酷上Atom的宣传片，逼格好高的样子，`老奶奶都写会css...`:
<iframe height=300 width=510 src="http://player.youku.com/embed/XMTI4NTgzNzY0OA==" frameborder=0 allowfullscreen></iframe>

我捣腾了这么久，说说自己的看法吧。作为一个一直使用sublime 编辑器的人来说，转用Atom是非常的简单，Atom和Sublime功能非常相似，很多快捷键都是一样的。虽然他们现有功能差不多，并且sublime更加轻巧，但是我觉得相比于sublime，Atom的优势也很明显，界面更加优美，功能强大，插件使用方便，可扩展性强等等。。。

## 优美
Atom装了expose这个插件之后，mac用户使用`shift＋command＋e`组合键可以得到意想不到的酷炫！！下面是截图为证，可以方便的看到每一个文件大概的内容，快速选择已经打开的文件。方便！！！优美！！！

![screenshot](http://img4.tbcdn.cn/L1/461/1/165c46ce00a0d55151c8f92f0f1b32f2408caaa0)

## 和git完美结合
Atom 编辑器可以和 GIT 完美结合，所有对代码、文本的修改都能体现在编辑器的界面上。

> 比如在文件内新写的代码会在左边标记为绿色，删除的标记为红色，修改的标记为黄色。在左边的目录导航也能方便的看到文件改动：有改动的文件其文件名和所在文件夹名都会被标记为高亮显示。

编辑器底部会显示当前所在分支和对文件的修改行数统计，对于 GIT 用户来说非常方便。

## 基于WEB技术构建
和微软发布的Visual Studio Code编辑器一样，Atom 也是基于WEB技术（Chromium+Node.js）开发的，简单理解的话编辑器本身其实是一个跑在本地的网页，这足以让无数 WEB 工程师为之兴奋！用户可以用Javascript来编写编辑器插件，并且github大家都知道，他是Atom的强大后台支撑，这里有着大量的编程爱好者，相信Atom的插件会持续的增多，Atom也会变的越来越优秀。

# Atom推荐插件
安装插件跟sublime一样，可以在setting界面中，搜索安装，也可以快捷键`shift＋command＋p`跳出搜索输入框输入关键字进行安装。

`atom-beautify`:美化代码，格式化代码的插件。

`autocomplete-paths`:输入.或者/的时候，会自动提示路径和路径下的文件。非常好用！

`autoprefixer`:css样式的兼容性添加。当写好自己的css之后，会自动添加类似-webkit-等前缀的css样式代码。

`color-picker`:css中需要赋值颜色的时候，使用这个插件可以看到一个完整的取色器，实时的取色，改变。。。非常方便！

`file-icons`:左边工程目录的每一个文件，如果有一个小图标表示就漂亮了，没错，就需要这个插件了。

`git plus`：当然是为了更好的使用git了。

`markdown-format，markdown-writer`：如果你不是Mac用户，并且十分喜欢使用markdown来写东西的话，那么你一定会爱上在Atom上写markdown的感觉~~

`linter`: 自动提示你代码中不规范的地方，让你拥有更加规范的代码习惯。如果你想得到完整的信息提示，则推荐这个插件`linter-jshint`

`minimap`:用过sublime text的同学一定知道右边那方便的缩略图，难道这么好用的工具Atom上会没有吗？不会！这个插件就会让你见到熟悉的缩略图, 而且功能更加强大！

插件                           | 说明
---------------------------- | ---------------------------------------------------------------------
`minimap-codeglance`         | 放大镜的功能，这个插件就会让你的鼠标移动到缩略图上的时候放大显示那边部分的代码
`minimap-find-and-replace`   | 当你想替换单词的时候你会想起`ctrl+D`，可以你知道全篇有多少你要替换的字符串吗？通过这个插件你就可以在缩略图上看到所有你选中的字符串
`minimap-git-diff`           | 通过这个插件，每当你修改你的代码的时候你就会在缩略图上看到和之前git中的区别
`minimap-highlight-selected` | 当你选中部分代码的时候，它就会高亮的出现在缩略图中
`minimap-linter`             | 这个插件让你的缩略图显示的更加漂亮和完整

`expose`:当你一次性打开多个文件的时候也许你会使用分屏来查看，安装了这个插件之后使用`shift＋command＋e`就可以分屏查看。截图上面已经出现过，美不美！！！

`atom-material-ui`:这是一个`主题插件`,他有自带的一些动态效果，特别酷炫。话不多说，自己试试就知道。

终于废了老半天时间来搞这个编辑器，不过每装一个插件就能感受其强大的时候，就觉得很值得，下面是我使用这个主题的截图，很漂亮吧？

![screenshot](http://img1.tbcdn.cn/L1/461/1/f15c17095abedad0dc396aaf30e0a0c84d39bda3)

# Atom快捷键
其实Atom和sublime的跨界件非常相似，比如我常用的cmd+d,这里只列出一些常用的快捷键。

`ctrl+shift+s` 保存所有打开的文件

`cmd+\` 显示或隐藏目录树

`cmd+b` 在打开的文件之间切换

`cmd+d` 快速查找选中的内容

`cmd+right, ctrl+E` 移动到一行结束

`cmd+left, ctrl+A` 移动到一行开始

`cmd+K, cmd+U` 使当前字符大写

`cmd+K, cmd+L` 使当前字符小写

`ctrl+shift+K` 删除当前行

`ctrl+shift+U` 调出切换编码选项

`cmd+F` 在当前打开的页面中查找

`cmd+shift+f` 在整个工程中查找

`ctrl+space` 提示补全信息

`ctrl-shift-M` Markdown预览（前提是装了markdown插件）

如果有需要，大家去看这个链接吧，里面的快捷键总结非常的全面，[想看点这里](http://www.iplaysoft.com/item/atom-shortcuts)



---
