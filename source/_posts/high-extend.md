---
title: 实例demo之Highextend图表
date: '2016-05-12 12:11:51'
categories: 工作
tags:
  - demo
  - highcharts
  - 插件
toc: true
---

--------------------------------------------------------------------------------

<header style="margin-top:-55px;text-align:center">
    <p style="font-size: 1.5em;margin-top: 30px;"><span>highextend图表实例</span><a href="https://github.com/luckykun/highextend" style="font-size:14px;font-weight:normal;">view on github</a></p>
</header>

highextend是基于highcharts进一步封装的图表组件，组件暴露一个`Hxt`的全局对象，它有以下一些方法来绘制不同类的图形，从此以后，画图只需简简单单的几行配置就ok了！赶紧点进来看看实例吧！

```js
    Hxt.line(elem, data, options);         //渲染默认折线图
    Hxt.spline(elem, data, options);         //曲线图
    Hxt.scatter(elem, data, options);         //散点图
    Hxt.bubble(elem, data, options);         //bubble图
    Hxt.column(elem, data, options);         //柱状图
    Hxt.bar(elem, data, options);         //bar图，（横向柱状图）
    Hxt.area(elem, data, options);         //区域图（默认为平滑区域图）
    Hxt.pie(elem, data, options);         //圆饼图
    Hxt.polar(elem, data, options);         //雷达图
    Hxt.pyramid(elem, data, options);         //金字塔图
    Hxt.mix(elem, data, options);         //混合图
```

<!--more-->


# 实例展示

<div class="high-extend-container">
    <div class="chart" id="line-chart"></div>
    <div class="chart" id="area-chart"></div>
    <div class="chart" id="column-chart"></div>
    <div class="chart" id="mix-chart"></div>
    <div class="small-chart" id="pie-chart1"></div>
    <div class="small-chart" id="pie-chart2" style="margin-top:-28px;"></div>

</div>

# 使用文档

本页面的实例不全，读者可以在[我的github](https://github.com/luckykun/highextend)上访问到这个项目的所有代码，然后down下来执行以下代码就能直接在浏览器看到所有实例。

```js
npm install   //安装依赖
gulp   //启动服务
```

在上面介绍的方法中，有`elem`, `data`, `options`三个参数，下面依次介绍以下：

- elem：绘制图表元素的id。
- data：图表的数据。
- options：图表配置项,`可以为空，为空则使用默认配置。`

## 公共配置项

- `emptyHtml`: 数据为空时的提示信息，支持html格式。

- `backgroundColor`: 图表背景颜色，默认为’白色‘。

- `marginTop`: 图形的上边距，例如 marginTop: 60。

- `marginRight`: 图形的上边距，例如 marginRight: 60。

- `chartLoad`: 设置图形加载方式。一般实时动态数据会配置此项。

- `colors`: 颜色，`类型为数组`。非必选，有默认的颜色。

- `title`: 图表标题，默认为空。

- `subtitle`: 副标题，默认为空。

- `markerEnabled`: 是否显示线条上的点，默认`true`为显示。

- `markerSymbol`: 线条上点的形状，默认为‘circle’圆形，并且默认样式为空心。其他值有‘square’，‘diamond’，‘triangle’等。

- `legendEnabled`: 图例是否显示，默认为`true`，显示图例。

- `legendLayout`: 图例显示方式，默认为水平方向：‘horizontal’。 ‘vertical’为垂直方向。

- `legendAlign`: 水平方向显示位置，默认中间位置：‘center’，其他值有‘right’，‘left’。

- `legendVerticalAlign`: 垂直方向显示位置，默认‘bottom’，其他值有‘top’，‘middle’。

- `legendItemMarginBottom`: 每个图例的下边距，默认为4px。

- `shared`: tooltip提示框是否被共享。默认为`false`。

- `valuePrefix`: tooltip悬浮框value值前面的字符，默认为空。

- `valueSuffix`: tooltip悬浮框value值后面的字符，默认为空。

- `xDateFormat`: tooltip中时间转化格式，默认为‘%Y-%m-%d’，即‘2016-01-10’。

- `chartLabel`： 图形中的提示文案，格式为对象，如：{html:'title', style:{left:'30px', top: '5px'}}。


## 线图/柱状图配置项


- `Xtype`: 横坐标类型，默认为空。`如设置'datetime'，则为时间类型，默认转为'01-01'格式`。

- `Ytitle`: 纵坐标标题，默认为空。

- `Ytype`: 纵坐标类型，默认为空。`如设置'rate',则用1024为单位计算，且默认单位为‘kb’，大于1024单位变为‘M’`。



## 饼图配置项

- `size`: 饼图的整体大小（百分比），默认为100%。

- `innerSize`: 内圆所占的百分比，默认为55%。

- `showInLegend`: 设置圆饼图的图例是否显示，默认为`true`。

- `startAngle`: 圆饼图的开始角度。

- `endAngle`: 圆饼图的结束角度。

- `pieLabel`: 设置圆饼图的series文案，默认为`数量`。

- `pieClick`: 圆饼图的点击事件。

- `pieMouseOut`: 圆饼图的mouseout事件。

- `pieMouseOver`: 圆饼图的mouseover事件。


## 雷达图配置项

- `polarType`: 雷达图的形状，可设置为‘circle’－圆形，默认为‘polygon’－菱形。

- `polarSize`: 雷达图大小百分比，默认95%。


## 正负对比图（bar）配置项

- `barStacking`: 设置为“normal”－表示正负对比图。


## 混合图特有配置项

- `mixPieCenter`: 混合图中饼图的特有设置--中心原点坐标，如[100,50]。

- `mixPieSize`: 混合图中饼图的特有设置--饼图大小，默认为‘60%’。

- `mixPieInnerSize`: 混合图中饼图的特有设置--饼图空心圆大小，默认为0。

- `miePieShowInLegend`: 混合图中饼图的特有设置--是否显示legend，默认为false。

--------------------------------------------------------------------------------

<style>
.high-extend-container {
    min-height: 400px;
    overflow: hidden;
}
.high-extend-container .chart {
    background: #f5f5f5;
    padding: 10px;
    width: 90%;
    height: 280px;
}
.high-extend-container .small-chart {
    padding: 10px;
    background: #f5f5f5;
    width: 360px;
    height: 340px;
    float: left;
    margin-right: 30px;
    margin-bottom: 20px;
    display: inline-block;
}
</style>

<script src="http://cdn.hcharts.cn/highcharts/highcharts.js"></script>
<script src="https://rawgit.com/luckykun/highextend/daily/0.0.1/build/js/highextend-min.js"></script>
<script type="text/javascript">
    var lineData = {
        "items": [{
            "data": [100,80,94,99,80,50,90],
            "name": "tom"
        }],
        "categories": ["first", "second", "third", "forth", "fifth", "sixth", "seventh"]
    };
    var chart = Hxt.spline('line-chart', lineData, {
        shared: true
    });
    chart.fn.setTitle({text: '这是我动态设置的标题'});
    //展示动态添加一行数据，和动态删除一行数据的例子
    var num = 1;
    var time = function() {
        var colors = chart.fn.options.colors;
        if(num < 4) {
            setTimeout(function() {
                chart.fn.addSeries({
                    name: 'name' + num,
                    data:[num*50, num*50, num*50, num*50, num*50, num*50, num*50],
                    color: colors[num]
                });
                num ++;
                time();
            }, 3000);
        }else {
            setTimeout(function() {
                var series = chart.fn.series;
                series[series.length-1].remove();
                num  = num == 6 ? 1 : num+1;
                time();
            }, 3000);
        }
    };
    time();

    var areaData = {
        "items": [{
            "data": [100,105,94,79,80],
            "name": "tom"
        }, {
            "data": [65,90,140,100,120],
            "name": "jane"
        }],
        "timeScope": {
            "interval": 86400000,
            "start": 1463328000000
        }
    };
    var chart1 = Hxt.area('area-chart', areaData, {    //曲线图
        Xtype: 'datetime',
        shared: true
    });
    var chart2 = Hxt.column('column-chart', areaData, {    //曲线图
        Xtype: 'datetime',
        shared: true
    });

    var pieData = {
        "items": [{
            "name": "已开通",
            "data": 20
        },{
            "name": "未开通",
            "data": 5
        },{
            "name": "待定",
            "data": 10
        }]
    };
    var chart3 = Hxt.pie('pie-chart1', pieData, {
        size: '45%',
        valueSuffix: '个',
        showPercentLabels: true
    });
    var chart4 = Hxt.pie('pie-chart2', pieData, {    
        startAngle: -90,  //设置角度，达到半圆效果
        endAngle: 90
    });

                                var mixData = {
                                    "items": [{
                                        "type": "column",
                                        "name": "第一天",
                                        "data": [3, 2, 1, 3, 4]
                                    },{
                                        "type": "column",
                                        "name": "第二天",
                                        "data": [2, 3, 5, 7, 9]
                                    },{
                                        "type": "spline",
                                        "name": "平均值",
                                        "data": [2.5, 2.5, 3, 5, 6.5]
                                    },{
                                        "type": "pie",
                                        "name": "总量",
                                        "data": [{
                                            "name": "第一天",
                                            "y": 13
                                        },{
                                            "name": "第二天",
                                            "y": 26
                                        }]
                                    }],
                                    "categories": ["苹果", "橘子", "石榴", "香蕉", "草莓"]
                                };
                                var chart5 = Hxt.mix('mix-chart', mixData, {
                                    valueSuffix: '斤',
                                    mixPieCenter: [60, 50],
                                    mixPieSize: '55%',
                                    shared: true,
                                    chartLabel: {
                                        html: '水果两天的总销量',
                                        style: {
                                            left: '30px',
                                            top: '5px'
                                        }
                                    }
                                });
</script>
