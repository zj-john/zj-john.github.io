---
title: react程序中 Failed to execute 'insertBefore' on 'Node'&& Cannot read property 'nextSibling' of null
categories:
  - tips
tags:
  - insertBefore
  - react
  - nextSibling
toc: true
date: 2018-01-08 11:22:53
---
> 问题：react程序中 Failed to execute 'insertBefore' on 'Node'&& Cannot read property 'nextSibling' of null

代码执行的功能是 在以下table中点击每一行右侧的红色btn，会弹出一个modal，modal中可以进行一些其它操作以及关闭modal。  

现在遇到的问题是打开modal时console中会报错（Failed to execute 'insertBefore' on 'Node'），modal可以打开，但是关闭时报错Cannot read property 'nextSibling' of null，且无法关闭。  

相关效果和代码如下：

<!-- more -->

## 图例
Table：

![](/images/webinfo_table.png)

点击按钮时，console中的报错：

![](/images/webinfo_error_insert.png)

虽然有报错，modal可以正常打开：

![](/images/webinfo_modal.png)

点击modal关闭按钮时的报错，且modal无法关闭：

![](/images/webinfo_error_next.png)

## 相关代码
main.js
``` js
import React, { Component } from 'react'
import { config } from 'ConfigRoot/config'
import Datatable from 'ComponentsRoot/tables/Datatable';
import ActionModalByRelation from '../../../../../modals/inAndOutByApp/ActionModalByRelation'

let operation_render = function (data, type, full, row, meta) {
    return `
<span style="margin-right: 10px" class="deviceAction tool_tip" data-toggle="tooltip" title="拉入\出操作"><img src="assets/img/pull.png" style="height: 17px;width: 17px"/></span>
<!--<span style="margin-right: 10px" class="containerAction tool_tip" data-toggle="tooltip" title="容器操作"><img src="assets/img/container.png" style="height: 23px;width: 23px"/></span>-->
        `
}

export  default  class ServerInGroup extends Component {
    constructor (props) {
        super(props);
        console.log('props', props)
        let page = this;
        this.columns = [
            {data: 'ci_code', title: '主机名', render: ci_render},
            {
                data: null,
                title: '操作',
                orderable: false,
                className: "menu",
                render: function () {
                    return `<span class="deviceAction tool_tip glyphicon glyphicon-resize-horizontal btn btn-xs" data-toggle="tooltip" title="拉入\出操作"></span>
                            <span class="containerAction tool_tip glyphicon glyphicon-cog btn btn-xs" data-toggle="tooltip" title="容器操作"></span>`
                },
                createdCell: function (td, cellData, rowData, row, col) {
                    $(td).find('.tool_tip').tooltip({
                        placement: 'top',
                    });
                    $(td).on('click', '.deviceAction', function () {
                        page.setState({
                            showModal: true,
                            modalInfo: rowData,
                        })
                    });
                }
            }
        ];
        let columns = {"columns": this.columns};
        this.state = {
            options: Object.assign(this.props.options , columns),
            showModal: false,
            modalInfo: null,
        }
    }

    hideModal = () => {
        this.setState({
            showModal: false
        })
    }

    render () {
        return (
                <div>
                    <Datatable
                      options={ this.state.options }
                      lengthChange={false}
                      searching={false}
                      autoWidth={false}
                      className="table"
                      style={{'width': '100%'}}
                      id="serverRelationInGroup"
                    >
                    </Datatable>

                    {this.state.showModal?
                      <ActionModalByRelation showModal={this.state.showModal} modalInfo={this.state.modalInfo} hideModal={this.hideModal} type="group"/> :
                      ''
                    }

                </div>
        )
    }
}
```
modal.js
```js
import React from 'react'
import { Button, Modal } from 'react-bootstrap'

export default class ActionModalByRelation extends React.Component {
    constructor (props) {
        super(props)
    }

    componentWillMount () {      
    }


    close = () => {
        this.props.hideModal()
    }


    render () {

        return (<div>
                    <Modal show={this.props.showModal} onHide={this.close} className="pull_in_modal">
                        <Modal.Header closeButton>
                            <Modal.Title
                                    className="text-center">Title</Modal.Title>
                        </Modal.Header>
                        <Modal.Body>
                            <div className="row">

                            </div>
                        </Modal.Body>
                        <Modal.Footer>
                        </Modal.Footer>
                    </Modal>
                </div>
        )
    }
}
```

## 解决方案

后来遇到了如下类似报错：  
NotFoundError: Failed to execute 'insertBefore' on 'Node': The node before which the new node is to be inserted is not a child of this node.

通过给组件外套一层<div>解决了。

```js
import Datatable from 'react-datatable-jq'

<div>
	<Datatable {...props}>
</div>
```
