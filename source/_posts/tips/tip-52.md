---
title: Datatable源变化后rerender
categories:
  - tips
tags:
  - datatable
  - draw
toc: false
date: 2018-06-11 11:23:39
---

datatable的值还需要异步的数据来补充的话，要把异步的数据也插入到表格中去。  
一种方式是destory掉，然后在datatable()，这种方式会产生闪烁的效果。

更好的方式如下,利用drawCallback函数，再拿到新数据绘制到表格中。

例子中的方式是利用click事件来触发draw()，如果是异步数据重绘的话，需要设定一个标志位，在异步数据拿到之后改变这个标志位，使重绘停止。否则会不断重绘，造成死循环。

```js
var dataSet = [
	["yes", "Jose M. Smith"],
	["no", "Sidney D. McRae"],
	["yes", "Christina D. Turner"],
	["no", "Mary L. Engen"],
	["no", "Charles G. Campbell"],
	["no", "Alan J. Harris"],
	["no", "Kelly E. Litton"],
	["no", "Scott T. Cannon"],
	["no", "Judith C. Cockrell"],
	["yes", "Raymond C. Gordon"]
]

$("#js_table").DataTable( {
	"data": dataSet,
	"paging": true,
	"stateSave": true,
	"stateSaveParams": function (settings, data) {
		delete data.search;
	},
	"searching" : true,
	"lengthMenu": [ [25, 50, 100, -1], [25, 50, 100, "All"] ],
	"autoWidth": true,
	"order": [[ 0, 'asc' ],[ 1, "asc" ]],
	"drawCallback": function (settings) {
		var api = new $.fn.dataTable.Api(settings);
		api.rows().every(function () {
			var rowApi = this;
			$('.js_assign_element', this.node()).off("click").click(function () {
				var rowData = rowApi.data();

				if (rowData[0] == "yes") {
					rowData[0] = "no";
				} else {
					rowData[0] = "yes";
				}

				rowApi.invalidate().draw();
			});
		});
	},
	"columns": [
		{"orderSequence": [ "asc","desc" ], "render": function (data, type, row ){
			if ( type === 'display') {
				if (data === "yes") {
					return '<div class="js_assign_element element_assign __assigned"></div>'
				} else {
					return '<div class="js_assign_element element_assign"></div>'
				}
			}
			else
			{
				if (data === "yes") {
					return "assigned"
				} else {
					return "not assigned"
				}
			}
		}},
		{"orderSequence": [ "asc","desc" ]}
	]
})
```


# 参考：
* https://codepen.io/anon/pen/NXNEMJ?editors=0010
