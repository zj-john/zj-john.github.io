---
title: Angular1 笔记
categories:
  - notes
tags:
  - Angular1
toc: true
date: 2018-03-16 15:19:42
---

## Angular1学习记录

整理文件夹的时候发现了蛮早之前写的Angular1的笔记，搬到博客来。

<!-- more -->

### 基本
```js
// 原生方法替代
$window.alert();
$eval("20+1|number:0")
// 模板
// 允许空值、null、undefined
{{ tmp }}
// 允许函数，需要返回值
{{ temp() }}
// 定义module
var module = anguler.module("a",[]);
// 注入控制器
a.controller('aCtrl',['$scope',function($scope)(){
	$scope.text = "hello world"
}]);
```


### 模板
```js
// 方式1
<script type="text/ng-template" id="tplbase">……</script>
// 方式2
<div ng-include src="tplbase" ng-controller="CtrlTest"></div>
```

### 样式
```html
<!-- $scope.a = true; -->
<!-- $scope.b = false; -->
<div ng-class="{'class1':a,'class2':b}"></div>
<!-- 等价于 -->
<div class="class1"></div>

<!-- switch -->
<ul ng-switch on={{ switchvalue }}>
	<li ng-switch-when="1">a</li>
	<li ng-switch-when="2">b</li>
	<li ng-switch-default>default</li>
</ul>
```

### Form
* $pristine: 表单或控件是否未输入过
* $dirty: 表单或控件是否已输入过

> checkbox/radio 不具备控件验证功能

```html
<!-- 错误信息展示 -->
<form name="testForm">
<input type="text" name="testInput"/>
<label ng-show="testForm.testInput.$error.required">这是必填项</label>

<input type="email" name="emailInput"/>
<label ng-show="testForm.emailInput.$error.email">不符合email格式</label>
</form>

<input type="checkbox" ng-model="a" ng-true-value="1" ng-false-value="2" />

<input type="radio" ng-model="b" value="1">
<input type="radio" ng-model="b" value="0">

<select ng-model="a" ng-options="txt for txt in data">
  <option value="">--请选择--</option>
</select>

<!-- 分组 -->
<select ng-model="a" ng-options="txt.value as txt.text group by txt.key for txt in data">
  <option value="">--请选择--</option>
</select>
```

## 过滤器
```html
<li ng-repeat=" stu in data | orderBy: '-score' | limitTo: 3"></li>

<!-- 在score列匹配 -->
{{ data | filter:{score:90}}}

<!-- 所有列匹配 -->
{{ data | filter:{$:90}}}

<!-- 自定义过滤器 -->
<li ng-repeat=" stu in data | filter:selfmade "> </li>
<script>
module.filter('selfmade',function(){
   //e:数据,type:传参
   return function(e, type){
     return result
   }
});
</script>

<!-- 类似datatable排序 -->
<!-- 表头 -->
<li>
    <span ng-click="title:'name';desc=!desc">姓名</span>
    <span ng-click="title:'sex';desc=!desc">性别</span>
    <span ng-click="title:'age';desc=!desc">年龄</span>
</li>
<!-- 内容 -->
<li ng-repeat=" stu in data | orderBy : title : desc">
    <span>{{ stu.name }}</span>
    <span>{{ stu.sex }}</span>
    <span>{{ stu.age }}</span>
</li>
```

### 搜索
```html
<input ng-model="key" type="text" placeholder="key world" />
<!-- filter功能使用来创建一个新的数组 -->
<li ng-repeat="stu in data | filter: {name : key}"></li>
```

### 作用域
```js
$scope.name=""
//监听属性的变化
$scope.$watch('name',function(){

})
// $rootScope :顶级作用域
```

> 子作用域和父作用域属性值重合时，先访问子作用域的对象，找不到再访问父作用域对象


通过作用域中的事件传播数据的功能十分有限，只能调用Angular中的$emit和$broadcast方法。
* $broadcast(eventname, data):  
将事件从父级作用域传播至子级作用域

* $emit(eventname, data)：  
将事件从子级作用域传播至父级作用域

* $on(eventname,function(event,data){})：  
  在作用域中监控传播来的事件并获取相应的数据

### 依赖注入

```js
//此类内联写法使得代码支持压缩
module.controller("controller", ['$scope',function($scope){
	//控制器代码
}]);

// 等价于

module.config(function($controllerProvider){
    $controllerProvider.register('controller',['$scope',function($scope){
        //控制器代码
    }])
})
```
>后者是实际执行的本质：创建一个控制器时，，实际上在config函数中调用controllerProvider服务的register方法，依赖注入

一般流程：  
config -> $provide服务 -> provider,factory,service,value 方法，
创建一个自定义的依赖注入对象，最上层可以直接使用return方法


#### factory
```js
angular.module('ngapp',[])
    .factory('$out',function(){
        return {
            // 服务代码
        }
    })
    .controller('aCtrl',function($scope,$out){
        //处理器代码
    });
```

>通过factory方法创建的服务代码，完全可以使用service方法来代替。但如果service方法创建的服务，使用factory方法代替时，需要先将service中的回调函数单独创建一个自定义函数，然后在factory的回调函数中使用关键字new实例化这个函数，并返回实例化后的对象。  
constant：返回常量，可用于创建配置数据，可以注入配置函数config中  
value：返回常量，创建对象和函数，不能注入到config中

>$inject 方式的依赖注入的服务名和顺序必须与函数在构造时的参数名和顺序完全一致

#### $inject api
* injector.has('$方法name')  
注册列表中查找相应服务，有返回true  

* injector.get('$方法name')  
var a = injector.get('$方法name') 获得服务的实例对象后可以直接调用 a.test()  

* injector.invoke('自定义fun')  
执行一个自定义函数（立即触发）

### ng-view
切换视图模板
```html
<div ng-view></div>
<script type="text/javascript">
    module.config(['$routeProvider',function($routeProvider){
        $routeProvider
            .when("/",{
                controller:"aCtrl",
                template:"aTemplate"
            })
            .when("/book",{
                controller:"bCtrl",
                template:"bTemplate",
				publicAccess:true  // 对外公开  全局
            })
            .otherwise({
                redirectTo:"/"
            })
    }]);
</script>
```

### 服务
单例对象：每一个应用中只会被实例化一次  
$scope/$http/$window/$location

### 装饰器
```js
angular.module('a',[])
	.config(function($provide){
		$provide.decorator('$student', function($delegate)(){
			return $delegate
		})
	})
```

### $http
刷新页面后缓存内容都会丢失  

```js
// 先调用config方法，注入$httpProvider,设置默认headers
anguler.module('module',[])
	.config(function($httpProvider){
		$httpProvider.default.headers.post = {
			'Content-Type': 'application/x-www-form-urlencoded'
		}
	})
```

```js
$http.get(url.[data],[config])
  .success(data,status,headers,config)
  .error(data,status,headers,config)
// 等价于
$http({}).then(fn1成功, fn2失败)
```

```js
$http({
	method:
	url:
	data: // POST 、PUT使用
	params: // GET方法，追加到url后面，传给服务端
	transformRequest:  // 用于请求体头信息和请求体进行序列化转换，并生成一个数组发给服务端
	transformResponse: // 对响应体头信息和响应体进行反序列化的转换，实质是解析服务器发送来的被序列化后的数据
	cache:  // 是否对http请求返回的数据进行缓存，true为缓存
	timeout: // 延迟发送http请求，单位是ms
}).success()
```

### 缓存服务：$cacheFactory
```js
 // 一般用法
$cacheFactory(key,[option])
$cacheFactory.get(key)

// 先建一个cache
var cache = $cacheFactory("test")
 //缓存信息 大小等
console.log(cache.info())
// 添加
cache.put("key1","value1")
cache.get("key1")
// 全删和清空所有引用 removeAll destory
cache.remove("key1")  

//把http的返回值存储在以$http为key的缓存中
var cacheHttp = $cacheFactory.get($http);
$http().then()  
```

### $resource
优于 $http

```html
<!-- 需要格外引入： -->
<script src="../Script/angular-resouce.min.js"></script>
```
注入时使用['ngResource']
```js
var obj = $resource('url');
obj.get(params,successFn,errorFn)
```

* get类：get/query
* 非get类：save/delete/remove


### $promise
```js
angular.module("m",[])
	.controller("c",function($scope,$q){
		var defer = $q.defer();
		var promise = defer.promise;
		promise.then(successCallback,errorCallback,notifyCallback);
		defer.notify(); // 通知
		defer.reject(); // 拒绝
		defer.reslove(); // 完成
	})
```
$http中使用$promise
```js
var defer = $q.defer();
$http.get()
.success(function(){
	defer.resolve(data)
})
.error(function(data){
	defer.reject(data)
})
return defer.promise;
```

### directive
```js
var app = anjular.module("a",[])
a.directive('tsHello',function(){
	return{
		restrict: "EAC"  // E：标签 A：属性 C：类别 M：注释 可以使用的方式
		template:'<h2>Hello World</h2>'
		templatUrl //template id
		replace：true // true 将模板中的内容替换指令标记 <h2></h2>，false不替换指令标记，将内容插入指令标记中，原内容清空，默认为false  
	}
})
```

四种使用方式：
```html
<ts-hello></ts-hello>
<div ts-hello></div>
<div class="ts-hello"></div>
<div data-ts-hello></div>
```
#### 属性
* transclude  
取值：true/false   
利用ng-transclude方式替换指令元素中的内容，如果是纯文本，加span标签

```html
<script type="text/ng-template" id="tpl">
  <div ng-transclude class="tip"></div>
</script>
```

```js
.directive("tsTplscript",function(){
	return{
		restrict:'EAC',
		templaateUrl:'tpl',
		transclude:true
	}
});
```

```html
<!-- text = "Hello" -->
<ts-tplscript>{{ text }}</ts-tplscript>

<!-- 等价于 -->
<div ng-transclude class="tip">
  <span>Hello</span>
</div>
```

* link  
link属性的值是一个函数，在该函数中可以操纵dom元素

```js
.directive("tsTplscript",function(){
	return {
		restrict:'EAC',
		templaateUrl:'tpl',
		link:function(scope,iEle,iAttr){
			// iEle 父级元素
			iEle.bind('click',function(){
				scope.$apply(……);
				// iAttr.$$element 与属性对应的元素集合
				iAttr.$$element[0].disabled = true;
			})
		}
	}
})
```

* compile  
与link不可同用

* scope  
取值：true/false   
false时：不创建新的作用域，父与子作用域的数据完全相同，两方相互关联，一起变化。  
属性值还可以设置为json：绑定策略：@ = &

* require  
创建子指定时添加，格式：require：'^?myParentDirective' ,与controller一起使用

* controller  
在创建父指令时添加，可以被实例的对象所继承，与require一起使用

### $location服务
#### 只读方法：
absUrl, protocol, host, port  

#### 读写类方法：
```js
// http://1.html#?a=1&b=2#hash
$location.url()
// ??a=1&b=2#hash
$location.hash()
// hash
$location.search()
//{"a":"1","b":"2"}
```

#### 事件
* $locationChangeStart :  
参数：current 当前地址 previous：上一个地址

* $locationChangeSuccess  
路由模式：标签（hashbang）和HTML5模式

### 常见问题：
* setTimeout:->$timeout()
* 防止应用闪烁: ng-cloak
> AngularJS 应用在加载时，文档可能会由于AngularJS 代码未加载完而出现显示 AngularJS 代码，进而会有闪烁的效果， ng-cloak 指令是为了防止该问题的发生。

```html
<element ng-cloak></element>
```

* ng-repeat:  
如果有过滤器，不能使用$index索引定位元素

* np-repeat:  
track by加快加载速度 ng-repeat="user in users track by user.id"

* 解决单击事件的冒泡现象：$event.stopPropagation()

* 释放多余的$watch监测函数：  
定义$watch事件内容为空

* 利用ng-show代替ng-if:  
因为ng-if下的元素是一个子级作用域，无法直接在scope中修改，需要绑定的方式是ng-model="$parent.b"

* 在浏览器标题栏中会在一瞬间看到如Google Phone Gallery:{{query}}这样的信息，但其实这不是你希望的（这时angular的数据绑定还没有起作用，就造成了前面的原始数据显示，和后面起作用后的正确显示），对此一个更好的处理方法是使用ngBind或者ngBindTemplate命令，这使得在加载器无关的信息对用户来说是不可见的：
```html
<title ng-bind-template="Google Phone Gallery: {{query}}">Google Phone Gallery</title>
```

* 把ng-src指令替换为原来的src属性:  
使用诸如Firebug之类的工具或者Chrome浏览器的Web开发组件观察web访问日志，可以观察到如/app/%7B%7Bphone.imageUrl%7D%7D 或者/app/{{phone.imageUrl}}这样的无效请求，这些无效请求就是浏览器在Angular还没有正确初始化和进行数据绑定（注入）时，构建img标签时发出的，这使得图片不能正确显示。
