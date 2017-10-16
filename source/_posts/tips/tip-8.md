---
title: ngTable中使用全局搜索
categories:
  - tips
tags:
  - ngTable
  - filter
  - angular
date: 2017-10-13 12:12:46
---
> 问题：ng-table中使用全局搜索

在使用angular作为主体语言的项目中，一般选用[ngTable](http://ng-table.com/#/)来展示表格。
> jQuery中常用的datatable在angular中效果并不理想。在使用ng-repeat这种angular的方式渲染时，性能慢的出奇。换为后端defer的方式加载的话，性能会有提升，但是这种方式失去了angular数据响应式渲染的有点。

ngTable中的默认的search方式，是给每一列加自己的筛选。如下图：  
![](/images/ngTable.png)

这种过滤方式的优点是精准，但是缺点也很明显，无法做到全局匹配。如果想做到类似于datatable中全局搜索的效果应该怎么操作呢？  
直接上代码：
```Javascript
//html 部分
<table ng-table="page.tableParams" class="table table-bordered table-striped">
  <caption>
    <span class="pull-right">
      // 类似于datatable的 搜索框
      <input placeholder="Search" class="form-control input-sm" type="text" ng-model="page.keyword" ng-change="page.search()">
    </span>
  </caption>
  <tr ng-repeat="d in $data">
    <td title="'域名'" sortable="'domain'">{{ d.domain }}</td>
    <td title="'类型'" sortable="'is_internet'">{{ d.is_internet?"外网":"内网" }}</td>
  </tr>
</table>
<div ng-if="page.tableParams.total() == 0" class="text-center">
  No data found
</div>

//JS controllerAs: 'page'
vm.search = function(){
  vm.tableParams = new NgTableParams({
    count : 10          
  }, {
    dataset: $filter('filter')(vm.selected_data, vm.keyword);
  });
}
```
代码十分简单，不过有两个问题要注意下：
1. 为什么不在html中ng-repeat后直接filter  
一般来说，首先会使用ng-repeat="d in $data | filter:page.keyword"的方式进行过滤。但是这种方式在ngTable中行不通，因为这里的$data实际是ngTable做了分页后的数据，也就是currentPageData，所以filter只能过滤当前页面的内容。

2. controller中$filter的用法  
filter一般在html中用于格式化，在controller中也可以通过$filter使用。在使用中要了解filter原型，以便传参。另外，两者用法有所区别：  

```Javascript
//将12.45格式化为货币，使用自定义单位符号为 'CHY￥', 小数指定1位, 会执行四舍五入操作
{{ 12.45 | currency:'CHY￥':1}}

//currencyFilter：function(amount, currencySymbol, fractionSize)
$filter('currency')('12.45', 'CHY￥', 1);
// 或者 直接使用currencyFilter
currencyFilter('12.45', 'CHY￥', 1)
```
可以看出，html中除值外，其余参数使用":"来传参。controller中可以使用统计$filter或者单独filter来处理。  
常用内置filter：    
* currencyFilter（currency）：function(amount, currencySymbol, fractionSize)
* dateFilter（date）：function(date, format, timezone)
* filterFilter（filter）：function(array, expression, comparator)
* jsonFilter（json）：function(object, spacing)
* limitToFilter（limitTo）：function(input, limit)
* lowercaseFilter（lowercase）：function(string)
* uppercaseFilter（uppercase）：function(string)
* numberFilter（number）：function(number, fractionSize)
* orderByFilter（orderBy）：function(array, sortPredicate, reverseOrder)
