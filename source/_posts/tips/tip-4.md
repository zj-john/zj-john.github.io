---
title: Git ignore 修改后不生效
categories:
  - tips
tags:
  - git
  - ignore
date: 2017-09-20 11:28:59
toc: true
---
> 问题：Git ignore 修改后不生效

<!-- more -->

.gitignore文件中新加了忽略规则，发现并未生效。   
原因：.gitignore只能忽略没有被追踪的文件，如果文件已经被纳入了版本管理中，则修改无效。  
解决：先把本地缓存删除（改变成未被追踪状态），然后再提交。

详细理解和说明可以参考.gitignore[官方](https://git-scm.com/docs/gitignore)解释。
具体操作如下：
```
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```

>tips: gitignore

忽略文件的原则是：  
- 忽略操作系统自动生成的文件，比如缩略图等；
- 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
- 忽略带有敏感信息的配置文件，比如存放口令的配置文件。

如果不知道有哪些文件需要忽略，可以参考[ignore template](https://github.com/github/gitignore),里面根据语言列出了对应ignore的模板



以下语法应用在.gitignore文件中（若根目录下没有，可手工新建）。  

> windows新建时提示必须添加文件名，无法创建的解决办法:1.使用文件名.gitignore.来创建(末尾有个".");2.使用notepad等文本编辑器保存

```
# 此为注释 – 将被 Git 忽略
*.sample 　　    # 忽略所有 .sample 结尾的文件
!lib.sample 　　 # 但 lib.sample 除外
/TODO 　　       # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
foo/ 　　        # 忽略 foo/ 目录下的所有文件
foo              # 匹配所有名为foo的文件或目录
**/foo/bar       # 匹配所有foo一级目录下名字为bar的文件或目录
/log/*           # 屏蔽log下所有文件，不包括log文件夹本身
doc/*.txt 　　   # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```
