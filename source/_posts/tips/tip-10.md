---
title: react-bootstrap-typeahead的使用
categories:
  - tips
tags:
  - typeahead
  - react-bootstrap-typeahead
  - react
date: 2017-10-17 10:27:28
toc: true
---
> 问题：react-bootstrap-typeahead的使用

<!-- more -->

最近开发react应用，用到的是[react-bootstrap](https://react-bootstrap.github.io/components.html#)来构建基础样式。但是用于input的typeahead组件，这个框架中没有提供。为了样式的统一，最终选用了[react-bootstrap-typeahead](https://github.com/ericgio/react-bootstrap-typeahead)这个typeahead控件。   
使用过程中遇到了以下几个问题，在此记录一下：
* 效果和[控件demo](http://ericgio.github.io/react-bootstrap-typeahead/)不符

 直接npm install --save react-bootstrap-typeahead后，仿照demo中的代码使用，发现展示效果和demo的样式差别很大，不是想要的效果。   
 通过对demo版本的比较发现，直接通过下列命令安装的版本是2.0.0-alpha.4版本，而GitHub中demo的版本是1.4.2。  
 <b>解决：如果需要使用demo中的效果，需要指定版本来安装。</b>

```Javascript
//wrong:"react-bootstrap-typeahead": "^2.0.0-alpha.4"
npm install --save react-bootstrap-typeahead

//correct
npm install --save react-bootstrap-typeahead@1.4.2

```

* query 与 response 内容不匹配，不显示option  
这个问题的表征是如果你搜索了"z_j",后端返回的是一个字典数组[{"name":'zj-john'}],此时labelKey是"zj-john",并不和z_j匹配，所以typeahead的可选择项为空。  
原因是"By default, the filtering algorithm only searches the field that corresponds to labelKey. However, you can pass an array of additional fields to search"  
<b>解决： 使用 filterBy={["query"]} 属性，增加匹配字段</b>

* 实现input不能输入，只能从select获取值  
插件虽然提供了onblur的方法，但是使用 onBlur 属性，对于keyboard选择支持良好，对click支持不佳。 click中先执行onBlur，再执行onChange，导致选中框被提前清空。 具体可参考以下issues：[112](https://github.com/ericgio/react-bootstrap-typeahead/issues/112)
[119](https://github.com/ericgio/react-bootstrap-typeahead/issues/119)
[127](https://github.com/ericgio/react-bootstrap-typeahead/issues/127)
[210]( https://github.com/ericgio/react-bootstrap-typeahead/issues/210)  
<b>解决：延时执行onblur </b>  

完整用法参考如下：
```Javascript
handleOwnerBlur = () => {
  //对于click事件，会先执行onBlur，再执行onChange，导致选中框被提前清空。所以延时0.5s
  setTimeout(()=>{
    if(this.refs.input_owner.getInstance().state.selected.length===0) this.refs.input_owner.getInstance().clear();
  }, 500);
}
handleOwner = query => {
    if (!query) {
        return;
    }
    this.setState({
        clearbutton: true
    });
    let url = config.base_url;
    let post_data = {
        "length": 20,
        "q": query,
        "type": "owner"
    }
    $.ajax({
        url: url,
        data: JSON.stringify(post_data),
        type: 'post',
        contentType: "application/json; charset=utf-8",
        success: function (data) {
          if (data.result) {
              data.data.map((item)=>{
                // 把检索字作为返回结果的一个字段，使得filter检索的时候可以检索到
                item.query = query;
              })
              this.setState({
                  owner_typeahead_options: data.data
              });
          } else {
              alert(data.message);
          }
        }.bind(this),
        error: function () {
            alert(data.message);
        }.bind(this)
    })
}
renderMenuItemOwner = (option, props, index) => {
    return (
      <div>
        {option.value}
        <div>
          <small style={{ "wordBreak":"break-all" , "whiteSpace": "initial" }}>{option.name}</small>
        </div>
      </div>
    );
}
<Typeahead
    ref="owner"
    name="owner"
    // 是否显示 clear btn(x图标)
    clearButton = {this.state.clearbutton}
    maxHeight = {300}
    maxResult = {20}
    options={this.state.owner_typeahead_options}
    labelKey={"name"}
    filterBy={["query"]}
    placeholder="请输入……"
    onInputChange={this.handleOwner}
    onBlur={this.handleOwnerBlur}
    selected = {[this.state.app_owner]}
    renderMenuItemChildren={this.renderMenuItemOwner}
/>
```
>tip:
1. 如果不是为了适配bootstrap的效果，可以选用其它星级更高的typeahead插件（应该也有更好的bootstrap的适配插件，暂时没找到）。
2. 即使typeahead的内容是后端API异步给到的，也建议使用&lt;Typeahead>而非&lt;AsyncTypeahead>。因为&lt;AsyncTypeahead>的props更少，且不好调试。&lt;Typeahead> + promise 完全可以替代&lt;AsyncTypeahead>
