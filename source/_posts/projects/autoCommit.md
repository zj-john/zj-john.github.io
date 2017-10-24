---
title: 一个向GitHub定期自动提交commit的小脚本
tags:
  - github
  - node
  - schtasks
  - contribution
categories:
  - projects
date: 2017-09-05 16:32:52
toc: true
---
> 偷懒是人类进步的阶梯。  

作为一只重度加班狗和强迫症患者，没有时间每天更新GitHub，但是又不能忍受contributions上的空白，所以只能动动脑子，搞点取巧的东西。

- **目的**：定期自动向GitHub发起commit  
- **工具**：node, git, schtasks(win), crontab(linux)  
- **难度等级**: ★
- **项目地址**: https://github.com/zj-john/autoCommit

<!-- more -->

##### 第1步：自动提交git  
以下三行代码可以完成一次提交
```
git add --all
git commit -m "update"
git push origin master

```

node中写法如下：  

```javaScript
//git目录下新建index.js

const child_process = require("child_process");

//定义一个执行cmd的函数
const execCMD = function(cmd){
  child_process.exec(cmd, function(error, stdout, stderr) {
    if(error) {
        console.error('error: ' + error);
        return;
    }
    console.log('stdout: ' + stdout);
    console.log('stderr: ' + stderr);
  })
}

//用&&连接多条CMD命令
const cmd = 'git add --all :/ && git commit -m "update" && git push origin master';
execCMD(cmd);
```

cmd中执行

```
node index.js
```
可以看到执行了一次commit。至此，第一步完成


##### 第2步：更新目录下内容  
为了让git每一次都有可以提交的commit，需要自动更改目录下的内容。
这一步骤大家可以自由发挥，比如建立一个文本文件，内容为当前时间，在每次执行commi前，更新其中的内容等。  
我这里是写了一个爬虫，在定期commit的同时获取一些信息保存下来，不需要的同学可以直接跳过以下部分了。

以下操作是从 https://github.com/explore 页面爬取本周星数最高，fork最多的git项目，保存到一个txt文本中



```javaScript

//爬虫
const crawler = require('crawler');
// 时间处理插件，生成txt文件名称时使用
const moment = require('moment');
const crawlerUrl = "https://github.com/explore";

const crawlerMeta = new crawler({
  maxConnections: 1,
  callback: (error, res, done) => {
    if (error) {
      export_to_text(error, "./error/" + moment().format("YYYY-MM-DDThh-mm-ss") + '.txt');
    } else {
      let $ = res.$;
      // 判断是否重定向, 如果页面数量超标的话则会重定向到其他页面
      if (res.request.headers.referer == undefined) {
        var content = [];
        $('.d-md-flex').find('li').each((i, item) => {
          var $item = $(item);
          var name = $item.find('div').find('a').text();
          var desc = $item.find('div').find('p').text();
          content.push({
            "name": name,
            "desc": desc
          });
        });
        export_to_text(JSON.stringify(content), './file/' + moment().format("YYYY-MM-DDThh-mm-ss") + '.txt');
      }
    }
    done();
  }
})

```

##### 第3步：建立计划任务


```Javascript
const child_process = require("child_process");

//每8h小时执行一次index.js
const execCMD = function(){
  child_process.exec('schtasks /create /tn "auto-commit" /tr "cmd /c node '+ __dirname + '\\index.js" /sc hourly /mo 8 /f', function(error, stdout, stderr) {
    if(error) {
        console.error('error: ' + error);
        return;
    }
    console.log('stdout: ' + stdout);
    console.log('stderr: ' + stderr);
  })
}

execCMD();
```


##### 后记：
以上3步基本完成脚本所需所有功能，需要源码的同学请在 https://github.com/zj-john/autoCommit 自取。  

因为工作的电脑是win7的，目前只实现了windows下的自动commit。在mac或linux下使用的小伙伴，请自行修改代码吧。 欢迎贡献代码，一起进步。
