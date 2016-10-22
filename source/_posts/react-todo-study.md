---
title: React入门最好的实例－TodoList
date: 2016-05-23T22:11:51.000Z
categories: 工作
tags:
  - react
  - es6
  - 教程
toc: true
---

--------------------------------------------------------------------------------


> React 的核心思想是：封装组件，各个组件维护自己的状态和 UI，当状态变更，自动重新渲染整个组件。


最近前端界闹的沸沸扬扬的技术当属`react`了，加上项目需要等等原因，自己也决定花些时间来好好认识下这个东西。然后学习的时候顺便花时间写了一个demo：`react-todos`， 为了提起兴趣，你可以先[点这里去看react-todo](http://luckykun.com/work/2016-05-08/react-todo-demo.html)

首先react值得拍手称赞的是它所有的开发都基于`组件（component）`，然后组件和组件之间通过props传递方法，每个组件都有一个`状态（state）`，当某个方法改变了这个状态值时，整个组件就会`重绘`，从而达到刷新。另外，说到重绘就要提到`虚拟dom`了，就是用js模拟dom结构，等整个组件的dom更新完毕，才渲染到页面，简单来说只更新了相比之前改变了的部分，而不是全部刷新，所以效率很高。
<!--more-->

# 项目初始化
大家先新建一个项目文件夹，在里面建一个项目信息的文件`package.json`:

```js
{
    "name": "react-todos",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
        "react": "^0.13.3",
        "sass": "^0.5.0"
    },
    "devDependencies": {
        "babel-core": "^5.5.8",
        "babel-loader": "^5.1.4",
        "css-loader": "^0.14.5",
        "file-loader": "^0.8.4",
        "jsx-loader": "^0.13.2",
        "node-libs-browser": "^0.5.2",
        "node-sass": "^3.2.0",
        "sass-loader": "^1.0.2",
        "style-loader": "^0.12.3",
        "url-loader": "^0.5.6",
        "webpack": "^1.9.11"
    }
}
```

建好之后，运行命令：

```js
npm install
```

安装项目依赖的所有模块。安装好之后，另外还有一点，项目数据是存储在本地浏览器的，所以我找到一个小模块用来操作localStorage，它的原理就是，通过将数据格式化成JSON字符串进行存储，使用的时候就解析JSON字符串。他的代码[点这里看localDb](https://github.com/luckykun/react-demo/tree/master/node_modules/localDb)可以看到，你可以复制一份，放在node_modules的文件夹内。

# webpack配置
项目使用的技术方案是：`webpack＋react＋es6`。关于es6的文章，我之前简单的介绍过，可以[点这里去看es6](http://luckykun.com/work/2016-05-10/es6-feature.html)，关于webpack的学习，我这里不详述了，看以后有时间再出篇文章吧。在项目文件夹下新建一个`webpack.config.js`：

```js
'use strict';
module.exports = {
    entry: [
        "./src/entry.js"
    ],
    output: {
        path: './out/',
        filename: "bundle.js"
    },
    externals: {
        'react': 'React'
    },
    module: {
        loaders: [
            { test: /\.js$/, loader: "jsx!babel", include: /src/},
            { test: /\.css$/, loader: "style!css"},
            { test: /\.scss$/, loader: "style!css!sass"},
            { test: /\.(png|jpg)$/, loader: 'url?limit=8192'}
        ]
    }
};
```

上面的文件可以看到：入口文件是在src文件夹里的entry.js，然后输出文件放在out文件夹的bundle.js里。externals属性是告诉webpack当遇到require('react')的时候，不去处理并且默认为全局的React变量。这样子，我们就需要在index.html单独用src去加载js。最后看看配置的loaders：
- 因为我们js文件会使用jsx和es6的语法，所以使用`jsx-loader`和`babel-loader`来编译js文件。
- scss文件使用`sass-loader`编译成css文件。
- 写的时候可以省略-loader，多个loader使用`!`连接。

# 项目目录
先来看一下项目的目录结构，最重要的就是src目录：
- `index.html`是项目的入口页面。
- `components`文件夹存放项目拆分出来的各个组件文件。
- `vendor`文件夹存放项目依赖的框架，这里只有react。

# index.html和entry.js
先来看index.html：

```js
<body>
    <header>
        <h1 class="todo-title">React-Todos</h1>
    </header>
    <div class="container todo-container">
        <div id="app"></div>
    </div>
    <script src="./src/vendor/react.min.js"></script>
    <script src="./out/bundle.js"></script>
</body>
```

entry.js :

```js
'use strict';
require('./styles/main.scss');    // 引入样式表
require('./components/App');     // 引入组件
```

webpack会将入口文件进行合并和整理，最后输出一个bundle.js，所以所有的逻辑都在这个js文件中，因此在index.html中，只需要引入react框架和bundle.js就好了。

# 分析组件
这个todo的项目，我们可以分为三个部分：头部，中间部分，尾部。那我们就来逐一的分析一下这些组件：

## App

```js
'use strict';
import React from 'react';
import LocalDb from 'localDb';
import TodoHeader from './TodoHeader.js';
import TodoMain from './TodoMain.js';
import TodoFooter from './TodoFooter.js'
//es6写法
class App extends React.Component { //定义组件，继承父类
    constructor() { //定义App类的构造函数
        super(); //调用父类的构造函数
        this.db = new LocalDb('ReactDemo');
        this.state = { //定义组件状态
            todos: this.db.get('todos') || [],
            isAllChecked: false
        };
    }
    // 判断是否所有任务的状态都完成，同步底部的全选框
    allChecked() {
        let isAllChecked = false;
        if (this.state.todos.every(todo => todo.isDone)) {
            isAllChecked = true;
        }
        this.setState({   //改变状态，组件重绘
            todos: this.state.todos,
            isAllChecked: isAllChecked
        });
    }
    // 添加任务，是传递给Header组件的方法
    addTodo(todoItem){
        this.state.todos.push(todoItem);  //todo列表
        this.db.set('todos', this.state.todos);
        this.allChecked();
    }
    // 删除当前的任务，传递给TodoItem的方法
    deleteTodo(index){
        this.state.todos.splice(index, 1);
        this.setState({todos: this.state.todos});  //改变状态
        this.db.set('todos', this.state.todos);
    }
    // 清除已完成的任务，传递给Footer组件的方法
    clearDone(){
        let todos = this.state.todos.filter(todo => !todo.isDone);   //过滤掉数组中todo.isDone为true的item。
        this.setState({
            todos: todos,
            isAllChecked: false
        });
        this.db.set('todos', todos);
    }
    // 改变任务状态，传递给TodoItem和Footer组件的方法
    changeTodoState(index, isDone, isChangeAll=false){   //初始化isChangeAll为false
        if(isChangeAll){     //全部操作
            this.setState({
                todos: this.state.todos.map((todo) => {
                    todo.isDone = isDone;
                    return todo;
                }),
                isAllChecked: isDone
            });
        }else{   //操作其中一个todo
            this.state.todos[index].isDone = isDone;
            this.allChecked();
        }
        this.db.set('todos', this.state.todos);
    }
    //组件渲染方法
    render() {
        let info = {
            isAllChecked: this.state.isAllChecked,
            todoCount: this.state.todos.length || 0,
            todoDoneCount: (this.state.todos && this.state.todos.filter((todo) => todo.isDone)).length || 0
        };
        return (
            <div className="todo-wrap">
                <TodoHeader addTodo={this.addTodo.bind(this)} />
                <TodoMain todos={this.state.todos} deleteTodo={this.deleteTodo.bind(this)} changeTodoState={this.changeTodoState.bind(this)} />
                <TodoFooter {...info} changeTodoState={this.changeTodoState.bind(this)} clearDone={this.clearDone.bind(this)} />
            </div>
        );
    }
}
React.render(<App/>, document.getElementById('app'));
```

我们知道React的主流思想就是，所有的state状态和方法都是由父组件控制，然后通过props传递给子组件，形成一个单方向的数据链路，保持各组件的状态一致。所以我们在这个父组件App上，看的东西稍微有点多。一点点来看：
- 它采用es6的语法来创建了一个`继承React.Components的App类`。
- 然后在构造函数里定义了自己的`状态state`。
- 然后定义了很多方法，后面通过`props传递给子组件`。
- 最后定义组件自己的渲染方法`render`。

### App状态

```js
this.state = { //定义组件状态
    todos: this.db.get('todos') || [],
    isAllChecked: false
};
```

在App组件的构造函数里，我们初始化了组件的state，分别有两个，一个是todos的列表，一个是所有的todos是否全选的状态。在渲染的时候，我们会把状态传递到子组件中，如果子组件的某一个方法让状态发生了改变，那么整个组件就会进行重绘。

### App的方法

```js
// 判断是否所有任务的状态都完成，同步底部的全选框
allChecked() {}
// 添加任务，是传递给Header组件的方法
addTodo(todoItem) {}
// 删除当前的任务，传递给TodoItem的方法
deleteTodo(index) {}
// 清除已完成的任务，传递给Footer组件的方法
clearDone() {}
// 改变任务状态，传递给TodoItem和Footer组件的方法
changeTodoState(index, isDone, isChangeAll=false) {}
//组件渲染方法
render() {
    let info = {
        isAllChecked: this.state.isAllChecked,
        todoCount: this.state.todos.length || 0,
        todoDoneCount: (this.state.todos && this.state.todos.filter((todo) => todo.isDone)).length || 0
    };
    return (
        <div className="todo-wrap">
            <TodoHeader addTodo={this.addTodo.bind(this)} />
            <TodoMain todos={this.state.todos} deleteTodo={this.deleteTodo.bind(this)} changeTodoState={this.changeTodoState.bind(this)} />
            <TodoFooter {...info} changeTodoState={this.changeTodoState.bind(this)} clearDone={this.clearDone.bind(this)} />
        </div>
    );
}
```

从上面的渲染（render）方法可以看出，组件的结构分为三部分，就是上中下。上面的`TodoHeader`是用来输入任务的地方，中间的`TodoMain`是用来展示任务列表的, 下面的`TodoFooter`提供一些特殊的方法，比如全选、删除等。

另外，上面省去function创建函数的方法，是es6的一种语法，关于es6，我之前总结过一篇文章[点这里去看es6](http://luckykun.com/work/2016-05-10/es6-feature.html)。

App组件定义的方法，会在渲染的时候传递给子组件，比如TodoHeader组件：

```js
<TodoHeader addTodo={this.addTodo.bind(this)} />
```

说明：
- 通过props传递子组件需要的值和方法。
- 传递方法时一定要bind(this)，不然内部this会指向不正确。
- 子组件的标签使用的时候一定要使用`/`闭合起来。
- ES6语法，spread操作符让代码简洁很多，如上述代码中的TodoFooter:

  ```js
    <TodoFooter {...info} />
    //如果不使用spread操作符，就要这样写：
    <TodoFooter isAllchecked={info.isAllChecked} todoCount={info.todoCount}     todoDoneCount={info.todoDoneCount}>
  ```

### 渲染App

```js
React.render(<App/>, document.getElementById('app'));
```

把上面的App组件的内容渲染到id为'app'的dom元素里。

然后我们再简单看一下分解出来的三个组件：`TodoHeader`, `TodoMain`, `TodoFooter`。

## TodoHeader组件

```js
class TodoHeader extends React.Component {
    // 绑定键盘回车事件，添加新任务
    handlerKeyUp(e) {
        if(e.keyCode == 13) {
            let value = e.target.value;
            if(!value) return false;
            let newTodoItem = {
                text: value,
                isDone: false
            };
            e.target.value = '';
            this.props.addTodo(newTodoItem);   //使用props调用App组件传过来的方法。
        }
    }
    render() {
        return (
            <div className="todo-header">
                <input onKeyUp={this.handlerKeyUp.bind(this)} type="text" placeholder="请输入你的任务名称，按回车键确认"/>
            </div>
        )
    }
}
export default TodoHeader;   //ES6语法，导出模块，上文提到的es6文章中有讲解
```

TodoHeader组件的创建方法和App组件的创建方法一样，内部方法就少了很多了，这里就定义了一个监听键盘的方法，绑定到了输入框的keyUp事件上，敲击回车键的时候就会调用父组件传过来的`addTodo()方法`。

## TodoMain组件

```js
class TodoMain extends React.Component {
    render() {
        if(this.props.todos.length == 0) {
            return (
                <div className="todo-empty">恭喜您，目前没有待办任务！</div>
            )
        } else {
            return (
                <ul className="todo-main">
                    {
                        this.props.todos.map((todo, index) => {
                            //{...this.props} 用来传递TodoMain的todos属性和delete、change方法。
                            return <TodoItem text={todo.text} isDone={todo.isDone} index={index} {...this.props}/>
                        })
                    }
                </ul>
            )
        }
    }
}
```

TodoMain组件主要是为了把传递过来的todos列表遍历显示出来，而每一个list又是一个TodoItem组件。这里又用到了spread操作符`{...this.props}`，代码中也做了注释，可以洗洗品味一下。

## TodoItem组件

```js
class TodoItem extends React.Component {
    //改变任务是否已完成的状态
    handlerChange() {
        let isDone = !this.props.isDone;
        this.props.changeTodoState(this.props.index, isDone);
    }
    // 鼠标移入事件
    handlerMouseOver() {
        React.findDOMNode(this).style.background = '#eee';
        React.findDOMNode(this.refs.delButton).style.display = 'inline-block';
    }
    handlerMouseOut() {
        React.findDOMNode(this).style.background = '#fff';
        React.findDOMNode(this.refs.delButton).style.display = 'none';
    }
    // 删除当前任务
    handlerDelete(){
        this.props.deleteTodo(this.props.index);
    }
    render() {
        let className = this.props.isDone ? 'task-done' : '';
        return (
            <li onMouseOver={this.handlerMouseOver.bind(this)} onMouseOut={this.handlerMouseOut.bind(this)}>
                <label>
                <input type="checkbox" checked={this.props.isDone} onChange={this.handlerChange.bind(this)} />
                <span className={className}>{this.props.text}</span>
                </label>
                <button ref="delButton" className="btn btn-danger" onClick={this.handlerDelete.bind(this)}>删除</button>
            </li>
        )
    }
}
```

TodoItem有这四个方法，我们主要看看新出现的几点：
- `React.findDOMNode(this)`可以获取当前这个组件标签。
- 在元素中定义`ref=xxx`属性，就可以通过`React.findDOMNode(this.refs.xxx)`获取到这个元素。
- 给元素定义class类名的时候要使用`className`。

## TodoFooter组件

```js
class TodoFooter extends React.Component {
    //改变任务是否已完成的状态
    handlerSelectAll(e) {
        this.props.changeTodoState(null, e.target.checked, true);    // true表示全部操作。
    }
    //删除全部已完成的任务
    handlerDeleteDone() {
        this.props.clearDone();
    }
    render() {
        return (
            <div className="todo-footer">
            <label>
                <input type="checkbox" checked={this.props.isAllChecked} onChange={this.handlerSelectAll.bind(this)} />全选
            </label>
            <span><span className="text-success">已完成{this.props.todoDoneCount}</span> / 全部{this.props.todoCount}</span>
            <button className="btn btn-danger" onClick={this.handlerDeleteDone.bind(this)}>清除已完成任务</button>
            </div>
        )
    }
}
```

todoFooter组件主要用来批量更改状态和清除已完成的任务，还要显示任务完成情况，所以代码很简单了。

# 总结


`webpack 命令行的几种基本命令: `

```js
$ webpack // 最基本的启动webpack方法
$ webpack -w // 提供watch方法，实时进行打包更新
$ webpack -p // 对打包后的文件进行压缩，提供production
$ webpack -p -w // 对打包后的文件进行压缩，并且实时监听
$ webpack -d // 提供source map，方便调试。
```

回过头来再看看这个demo的实现过程，react组件化的思想让我们编写代码的时候思维清晰，便于阅读。我们通过父组件来控制状态，并通过props传递，来保证组件内的状态一致，并且我们可以清晰的看到某一个方法该由谁来维护。这是一种全新的前端编码体验，相信以后会成为主流。

另外，我们看到代码中，html直接嵌到js中了，这就是React提出的一种叫JSX的语法。其实入门react本身还是很简单，只是很多人看到JSX和ES6的语法，就打了退堂鼓了，因为我们被代码分离"洗脑"太久了。其实，它们就好像是一堵墙，要是我们畏惧这个障碍止步不前，那么只能停留在原地，如果我们骨气勇气爬上去，才发现react的风景真的很优美！



# 参考资料

- [http://www.reqianduan.com/2297.html](http://www.reqianduan.com/2297.html)
- [http://wiki.jikexueyuan.com/project/react-tutorial/](http://wiki.jikexueyuan.com/project/react-tutorial/)
- [http://gank.io/post/564151c1f1df1210001c9161](http://gank.io/post/564151c1f1df1210001c9161)


--------------------------------------------------------------------------------
