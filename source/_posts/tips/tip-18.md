---
title: angular中rowspan样式
categories:
  - tips
tags:
  - angular
  - rowspan
  - ngTable
toc: true
date: 2017-12-04 11:30:32
---
> 问题：angular中rowspan样式  

本文探讨了angular框架中原生table和ngTable中rowspan的用法。

<!-- more -->
# 原生的table使用方法：
```HTML
<table border=1>
  <thead>
    <tr>
       <th>Group</th>
       <th>Name</th>
       <th>Gender</th>
       <th>Status</th>
    </tr>
  </thead>  
  <tbody ng-repeat="group in groups">
    <tr ng-repeat="member in group.members">
      <td rowspan="3" ng-if="!($index%3)">{{group.name}}</td>
      <td>{{ member.name }}</td>
      <td>{{ member.gender }}</td>
      <td rowspan="3" ng-if="!($index%3)">{{group.status}}</td>
  </tbody>
</table>
```

```Javascript
function Ctrl($scope) {
  $scope.groups = [
    { name: 'Employees',
      members: [
        { name: 'John', gender: 'M'},
        { name: 'Jane', gender: 'F'},
        { name: 'John', gender: 'M'}
      ],
      status: 'Active'
    },
    { name: 'Pets',
      members: [
        { name: 'Rex', gender: 'M'},
        { name: 'Lucy', gender: 'F'},
        { name: 'Rex', gender: 'M'}
      ],
     status: 'Active'
    }
  ];
}
```


# ngTable中使用方法：
## 一般用法
```html
<table ng-table="page.tableParams" class="table">
  <thead>
    <tr>
       <th>选择</th>
       <th>策略</th>
       <th>IP</th>
       <th>权重</th>
       <th>操作</th>
    </tr>
  </thead>
  <tbody ng-repeat="d in $data">
    <tr ng-repeat="line in d.line_list">
      <td class="text-center" rowspan="3" ng-if="!($index%3)" >
        <div class="checkbox checkbox-primary">
          <input id="akamai_source_select_{{ d.name }}" checked="false" type="checkbox" ng-model="d.checked" class="form-control">
          <label for="akamai_source_select_{{ d.name }}"></label>
        </div>
      </td>
      <td class="text-center" rowspan="3" ng-if="!($index%3)" sortable="'name'">{{ d.name }}</td>
      <td class="text-center">{{ line.ip }}</td>
      <td class="text-center">{{ line.weight }}</td>
      <td class="text-center">
        <button class="btn btn-warning btn-sm" ng-click="page.log(d.domain)" ng-if="line.enable">关闭</button>
        <button class="btn btn-primary btn-sm" ng-click="page.log(d.domain)" ng-if="!line.enable">开启</button>
      </td>
    </tr>
  </tbody>
</table>
```

```Javascript
vm.data = [
  {
    name:"ctslb-wspci-fq-peng",
    line_list:[
      {"enable":true , "weight":1.0, "ip":"1.1.1.1"},
      {"enable":true, "weight":1.0, "ip":"1.1.1.1"},
      {"enable":true, "weight":1.0, "ip":"1.1.1.1"}
    ]
  }
];
vm.tableParams = new NgTableParams({
  count : 10          
}, {
  dataset: vm.data
});
```

## ngTable中的问题
上面的写法中其实有个问题：对于ngTable来说，其实是不需要&lt;thead&gt;的，一般写法：
使用title属性来定义列标题。
```HTML
<table id="akamai_line_table" ng-table="page.tableParams" class="table">
  <tbody ng-repeat="d in $data">
    <tr ng-repeat="line in d.line_list">
      <td title="'#'" class="text-center" rowspan="3" ng-if="!($index%3)" sortable="'name'">{{ d.name }}</td>
      <td title="'type'" class="text-center" sortable="'line.type'">{{ line.type }}</td>
      <td title="'enable'" class="text-center" sortable="'line.enable'">{{ line.enable}}</td>
    </tr>
  </tbody>
</table>
```
但是在使用这种方法时会发现一个问题，就是：
<b>当有两个列需要rowspan时，如果使用title定义标题，第二个使用rowspan的列的标题会无法展示，后续的标题会前提，所以最终结果是最后一列无标题。</b>

>给自己：记得提个issue问下
