---
title: datatable通过post ajax方式获取源数据
categories:
  - tips
tags:
  - datatable
  - post
  - ajax
date: 2017-10-18 10:54:55
---
> 问题：datatable通过post ajax方式获取源数据

<!-- more -->

Datatable大概是最常用的table插件了，其中ajax是获取源数据最常用的方式。Datatable ajax的一般使用同jQuery的ajax，非常方便。但是如果通过post方式传输数据，参数的使用方式上就有区别。

[官网](https://datatables.net/reference/option/ajax)镇贴.

解决办法：
```JavaScript
// url是api 接口；data是传参
let {url, data, type} = options.ajax;
ajax = {
    url: url,
    data: function ( d ) {
      // $.extend：拷贝、合并
      // d:Datatables构造的请求参数，如果开启了服务器模式( serverSideOption ) 这个还会包含服务器请求的一些参数
      $.extend( d, data );
      return JSON.stringify( d );
    },
    type: type,
    contentType: "application/json; charset=utf-8",
    dataSrc: "data"
}
```

<hr/>

##### datatable ajax支持三种参数说明：
* string(url)   
  指定返回数据的url。Datatables默认接收的是一个属性为data，如果你返回的数据不是这样， 你需要使用 ajax.dataSrc来处理。   
  只能用于Get请求。

```Javascript
//data.json返回数据格式如下
{
    "data": [
        ["Tiger Nixon", "System Architect", "Edinburgh", "5421", "2011/04/25", "$3,120"],
        ......
    ]
}
$('#example').DataTable( {
    "ajax": "data.json"
} );
```

* object(类似于ajax)  
其中以下3个参数有特殊用法。

data的用法：  
```Javascript
// 添加一个静态值，来提交额外的参数.不允许变量
$('#example').dataTable({
  "ajax": {
    "url": "data.json",
    "data": {
        "user_id": 451
    }
  }
});
// 添加额外的参数发送到服务器(注意：下列方式不适用于服务器模式)（函数没有返回）
// 开启服务器模式： serverSide：true；此时排序、搜索、分页由datatable传参，后端处理
$('#example').DataTable({
  "ajax": {
    "url": "data.json",    
    "data": function ( d ) {
      // 额外传参是{extra_search: value}
        d.extra_search = $('#extra').val();
    }
  }
});

//添加额外的参数发送到服务器(适用服务器模式)（函数有返回）
$('#example').DataTable({
  "serverSide":true,
  "ajax": {
    "url": "data.json",
    "data": function (d) {
      return $.extend({}, d, {
        "extra_search": $('#extra').val()
      });
    }
  }
});
//以json格式提交
$('#example').dataTable({
  "ajax": {
    "url": "data.json",
    "contentType": "application/json",
    "data": function ( d ) {
      return JSON.stringify( d );
    }
  }
});
//动态传参数(在每次Datatables请求服务器时都可以动态计算提交的参数)
//初始化表格
var oTable = $("#example").DataTable({
    ajax: {
        url: "dataList.action",
        data: {
            args1: "我是固定传参的值，在服务器接收参数[args1]"
        }
    }
});

//当你需要多条件查询，你可以调用此方法，动态修改参数传给服务器
function reloadTable() {
    var name = $("#seName").val();
    var admin = $("#seAdmin").val();
    var param = {
        "obj.name": name,
        "obj.admin": admin
    };
    oTable.settings()[0].ajax.data = param;
    oTable.ajax.reload();
}
```
dataSrc的用法：  
当ajax或server-side process时，datatable默认在获取数据中找“data”属性作为源数据。如果需要换这个属性值的话，有以下2种方式。  
<b>string：</b>

```Javascript
// 使用 dataSrc属性把 data改为 tableData （比如： { tableData: [ ...data... ] } ）
$('#example').dataTable({
  "ajax": {
    "url": "data.json",
    "dataSrc": "tableData"
  }
});

// 通过 dataSrc 设置为空字符串，不从数组对象里获取，而是从数组里获取 （比如： { [ ...data... ] } ）
$('#example').dataTable( {
  "ajax": {
    "url": "data.json",
    "dataSrc": ""
  }
});
```

<b>function:</b>  

```Javascript
$('#example').dataTable( {
  "ajax": {
    "url": "data.json",
    "dataSrc": function ( json ) {
      for ( var i=0, ien=json.data.length ; i<ien ; i++ ) {
        json.data[i][0] = '<a href="/message/'+json.data[i][0]+'">View message</a>';
      }
      return json.data;
    }
  }
} );
```


success的用法：  
在datatable中不要使用success函数。因为datatable内部有使用success这个函数，如果自定义内部函数会被覆盖掉。如果需要处理返回值，可以使用dataSrc来处理。

* function(自定义)

```Javascript
$('#example').DataTable({
  // data:发送给服务器的数据
  // callback: 必须被执行，DataTables才能获取到数据;且将返回的数据作为callback()的唯一参数
  // settings: Datatables的设置对象
  "ajax": function (data, callback, settings) {
    callback(
      JSON.parse( localStorage.getItem('dataTablesData') )
    );
  }
});
```
