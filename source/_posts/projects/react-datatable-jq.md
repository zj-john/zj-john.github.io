---
title: react-datatable-jq 组件
date: 2018-01-19 12:20:23
categories:
  - projects
tags:
  - datatable
  - react
toc: true
---

- **功能**：react-datatable-jq npm包
- **工具**：react, datatable
- **难度等级**: ★★
- **项目地址**: https://github.com/zj-john/react-datatables

# react-datatables
Datatable components built with React

## Install
```
npm install react-datatable-jq --save
```
<!-- more -->

## Usage
```js

import React from 'react';
// import react-datatable-jq
import Datatable from 'react-datatable-jq'

// datatable global options. You can import it from your config.js
const options = {
    dom: "<'dt-toolbar'<'col-xs-12 col-sm-6'f><'col-sm-6 col-xs-12 hidden-xs text-right'l>r>" + "t" + "<'dt-toolbar-footer'<'col-sm-6 col-xs-12 hidden-xs'i><'col-xs-12 col-sm-6'p>>",
    autoWidth: false,
    searching: false,
    paging: true,
    language: {
        search: "<span class='input-group-addon input-sm'><i class='glyphicon glyphicon-search'></i></span> ",
        lengthMenu: "每页显示 _MENU_ 条记录",
        info: "<b>从_START_到_END_ / 共_TOTAL_条记录</b>",
        infoEmpty: "显示0条记录",
        emptyTable: "没有符合条件的记录",
        zeroRecords: "没有符合条件的记录",
        loadingRecords: "加载中...",
        processing: "处理中...",
        paginate: {
            "first": "<b>首页</b>",
            "previous": "<b>上一页</b>",
            "next": "<b>下一页</b>",
            "last": "<b>尾页</b>"
        }
    }
}

class BasicDatatable extends React.Component {
    constructor(props) {
        super(props);
        // change the default options or add new option
        options.ording = false;
        this.options = options;

        // the events list on datatable
        this.events = [{
            type: "click",
            scope: "tbody tr td",
            func: function() {
                console.log("a test");
            }
        }];
        // the columns for the datatable
        this.columns = [{
            data: 'id',
            title: 'ID',
        },
        {
            data: 'name',
            title: 'Name',
            orderable: false
        },
        {
            data: 'sex',
            title: 'Sex',
            render: function(data, full) {
                return data.toUpperCase() === 'M' ? "Boy": "Girl";
            },
            orderable: false
        },
        {
            data: null,
            title: 'Menu',
            className: "menu",
            orderable: false,
            createdCell: function(td, cellData, rowData, row, col) {
                $(td).on('click', '.action', function() {
                    console.log("you click the action dom");
                });
            }
        }]
        // dtData 负责 datatable data的部分
        this.state = {
            DTdata: null,
        }
    }

    componentWillMount() {
        let url = "/api/basic/ajax",
            postData = {
                "range": "all"
            },
            dtData = {
                _method: "ajax",
                url: url,
                data: function(d) {
                    $.extend(d, postData);
                    return JSON.stringify(d);
                },
                type: "post",
                contentType: "application/json; charset=utf-8",
                dataSrc: "data"
            };
        this.setState({
            dtData: dtData,
        })
    }

    render() {
        // theme: one of ["bootstrap", "bootstrap4", "foundation", "jqueryui", "material", "semanticui", "uikit"], default JqueryDatatable
        return (
          <div>
              this is sample：
              <Datatable
                  theme = {"bootstrap"}
                  options = {this.options}
                  dtData = {this.state.dtData}
                  columns = {this.columns}
                  events = {this.events}
                  className = "table table-striped table-hover"
                  id = "sample_table"
              />
          </div >
      );
    }
}
```

# Props

## theme
根据不同框架，可以选择不同主题的datatable样式，可选theme如下：
* bootstrap
* bootstrap4
* foundation
* jqueryui
* material
* semanticui
* uikit

theme为空时，将选择默认的jQuery Datatable的样式。

>选择不同主题的datatable时，请记得把框架本身的js和css导入。例如选择bootstrap样式时，需要同时引用bootstrap.css，bootstrap.js文件来辅助样式

## options
options中支持除ajax和data之外的datatable options中的取值，具体可选取值请参考以下地址
[datatable options](https://www.datatables.net/manual/options)

> 因为考虑到datatable中数据的变化，所以把和数据相关的ajax和data属性合为dtData这个props，这样在react组件中使用和判断变化时会比较方便。

## dtData
dtData代表了datatable中取值的方式，有以下4种：
### ajax
通过ajax的方式传输，可以定义各种参数和处理方法，配合option可以实现后端分页。
```js
dtData = {
  // 标识 method 为ajax
  _method: "ajax",
  // 请求的url
  url: url,
  // 对request data的处理
  data: function (d) {
      $.extend(d, postData);
      return JSON.stringify(d);
  },
  // 请求的方法
  type: "post",
  // contentType
  contentType: "application/json; charset=utf-8",
  // 对response data的处理
  dataSrc: "data"
}
```
更多的用法可以参考[JQuery ajax](http://api.jquery.com/jQuery.ajax/).

### data
直接给datatable传输数据。
```js
dtData = {
  // 标识 method 为 data
  _method: "data",
  // 数据
  data: [
    {
      "id": 0,
      "name": "john",
      "sex": "M"
    }
  ]
}
```


### url
简易的ajax方式，只需要传输一个url，默认get方法传输。
```js
dtData = {
  // 标识 method 为 url
  _method: "url",
  // 数据
  url: "/api/data.json"
}
```

### func
更自由的处理方式，参考[Datatable ajax](https://datatables.net/reference/option/ajax)
```js
dtData = {
  // 标识 method 为 url
  _method: "function",
  // 数据
  func: function (data, callback, settings) {
    callback(
      JSON.parse( localStorage.getItem('dataTablesData') )
    );
  }
}
```

## columns
提供Datatable的列信息，使用参考[Datatable columns](https://datatables.net/reference/option/columns)

## events
绑定在Datatable中的事件，使用方法如下：
```js
[
  {
    // 事件类型
    "type": "click",  
    // 事件触发的元素
    "scope": "tbody tr td",
    // 处理函数
    "func": function(){}
  }
]
```

## className
提供组件DOM的Class。

## id
提供组件DOM的ID。

> 插件中识别组件的方法是 先判断是否有ID，再判断是否有className，两者都没有就使用"table:eq(0)"来获取。建议给每一个table加ID。

## hasCheckOptionsChange
取值为布尔型，为true则在判断组件更新时，对options 这个props的值也进行判断，如果options的值变化，组件也会更新。  
默认取值false

## hasOptimizeDisplay
取值为布尔型，为true则在渲染组件时进行一些效果优化，比如dtData类型为data时，判断data的长度，小于10则不显示页码按钮等。  
默认取值false
