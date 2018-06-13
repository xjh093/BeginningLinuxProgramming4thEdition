# 第2章 shell程序设计

## 本章内容：
- 什么是shell
- 基本思路
- 微妙的语法:变量、条件判断和程序控制
- 命令列表
- 函数
- 命令和命令的执行
- here文档
- 调试
- grep命令和正则表达式
- find命令

## 2.3 什么是shell
shell是一个作为用户与Linux系统间接口的程序，它允许用户向操作系统输入需要执行
的命令。这点与Windows的命令提示符类似。

在Linux系统中，总是作为 /bin/sh 安装的标准 shell 是 GNU 工具集中的 bash (GNU Bourne Again Shell)。


查看 bash 的版本号：
```
/bin/bash --version
GNU bash, version 3.2.57(1)-release (x86_64-apple-darwin17)
Copyright (C) 2007 Free Software Foundation, Inc.
```

## 2.4 管道和重定向

### 2.4.1 重定向输出

```
ls -l > lsoutput.txt
```
这条命令把 ls 命令的輸出保存到文件 lsoutput.txt 中

现在你只需知道:
- 文件描述符 0 代表一个程序的标准输入，
- 文件描述符 1 代表标准输出，
- 文件描述符 2 代表标准错误输出。

```
ps >> lsoutput.txt 
```
这条命令会将 ps 命令的输出附加到指定文件 lsoutput.txt 的尾部



下面的命令将把标准输出和标准错误输出分别重定向到不同的文件中:
```
kill -HUP 1234 >killout.txt 2>killerr.txt
```


如果你想把两组输出都重定向到一个文件中，你可以用 >& 操作符来结合两个输出。如下所示:
```
kill -1 1234 >killouterr.txt 2>&1
```

这条命令将把标准输出和标准错误输出都重定向到同一个文件中。
请注意操作符出现的顺序。
这条命令的含义是“将标准输出重定向到文件 killouterr.txt,
然后将标准错误输出重定向到与标准输出相同的地方。”
如果顺序有误，重定向将不会按照你预期的那样执行。

你可以用 Linux 的通用“回收站”/dev/null来有效地丟弃所有的输出信息，如下所示:
```
kill -1 1234 >/dev/nu11 2>&1
```

### 2.4.2 重定向输入
例如：
```
more < killout.txt
```
很明显，在Linux 下这样做意义不大，因为Linux的more命令可以接受文件名作为参数，这与
Windows命令行中对应的命令不同。


### 2.4.3 管道



