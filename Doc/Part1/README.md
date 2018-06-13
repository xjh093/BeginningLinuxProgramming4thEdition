# 第1章 入门

## 1.1.1 什么是UNIX
UNIX操作系统最初是由贝尔实验室开发的，当时的贝尔实验室是电信业巨头AT&T (美国电报
电话公司)旗下的一员。UNIX是在20世纪70年代为DEC (数字设备公司)的PDP系列计算机设计的，
它现在已成为-种非常流行的多用户、多任务操作系统。UNIX操作系统可以运行在大量不同种类
的硬件平台上，其适用范围从PC工作站-直到多处理器服务器和超级计算机。

### UNIX哲学
UNIX程序特点：

1.简单性。
- “小而简单”(KISS,Keep It Small and Simple)

2.集中性。

3.可重用组件。

4.过滤器。

5.开放的文件格式。
- 比较成功并流行的UNIX程序都使用纯ASCII码的文本文件或XML文件作为配置文件和数据文件

6.灵活性。

## 1.1.2 什么是Linux
可能你已经知道，Linux是一个可以自由发布的类UNIX内核实现，它是一个操作系统的底层核心。
因为Linux以UNIX系统为其灵感来源，所以Linux程序和UNIX程序是非常相似的。
事实上，几乎所有为UNIX编写的程序都可以在Linux上编译运行。
而且，一些专用于UNIX商业版本的商业应用软件，也可以不加改变地以二进制形式运行在Linux系统上。

Linux是由赫尔辛基大学的Linus Torvalds开发的，期间得到了因特网上广大UNIX程序员的帮助。
它最初只是受Andy Tanenbaum教授的Minix (一个小型的类UNIX系统)启发而开发的程序，纯属个人
爱好，但后来它自身逐步发展成为一个完整的系统。

## 1.2.1 Linux 程序设计
Linux应用程序表现为两种特殊类型的文件:可执行文件和脚本文件。可执行文件是计算机可以直
接运行的程序，它们相当于Windows中的.exe文件。脚本文件是一组指令的集合，这些指令将由另一
个程序(即解释器)来执行，它们相当于Windows中的.bat文件、.cmd文件或解释执行的BASIC程序。

### 标准路径：
- /bin:二进制文件目录，用于存放启动系统时用到的程序。
- /usr/bin:用户二进制文件目录，用于存放用户使用的标准程序。
- /usr/ local /bin:本地二进制文件目录，用于存放软件安装的程序。

### 注意：
Linux像UNIX一样，使用冒号(:)分隔PATH变量里的条目

## 1.2.3 C语言编译器

### 第一个 Linux C 语言程序
```
#include <stdio.h>
#include <stdlib.h>

int main()
{
  printf("Hello World\n");
  exit(0);
}

```

编译、链接和运行程序：
```
gcc -o hello hello.c
./hello
```

符号.代表当前目录

-o name 告诉编译器可执行程序的名字，默认生成的是 a.out

## 1.2.4 开发系统导引

系统管理员一般喜欢使用/opt和/usr/local目录，因为它们分离了厂商提供及后续添加的文件与系统本身提供的应用程序。
一直保持以这种方式组织文件的好处在你需要升级操作系统时就可以看出来了，
因为只有目录/opt和/usr/local里的内容需要保留。
我们建议对于系统级的应用程序，你可以将它放在/usr/local目录中来运行和访问所需的文件。
对于开发用和个人的应用程序，最好在你的家目录中使用一个文件夹来存放它。

### 3.库文件
库文件的名字总是以lib开头，随后的部分指明这是什么库(例如，c代表C语言库，m代表数学库)。
文件名的最后部分以.开始，然后给出库文件的类型:
- .a代表传统的静态函数库;
- .so代表共享函数库。

### 4.静态库

fred.c
```
#include <stdio.h>

void fred(int arg)
{
	printf("fred: we passed %d\n",arg);
}
```

bill.c
```
#include <stdio.h>

void bill(char *arg)
{
	printf("bill: we passed %s\n", arg);
}
```

编译：
```
gcc -c bill.c fred.c
ls *.o
bill.o fred.o
```
-c 选项的作用是阻止编译器创建一个完整的程序。如果此时试图创建一个完整的程序将不会成功，因为你还未定义main函数。

为你的库文件创建一个头文件。这个头文件将声明你的库文件中的函数，它应该被所有希望使用你的库文件的应用程序所包含。把这个头文件包含在
源文件fred.c和bill.c中是一个好主意，它将帮助编译器发现所有错误。

lib.h
```
/*
	This is lib.h. It declares the functions fred and bill for users
*/

void bill(char *);
void fred(int);
```

调用程序(program.c)非常简单。它包含库的头文件并且调用库中的一个函数。

program.c
```
#include <stdlib.h>
#include "lib.h"

int main()
{
	bill("Hello World");
	exit(0);
}
```

现在，你可以编译并测试这个程序了。你暂时为编译器显式指定目标文件，然后要求编译器
编译你的文件并将其与先前编译好的目标模块bill.o链接。
```
gcc -C program.c
gcc -。program program.o bi11.o
./program
bill: we passed Hello World
```

现在，你将创建并使用一个库文件。你使用 ar 程序创建一个归档文件并将你的目标文件添加进去。
这个程序之所以称为ar,是因为它将若干单独的文件归并到一个大的文件中以创建归档文件或集合。
注意，你也可以用 ar 程序来创建任何类型文件的归档文件(与许多UNIX工具一样，ar是一个通用工具)。
```
ar crv libfoo.a bi11.o fred.o
a - bill.o
a - fred.o
```

库文件创建好了，两个目标文件也已添加进去。在某些系统，尤其是从Berkeley UNIX衍生的
系统中，要想成功地使用函数库，你还需要为函数库生成一个内容表。你可以通过ranlib命令来完成
这一工作。在Linux中，当你使用的是GNU的软件开发工具时，这-步骤并不是必需的(但做了也无妨)。
```
ranlib libfoo.a
```
你的函数库现在可以使用了。你可以在编译器使用的文件列表中添加该库文件以创建你的程序，
如下所示:
```
gcc -0 program program.o libfoo.a
./program
bi1l: we passed Hello World
```

要查看哪些函数被包含在目标文件、困数库或可执行文件里，你可以使用nm命令。
如果你查看 program 和 libfoo.a ,你就会看到函数库
libfoo.a 中包含 fred 和 bi11 两个函数，
而 program 里只包含函数 bill


### 5.共享库

静态库的一个缺点是，当你同时运行许多应用程序并且它们都使用来自同一个函数库的函数时，
内存中就会有同一函数的多份副本，而且在程序文件自身中也有多份同样的副本。这将消耗大量宝贵
的内存和磁盘空间。


当一个程序使用共享库时，它的链接方式是这样的:程序本身不再包含函数代码，而是引用运行
时可访问的共享代码。当编译好的程序被装载到内存中执行时，函数引用被解析并产生对共享库的调
用，如果有必要，共享库才被加载到内存中。

