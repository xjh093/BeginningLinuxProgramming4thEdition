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
你可以用管道操作符 | 来连接进程

一个简单的例子：

你可以使用 sort 命令对 ps 命令的输出进行排序

如果不使用管道，必须分步完成
```
ps > psout.txt
sort psout.txt > pssort.out
```

使用管道来连接进程：
```
ps | sort > pssort.out
```

如果想在屏幕上分页显示输出结果，你可以再连接第三个进程 more,将它们都放在同一个命令行上，如下所示:
```
ps| sort | more
```


允许连接的进程数目是没有限制的。假设你想看看系统中运行的所有进程的名字，但不包括 shell 本身，可以使用下面的命令:
```
ps -xo comm | sort | uniq | grep -v sh| more
```
这个命令首先按字母顺序排序 ps 命令的输出，再用 uniq 命令去除名字相同的进程，然后用 grep -v sh 命令删除名为 sh 的进程，最终将结果分页显示在屏幕上。


不要在命令流中重复使用相同的文件名。

如果你尝试执行如下命令：
```
cat mydata.txt | sort | uniq > mydata.txt
```

你最终将得到一个空文件，因为你在读取文件 mydata.txt 之前就已经覆盖了这个文件的内容


### 2.5.1 交互式程序
假设你想要从大量 C 语言源文件中查找包含字符串 POSIX 的文件。
与其使用 grep 命令在每个文件中搜索字符串，
然后再分别列出包含该字符串的文件，
不如用下面的交互式脚本来执行整个操作:

```
for file in *
do
if grep -l POSIX $file
then
more $file
fi
done
```
在这个例子中，grep 命令输出它找到的包含 POSIX 字符串的文件，
然后 more 命令将文件的内容显示在屏幕上。


另一种更有效的方式：
```
more `grep -l POSIX *`
```
或者：
```
more ${grep -l POSIX *}
```

下面的命令将输出包含 POSIX 字符串的文件名：
```
grep -l POSIX * | more
```


通配符 * 用来匹配一个字符串

通配符 ? 用来匹配一个字符


这个命令将列出文件 my_fingers 和 my_toes

```
ls my_{finger,toe}s
```


### 2.5.2 创建脚本

first

```
#! /bin/sh

# first
# This file looks through all the files in the current
# directory for the string POSIX, and then prints the names of 
# those files to the standard output

for file in *
do
  if grep -q POSIX $file
  then
   echo $file
  fi
done

exit 0
```

'#' 表示注释

#!/bin/sh 是一种特殊形式的注释，#!字符告诉系统在它后面的那个参数是用来执行本文件的程序


在 shell 程序设计里，0 表示成功

## 2.5.3 把脚本设置为可执行

两种运行方法：

1.调用 shell:
```
/bin/sh first
```

2.使用 chmod 命令改变文件的模式
```
chmod +x first
```

执行它：
```
first
```
或者
```
./first
```

## 2.6 shell 的语法

### 变量

1.不需要事先声明

2.所有变量都被看作字符串并以字符串来存储

3.Linux是一个区分大小写的系统

4.可以通过在变量前加 $ 符号来访问它的内容

5.使用 echo 命令将变量内容输出到终端上

```
salutation=Hello
echo $salutation
Hello
salutation="Yes Dear"
echo $salutation
Yes Dear
salutation=7+5
echo $salutation
7+5
```

6.字符串里面包含空格，就必须用引号把它们括起来。此外，等号两边不能有空格。

7.使用 read 命令

将用户输入赋值给一个变量，按下回车键时，read 命令结束
```
read salutation
Wie geht's?
echo $salutation
Wie geht's?
```

8.使用引号

脚本文件中的参数以空白字符分隔(一个空格、一个制表符或者一个换行符)

想在一个参数中包含一个或多个空白字符，就必须给参数加上引号

把 $变量表达式 放在双引号中，程序会把变量替换为它的值

把 $亦是表达式 放在单引号中，不会替换

在 $ 字符前面加上一个 \ 字符可以取消它的特殊含义

```
#!/bin/sh

myvar="Hi there"

echo $myvar
echo "$myvar"
echo '$myvar'
echo \$myvar

echo Enter some text
read myvar

echo '$myvar' now equals $myvar
exit 0
```

输出结果：
```
Hi there
Hi there
$myvar
$myvar
Enter some text
Hello World
$myvar now equals Hello World
```

9.环境变量

$HOME

当前用户的家目录

$PATH

以冒号分隔的用户搜索命令的目录列表

$PS1

命令提示符，通常是 $ 字符

$PS2

二级提示，用来提示后续的输入，通常是 > 字符

$IFS

输入域分隔符，通常是空格、制表符或换行符

$0

shell 脚本的名字

$#

传递给脚本的参数个数

$$

shell 脚本的进程号


10.参数变量

$1,$2... 

脚本程序的参数

$*

列出所有的参数，各个参数之间用环境变量IFS中的第一个字符分隔开

$@

列出所有的参数，是 $* 的变体，不使用IFS环境变量

```
IFS=''
set foo bar bam
echo "$@"
foo bar bam
echo "$*"
foobarbam
unset IFS
echo "$*"
foo bar bam

```

11.try_var
```
#!/bin/sh

salutation="Hello"
echo $salutation
echo "The program $0 is now running"
echo "The second paramter was $2"
echo "The first parameter was $1"
echo "The parameter list was $*"
echo "The user's home directory is $HOME"

echo "Please enter a new greeting"
read salutation

echo $salutation
echo "The script is now complete"
exit 0
```

输出结果：
```
Hello
The program ./try_var is now running
The second paramter was bar
The first parameter was foo
The parameter list was foo bar baz
The user's home directory is /Users/haocold
Please enter a new greeting
Sire
Sire
The script is now complete
```


