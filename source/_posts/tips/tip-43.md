---
title: Echarts 宽度设置为100% 不生效
categories:
  - tips
tags:
  - Echarts
  - footable
toc: false
date: 2018-03-13 19:57:05
---

Echarts是一款常用的前端图形插件。在使用中，有时会遇到Echarts宽度设置为100%，但并不生效的情况。

<!-- more -->
此种情况一般发生在，Echarts所在的dom结构在页面初始化的时候处于隐藏状态的情形。
例如：
```js
<div>
    <div className="widget-body">
        <ul id="myTab" className="nav nav-tabs">
            <li className="active">
                <a data-toggle="tab" href="#tab1">
                    Tab1
                </a>
            </li>
            <li>
                <a data-toggle="tab" href="#tab2">
                  Tab2
                </a>
            </li>            
        </ul>
        <div className="tab-content padding-header">
            <div className="tab-pane active" id="tab1">
                Tab1 Content                
            </div>
            <div className="tab-pane" id="tab2">
              <Echarts {...this.props} style={{ width:'100%'}}/>                
            </div>
        </div>
    </div>
</div>
```
> Echarts是一个显示Echarts图表的Component

在这个例子中,Tab首屏展示的Tab1，Tab2中的Echarts组件在Tab被点击后才显示（但此时已加载）。虽然我们设置了Echarts宽度为100%自适应，但实际使用中会发现当Tab2点击后，Echarts的宽度只有100px，而且也非自适应的。

查其原因，当Echarts处于隐藏中加载时，无法获取其父元素的100%宽度这个值，所以截断了100%为100，宽度就变成了100px。

该怎么解决呢：
```js
<div>
    <div className="widget-body">
        <ul id="myTab" className="nav nav-tabs">
            <li className="active">
                <a data-toggle="tab" href="#tab1">
                    Tab1
                </a>
            </li>
            <li>
                <a data-toggle="tab" href="#tab2">
                  Tab2
                </a>
            </li>            
        </ul>
        <div className="tab-content padding-header">
            <div className="tab-pane active" id="tab1">
                Tab1 Content                
            </div>
            <div className="tab-pane" id="tab2">
              (currentTabId === 'tab2' ?
                <Echarts {...this.props} style={{ width:'100%'}}/>:
                ""
              )
            </div>
        </div>
    </div>
</div>
```
首屏时，先不加载Echarts组件。当Tab2被点击，页面显示之后再加载Echarts。这也是延迟加载的思想，当需要时才加载，减少首屏的渲染时间。

类似于Echarts这样的情况，我在angular中，使用footable时也遇到过，可做相同处理。
