---
title: 使用datatable插件，一次点击发送两次请求
categories:
  - tips
tags:
  - react
  - datatable
  - serverSide
toc: true
date: 2018-01-25 11:12:49
---
> 问题： 使用datatable插件，一次点击发送两次请求

场景是通过form改变查询条件，然后使用datatable插件，通过向后端请求url的方式获取数据，并且开启serverSide模式。  
问题是会出现一次form提交触发两次对后端接口的请求,而且两次请求有差异，最终导致展示的数据不正确。  
react中写法如下，
```js
componentWillUpdate(nextProps) {
    ......
    if (needUpdate) {
        let element = $(this.refs.table);
        if ($.fn.DataTable.isDataTable(element)) {
            element.dataTable().fnClearTable();
            element.dataTable().fnDestroy();
        }
        this.datatable(element, nextProps);
    }
}
```

<!-- more -->

## 解决方案
查下来，首先排除了form上的问题，定位在datatable这个组件上。  
最终的解决方法是：
```js
componentWillUpdate(nextProps) {
    ......
    if (needUpdate) {
        let element = $(this.refs.table);
        if ($.fn.DataTable.isDataTable(element)) {
            // element.dataTable().fnClearTable();
            element.dataTable().fnDestroy();
        }
        this.datatable(element, nextProps);
    }
}
```

去掉了fnClearTable的调用，在datatable上看[文档](https://datatables.net/reference/api/clear())说明如下：
>This method should not be used when serverSide is enabled. When server-side processing is enabled, the data should be deleted from the data store (i.e. an Ajax request to the server) and then draw() called. Deleting the client-side data will have no effect when server-side processing is enabled since the data would simply be restored on the next draw if the data is not removed from the server.

也就是在serverSide模式下，应该不使用fnClearTable方法，那么fnClearTable这个方法是否会向后端发起一个新请求来delete数据，导致两个请求发出，到达的顺序导致页面显示时好时坏，还得进一步查一查。
