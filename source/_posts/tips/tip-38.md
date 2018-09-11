---
title: datatable 一些易混淆options说明
categories:
  - tips
tags:
  - object
  - deep copy
toc: true
date: 2018-02-07 20:28:42
---
> 问题： datatable 一些易混淆options说明


<!-- more -->

## 解决方案

###  加载
* language.loadingRecords

When using Ajax sourced data and during the first draw when DataTables is gathering the data, this message is shown in an empty row in the table to indicate to the end user the the data is being loaded. Note that this parameter is not used when loading data by server-side processing, just Ajax sourced data with client-side processing.

* language.processing

Text that is displayed when the table is processing a user action (usually a sort command or similar).

### 空数据

* language.zeroRecords

Text shown inside the table records when the is no information to be displayed after filtering.
Note that language.emptyTable is shown when there is simply no information in the table at all (regardless of filtering), while this parameter is used for when the table is empty due to filtering.

* language.emptyTable

This string is shown in preference to language.zeroRecords when the table is empty of data (regardless of filtering) - i.e. there are zero records in the table.
Note that this is an optional parameter. If it is not given, the value of language.zeroRecords will be used instead (either the default or given value).

### 信息展示
* lengthChange [boolean]

When pagination is enabled, this option will control the display of an option for the end user to change number of records to be shown per page. The options shown in the list are controlled by the lengthMenu configuration option.

* info [boolean]

When this option is enabled, Datatables will show information about the table including information about filtered data if that action is being performed. This option allows that feature to be enabled or disabled.

Note that by default the information display is shown below the table on the left, but this can be controlled using dom and CSS)

* paging [boolean]

DataTables can split the rows in tables into individual pages, which is an efficient method of showing a large number of records in a small space. The end user is provided with controls to request the display of different data as the navigate through the data. This feature is enabled by default, but if you wish to disable it, you may do so with this parameter.

* searching [boolean]

This option allows the search abilities of DataTables to be enabled or disabled. Searching in DataTables is "smart" in that it allows the end user to input multiple words (space separated) and will match a row containing those words, even if not in the order that was specified (this allow matching across multiple columns).
