---
title: CMD参数
categories:
  - tips
tags:
  - cmd
toc: true
date: 2018-02-23 20:01:39
---
> 问题： CMD参数

这个问题的由来是我有一个计划任务，里面使用cmd执行一个node程序。  
过两天发现任务没执行，排障的时候，运行计划任务脚本，cmd一闪而退，看不到具体的报错。
<!-- more -->
```
// 查看语法
C:\Windows\system32>cmd /?
```
## Basic
CMD [/A | /U] [/Q] [/D] [/E:ON | /E:OFF] [/F:ON | /F:OFF] [/V:ON | /V:OFF]
    [[/S] [/C | /K] string]

* /C      执行字符串指定的命令然后终止，执行完后关闭命令窗口
* /K      执行字符串指定的命令但保留，不关闭窗口
* /S      修改 /C 或 /K 之后的字符串处理(见下)
* /Q      关闭回显
* /D      禁止从注册表执行 AutoRun 命令(见下)
* /A      使向管道或文件的内部命令输出成为 ANSI
* /U      使向管道或文件的内部命令输出成为 Unicode
* /T:fg   设置前台/背景颜色(详细信息见 COLOR /?)
* /E:ON   启用命令扩展(见下)
* /E:OFF  禁用命令扩展(见下)
* /F:ON   启用文件和目录名完成字符(见下)
* /F:OFF  禁用文件和目录名完成字符(见下)
* /V:ON   使用 ! 作为分隔符启用延迟的环境变量扩展。例如，/V:ON 会允许 !var! 在执行时扩展变量 var。var 语法会在输入时扩展变量，这与在一个 FOR循环内不同。
* /V:OFF  禁用延迟的环境扩展。

注意，如果字符串加有引号，可以接受用命令分隔符"&&"分隔多个命令。另外，由于兼容性原因，/X 与 /E:ON 相同，/Y 与 /E:OFF 相同，且 /R 与/C 相同。任何其他开关都将被忽略。

## /C 和 /K
如果指定了 /C 或 /K，则会将该开关之后的命令行的剩余部分作为一个命令行处理，其中，会使用下列逻辑处理引号(")字符:

 如果符合下列所有条件，则会保留命令行上的引号字符:
    - 不带 /S 开关
    - 正好两个引号字符
    - 在两个引号字符之间无任何特殊字符，特殊字符指下列字符: &<>()@^|
    - 在两个引号字符之间至少有一个空格字符
    - 在两个引号字符之间的字符串是某个可执行文件的名称。

否则，老办法是看第一个字符是否是引号字符，如果是，则去掉首字符并删除命令行上最后一个引号，保留最后一个引号之后的所有文本。

## /D
如果 /D 未在命令行上被指定，当 CMD.EXE 开始时，它会寻找以下REG_SZ/REG_EXPAND_SZ 注册表变量。如果其中一个或两个都存在，这两个变量会先被执行。

    HKEY_LOCAL_MACHINE\Software\Microsoft\Command Processor\AutoRun

  和/或

    HKEY_CURRENT_USER\Software\Microsoft\Command Processor\AutoRun

命令扩展是按默认值启用的。您也可以使用 /E:OFF ，为某一特定调用而停用扩展。您可以在机器上和/或用户登录会话上启用或停用 CMD.EXE 所有调用的扩展，这要通过设置使用REGEDIT.EXE 的注册表中的一个或两个 REG_DWORD 值:

    HKEY_LOCAL_MACHINE\Software\Microsoft\Command Processor\EnableExtensions

  和/或

    HKEY_CURRENT_USER\Software\Microsoft\Command Processor\EnableExtensions

到 0x1 或 0x0。用户特定设置比机器设置有优先权。命令行开关比注册表设置有优先权。

在批处理文件中，SETLOCAL ENABLEEXTENSIONS 或 DISABLEEXTENSIONS 参数
比 /E:ON 或 /E:OFF 开关有优先权。请参阅 SETLOCAL /? 获取详细信息。

命令扩展包括对下列命令所做的更改和/或添加:
<pre>
    DEL or ERASE
    COLOR
    CD or CHDIR
    MD or MKDIR
    PROMPT
    PUSHD
    POPD
    SET
    SETLOCAL
    ENDLOCAL
    IF
    FOR
    CALL
    SHIFT
    GOTO
    START (同时包括对外部命令调用所做的更改)
    ASSOC
    FTYPE
</pre>

有关特定详细信息，请键入 commandname /? 查看。

延迟环境变量扩展不按默认值启用。您可以用/V:ON 或 /V:OFF 开关，为 CMD.EXE 的某个调用而启用或停用延迟环境变量扩展。您可以在机器上和/或用户登录会话上启用或停用 CMD.EXE 所有调用的延迟扩展，这要通过设置使用REGEDIT.EXE 的注册表中的一个或两个 REG_DWORD 值:

    HKEY_LOCAL_MACHINE\Software\Microsoft\Command Processor\DelayedExpansion

  和/或

    HKEY_CURRENT_USER\Software\Microsoft\Command Processor\DelayedExpansion

到 0x1 或 0x0。用户特定设置比机器设置有优先权。命令行开关比注册表设置有优先权。

在批处理文件中，SETLOCAL ENABLEDELAYEDEXPANSION 或DISABLEDELAYEDEXPANSION参数比 /V:ON 或 /V:OFF 开关有优先权。请参阅 SETLOCAL /?获取详细信息。

如果延迟环境变量扩展被启用，惊叹号字符可在执行时间被用来代替一个环境变量的数值。

您可以用 /F:ON 或 /F:OFF 开关为 CMD.EXE 的某个调用而启用或禁用文件名完成。您可以在计算上和/或用户登录会话上启用或禁用 CMD.EXE 所有调用的完成，
这可以通过使用 REGEDIT.EXE 设置注册表中的下列REG_DWORD 的全部或其中之一:

    HKEY_LOCAL_MACHINE\Software\Microsoft\Command Processor\CompletionChar
    HKEY_LOCAL_MACHINE\Software\Microsoft\Command Processor\PathCompletionChar

和/或

    HKEY_CURRENT_USER\Software\Microsoft\Command Processor\CompletionChar
    HKEY_CURRENT_USER\Software\Microsoft\Command Processor\PathCompletionChar

由一个控制字符的十六进制值作为一个特定参数(例如，0x4是Ctrl-D，0x6 是 Ctrl-F)。用户特定设置优先于机器设置。命令行开关优先于注册表设置。

如果完成是用 /F:ON 开关启用的，两个要使用的控制符是:目录名完成用 Ctrl-D，文件名完成用 Ctrl-F。要停用注册表中的某个字符，请用空格(0x20)的数值，因为此字符不是控制字符。

如果键入两个控制字符中的一个，完成会被调用。完成功能将路径字符串带到光标的左边，如果没有通配符，将通配符附加到左边，并建立相符的路径列表。然后，显示第一个相符的路径。如果没有相符的路径，则发出嘟嘟声，不影响显示。之后，重复按同一个控制字符会循环显示相符路径的列表。将 Shift键跟控制字符同时按下，会倒着显示列表。如果对该行进行了任何编辑，并再次按下控制字符，保存的相符路径的列表会被丢弃，新的会被生成。如果在文件和目录名完成之间切换，会发生同样现象。两个控制字符之间的唯一区别是文件完成字符符合文件和目录名，而目录完成字符只符合目录名。如果文件完成被用于内置式目录命令(CD、MD 或 RD)，就会使用目录完成。
用引号将相符路径括起来，完成代码可以正确处理含有空格或其他特殊字符的文件名。同时，如果备份，然后从行内调用文件完成，完成被调用时位于光标右方的文字会被调用。

需要引号的特殊字符是:
<pre>
<空格>()[]{}^=;!'+,`~(&()
</pre>

## 解决方法
所以针对上文提到的问题，在命令行前加一个/K参数就可以解决了，别忘了解决后再改回/C
