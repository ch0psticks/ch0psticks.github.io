---
layout: post  
permalink: /post/Debug_with_GDB
tags: [Debug, Tools, Android, ARM, Linux]
categories: [Research]
---

近期需要调试Android的Native Code，主要针对自己写的有源码的程序。所以选择了GDB。

GDB以前在看 [The Shellcode's Handbook](http://www.amazon.com/Shellcoders-Handbook-Discovering-Exploiting-Security-ebook/dp/B004P5O38Q/ref=sr_1_1?s=books&ie=UTF8&qid=1394156684&sr=1-1&keywords=shellcode%27s+handbook) 一书时，用过GDB，但是年久了，已淡忘，所以重新捡起，随手记之。

## 1. GDB的特性

GDB是一个shell 交互式的调试器，广泛用于基于*nix系统的应用的调试。具备：

* **补全功能** 类似shell一样，输入命令前几个字符后，按`Tab`即可补全或列出可选的命令列表；此外，该补全不仅限于命令补全，根据情况甚至可以补全参数。如：想在函数system下断点，输入`b sys`,按`Tab`之后，即可补全为system函数。
* **支持命令缩写** 下断点可以直接用break的缩写`b`；information可缩写为`i`或`info`等。


## 2. GDB调试

### 2.1 基本调试命令

* `c`(continue）  继续运行
* `n`(next) 下一条语句，即单步运行，但进入调用
* `ni` 单步执行，遇到调用则跟入
* `step`Step可以让你跟踪进入一个函数，而next指令则不会进入函数。
* `b`(break) 下断点。`b *address`, `b line_number`或`b  function_name`.如`b *0xafd01234`,`b system`

* `info`显示信息。`info br`,显示断点列表
* `delete`删除断点等。`delete number`删除指定序号的端点
* `x` (examine)查看内存。`x/<n/f/u> *address`,n、f、u是可选的参数.*n 是一个正整数*，表示显示的单位的个数，也就是说*从当前地址向后显示几个单元的内容*。
*f 表示显示的格式*，参见上面。如果地址所指的是字符串，那么格式可以是s，如果地十是指令地址，那么格式可以是i。
*u 表示从当前地址往后请求的基本的单元（字节数），如果不指定的话，GDB默认是4个bytes的单元。*u参数可以用下面的字符来代替，b表示单字节，h表示双字节，w表示四字 节，g表示八字节。当我们指定了字节长度后，GDB会从指内存定的内存地址开始，读写指定字节，并把其当作一个值取出来。
* 
* `set`设置寄存器、变量的值。 `set $pc`设置pc寄存器的值。
* **设置内存**`print *address=value`将value写入地址为address的内存


### 2.2 其他命令

#### 1)`file`载入程序

#### 2)`run` 在文件或程序载入之后，运行程序

#### 3)`info`查看相关信息

* `info br` 得到你所设置的所有断点的详细信息。包括断点号，类型，状态，内存地址，断点在源程序中的位置等
* `info source` 查看当前源程序
* `info stack` 查看堆栈信息,用这条指令你可以看清楚程序的调用层次关系
* `info args` 查看当前的参数
* `info sharedlibrary` 查看共享库，即可以看到lib的加载基址、范围等


#### 4)输出信息`print`
`print`可以以指定输出格式输出表达式、变量的信息
	
	x 按十六进制格式显示变量;	
	d 按十进制格式显示变量。
	u 按十六进制格式显示无符号整型。
	o 按八进制格式显示变量。
	t 按二进制格式显示变量。
	a 按十六进制格式显示变量。
	c 按字符格式显示变量。
	f 按浮点数格式显示变量。

例如
	
	(gdb) p i
	$21 = 101 
	
	(gdb) p/a i
	$22 = 0x65

	(gdb) p/c i
	$23 = 101 'e'
	
	(gdb) p/f i
	$24 = 1.41531145e-43

	(gdb) p/x i
	$25 = 0x65

	(gdb) p/t i
	$26 = 1100101

也可以**查看变量地址**： `print &record`

#### 5) `list`列出源一段源程序

* `list function` 列出某个函数
* `list LINENUM`　以当前源文件的某行为中间显示一段源程序
* `list -`　显示前一次之前的源程序
* `list FILENAME:`或 `list FILENAME:LINENUM`显示另一个文件的一段程序

#### 6)条件端点 `break ... if COND`

*COND是一个布尔条件表达式，语法与C语言中的一样*。条件断点与一般的断点不同之处是每当程序执行到断点处，都要计算条件表达式，如果为真，程序才会断下，否则程序会一直执行下去。

#### 7)内存断点 watch

Watchpoint: 它的作用是让程序在某个表达式或地址的值发生变化的时候停止运行，达到‘监视’该表达式的目的

（1）设置watchpoints:
a. watch expr: 设置写watchpoint，当应用程序写expr, 修改其值时，程序停止运行
b. rwatch expr: 设置读watchpoint，当应用程序读表达式expr时，程序停止运行
c. awatch expr: 设置读写watchpoint, 当应用程序读或者写表达式expr时，程序都会停止运行
对指定地址: `watch *(int *)addr`

（2）info watchpoints:
查看当前调试的程序中设置的watchpoints相关信息

（3）watchpoints和breakpoints很相像，都有enable/disabe/delete等操作，使用方法也与breakpoints的类似

#### 8) 事件断点 catchpoint

Catchpoint: 的作用是让程序在发生某种事件的时候停止运行，比如C++中发生异常事件，加载动态库事件

（1）设置catchpoints:
a. catch event: 当事件event发生的时候，程序停止运行，这里event的取值有：
1）throw: C++抛出异常
2）catch: C++捕捉到异常
3）exec: exec被调用
4）fork: fork被调用
5）vfork: vfork被调用
6）load: 加载动态库
7）load libname: 加载名为libname的动态库
8）unload: 卸载动态库
9）unload libname: 卸载名为libname的动态库
10）syscall [args]: 调用系统调用，args可以指定系统调用号，或者系统名称
b. tcatch event: 设置只停一次的catchpoint，第一次生效后，该catchpoint被自动删除

（2）catchpoints和breakpoints很相像，都有enable/disabe/delete等操作，使用方法也与breakpoints的类似

*注: 上述中watchpoint和catchpoint相关内容源自: http://www.wuzesheng.com/?p=1362*

#### 9) 指定反汇编的指令集

使用`set arm force-mode arm`或者`set arm force-mode thumb`让gdb切换以arm或thumb的反汇编代码显示。

##### 10) 单步执行显示反汇编

`set disassemble-next on` 下一句指令显示反汇编

##### 11) 设置随机化的开关

`set disable-randomization on/off` 

## 其它相关


查看动态链接库等的内存映射情况:
//其中1234指相应进程的pid
`$adb shell cat proc/1234/maps`

陆续补充

## 参考

[1] [http://www.wuzesheng.com/?p=1362](http://www.wuzesheng.com/?p=1362)
[2] [android平台arm指令学习和调试](http://www.sanwho.com/552.html)


