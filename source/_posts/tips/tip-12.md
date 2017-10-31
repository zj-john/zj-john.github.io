---
title: select filters on ng-table from async call
categories:
  - tips
tags:
  - ngTable
  - select
  - filter
  - async
date: 2017-10-19 10:26:32
toc: true
---
> 问题：select filters on ng-table from async call

使用ngTable自带的filter功能，如果是select形式的filter，其filter-data需要在页面加载时完成。所以如果是异步加载filter-data的话，使用传统的angular的数据更新dom，是不生效的。如下：
```Javascript
// html:controller as page
<td data-title="'Test'" filter="{ test: 'select'}" filter-data="page.data">{{row.test}}</td>

// js
var vm = this;
// init
vm.data = [1, 2];
// ajax 更改data的值为[3, 4]后,html中select中的值还是[1, 2],并不会更新。
vm.data = changeDataAsync()
```

<!-- more -->

# 解决办法
[官网](http://ng-table.com/#/filtering/demo-select)
## 通过函数的方式解决
```JavaScript
// html:controller as page
<td title="'Test'" sortable="'test'" filter="{ test: 'select'}" filter-data="page.get_filter()">{{row.test}}</td>
//js
vm.get_filter = function(){
    var def = $q.defer();
    var list_filter = [];
    list_filter = changeDataAsync()
    def.resolve(list_filter);
    return def;
}
```

## 改写filter模板（未实际测试）  
使用filter-data="filterOptions"，此时默认模板：ng-options="data.id as data.title for data in $column.data"。这个模板的问题在于$column.data时固定的值，并不会随着$scope.filterOptions的更新而改变。  
更改模板为：ng-options="data.id as data.title for data in {{$column.data}}"，即可以更新。或者完全自己写一个模板替代。[样例](http://plnkr.co/edit/3tLixkFKYgpXfx04PbaD?p=preview)


>Tip：这种方式对于要制作联动filter的效果也是必不可少的。
