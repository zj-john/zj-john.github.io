---
title: datatable 后端分页处理
categories:
  - tips
tags:
  - datatable
  - serverside
toc: true
date: 2017-10-31 17:37:57
---
> 问题：datatable 后端分页处理

对于datatable来说，即使对大数据量的处理，如果不涉及状态数据（比如有实时要求）且后端接口速度够快的话，其实不用做后端分页也可以。在option中使用["deferRender": true](http://datatables.club/example/ajax/defer_render.html)，基本上能达到较快的渲染速度。
```JavaScript
$(document).ready(function() {
   $('#example').DataTable( {
       "ajax": "data/arrays.txt",
       //当处理大数据时，延迟渲染数据，有效提高Datatables处理能力
       "deferRender": true
   } );
});
```
但是，真的碰到了上述情况，那么后端分页就是必须的了。下面来看：
<!-- more -->

# 解决办法
[官网](https://datatables.net/manual/server-side) 、 [中文网](http://datatables.club/manual/server-side.html)镇贴.

直接上代码，如下。注意有一些key的值是datatable规定好的，具体的服务器参数和返回的参数可以在上述文档中找到，不再赘述。
```JavaScript
let {url, data, type} = options.ajax;
// 开启后端分页
options.serverSide = true;
options.ajax = {
    url: url,
    data: function ( d ) {
      // d中包含了当开启了服务器模式时，DataTables 会默认发送到服务器的参数
      // 可以对d中的数据进行处理，来适配后端的需求
      // 删除关键字搜索 即searching:false
      delete d.search;
      let _order = d.order[0];
      let column = d.columns[_order.column].data;
      // 只允许对一列数据进行排序，排序的column从index改为key
      d.order = {
        column: column,
        dir: _order.dir
      };
      // 只有一列排序，columns的值将用不到 所以删除
      delete d.columns;
      // data中存放了要post的数据
      $.extend( d, data );
      return JSON.stringify( d );
    },
    type: type,
    contentType: "application/json; charset=utf-8",
    // 从后端返回的数据也可以处理为自己想要的格式
    dataSrc: function ( json ) {
      json.recordsFiltered = json.recordsTotal;
      return json.data;
    }
}
// init datatable
element.DataTable(options);
```

此代码对应的req和res契约如下：
```Javascript
req:{
  // form 数据，相当于上述代码的post data
  "id":"10002",   
  "name": "hotel_product_search",

   // 以下为table信息 为分页所传的数据
  "length": 10 , // table每页展示数量
  "start": 0, // 第一条数据的起始位置，比如0代表第一条数据
  // 排序 只支持单排序，asc：升序 desc：降序，这个值在上述代码中做过特殊处理。
  "order":{column: "id", dir: "asc"}
  "draw":1  // 这个值前端用于判断ajax的顺序 无需做处理。
}

res:{
  "success": true,
  "msg": "",
  "draw": 1 // 前端传输的值
  "recordsTotal": 10054 //数据库里总共记录数，该值会显示在datatable的页脚位置
  "data": [ // 具体信息
    {
      "id": "100402",
      "name": "hotel_product_search",
    }
  ]
}
```
