---
title: jQuery checkbox 选中失效问题
categories:
  - tips
tags:
  - jQuery
  - checkbox
toc: true
date: 2017-11-08 14:11:24
---
> 问题：jQuery checkbox 选中失效问题  

做项目的时候遇到了需要用jQuery来选中checkbox的情况，但是发现多次使用时，首次生效，之后就无法选中的情况。
使用的代码如下：
```JavaScript
// 选中
$("[type=checkbox]").attr('checked', true);
// 取消选中
$("[type=checkbox]").removeAttr('checked');
```

<!-- more -->
# 解决方案
使用prop代替attr即可。
```JavaScript
// 选中
$("[type=checkbox]").prop('checked',true);  
// 取消选中
$("[type=checkbox]").prop('checked',false);  
```

# 原因
为什么使用prop和attr的效果不同呢，两者又有什么区别。我们来看下[官网](http://api.jquery.com/prop/)的说法：

The difference between <em>attributes</em> and <em>properties</em> can be important in specific situations. <strong>Before jQuery 1.6</strong>, the <code>.attr()</code> method sometimes took property values into account when retrieving some attributes, which could cause inconsistent behavior. <strong>As of jQuery 1.6</strong>, the <code>.prop()</code> method provides a way to explicitly retrieve property values, while <code>.attr()</code> retrieves attributes.

For example, <code>selectedIndex</code>, <code>tagName</code>, <code>nodeName</code>, <code>nodeType</code>, <code>ownerDocument</code>, <code>defaultChecked</code>, and <code>defaultSelected</code> should be retrieved and set with the <code>.prop()</code> method. Prior to jQuery 1.6, these properties were retrievable with the <code>.attr()</code> method, but this was not within the scope of <code>attr</code>. These do not have corresponding attributes and are only properties.

Concerning boolean attributes, consider a DOM element defined by the HTML markup <code>&lt;input type="checkbox" checked="checked" /&gt;</code>, and assume it is in a JavaScript variable named <code>elem</code>:

| Code        | Desc           |
| ------------- |:-------------:|
| <code>elem.checked</code> |<code>true</code> (Boolean) Will change with checkbox state |
| <code>$( elem ).prop( "checked" )</code> | <code>true</code> (Boolean) Will change with checkbox state|
| <code>elem.getAttribute( "checked" )</code> | <code>"checked"</code> (String) Initial state of the checkbox; does not change |
| <code>$( elem ).attr( "checked" )</code><em>(1.6)</em> | <code>"checked"</code> (String) Initial state of the checkbox; does not change |
| <code>$( elem ).attr( "checked" )</code><em>(1.6.1+)</em> | <code>"checked"</code> (String) Will change with checkbox state |
| <code>$( elem ).attr( "checked" )</code><em>(pre-1.6)</em> | <code>true</code> (Boolean) Changed with checkbox state |

According to the <a href="http://www.w3.org/TR/html401/interact/forms.html#h-17.4">W3C forms specification</a>, the <code>checked</code> attribute is a <em><a href="http://www.w3.org/TR/html4/intro/sgmltut.html#h-3.3.4.2">boolean attribute</a></em>, which means the corresponding property is <strong>true</strong> if the attribute is present at all—even if, for example, the attribute has no value or is set to empty string value or even "false". This is true of all boolean attributes.

Nevertheless, the most important concept to remember about the <code>checked</code> attribute is that it does not correspond to the <code>checked</code> property. The attribute actually corresponds to the <code>defaultChecked</code> property and should be used only to set the <em>initial</em> value of the checkbox. The <code>checked</code> attribute value does not change with the state of the checkbox, while the <code>checked</code> property does. Therefore, the cross-browser-compatible way to determine if a checkbox is checked is to use the property:

* <code>if ( elem.checked )</code>
* <code>if ( $( elem ).prop( "checked" ) )</code>
* <code>if ( $( elem ).is( ":checked" ) )</code>

The same is true for other dynamic attributes, such as <code>selected</code> and <code>value</code>.

简单的说，具有 true 和 false 两个属性的属性，如 checked, selected 或者 disabled 使用prop()，其他的使用 attr()。
具体列表如下：
![](/images/attr_prop.png)
