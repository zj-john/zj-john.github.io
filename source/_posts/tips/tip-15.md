---
title: select添加placeholder
categories:
  - tips
tags:
  - select
  - placeholder
toc: true
date: 2017-11-09 20:27:59
---
> 问题：select添加placeholder

select默认是没有placeholder这个属性的，但是又希望加一个类似于placeholder的效果，该怎么做呢？

<!-- more -->
# 解决方案
[最佳解决方案](https://stackoverflow.com/questions/5805059/how-do-i-make-a-placeholder-for-a-select-box/8442831)：
```JavaScript
<select>
    // selected :让选项默认选中，出现在select当前框中
    // disabled ：使此项不能被当做有效项使用
    // display:none : 让select选中其它值后，placeholder项不再出现
    <option value="" disabled selected style='display:none;'>placeholder的内容</option>
    <option value="option1">Option1</option>
</select>
```
效果如下：
<select style="width:200px;">
    <option value="" disabled selected style='display:none;'>请选择...</option>
    <option value="option1">Option1</option>
    <option value="option2">Option2</option>
</select>

angular中：
```JavaScript
<select name="type"
  ng-model="user"
  ng-options="t.id as t.name for t in user_list"
>
    <option value='' disabled selected style='display:none;'> 请选择用户...</option>
</select>
```
