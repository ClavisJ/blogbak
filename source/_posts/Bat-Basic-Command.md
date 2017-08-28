title: bat的一些基础语法
categories: Script
date: 2016-02-26 16:19:21
tags:
---


前两天想在windows下生成两个文件，就看了下bat。这里整理了一些简单的bat命令。

<!--more-->

> bat：批处理文件，在DOS和Windows（任意）系统中，.bat文件是可执行文件，由一系列命令构成，其中可以包含对其他程序的调用。 —— 摘自百度百科


#### 输出
	echo "test" 

#### 注释
	rem setloca

#### 接收命令提示符的参数
	echo %1 
同理，第二个第三个参数就依次是%2，%3...  要执行bat文件，直接双击，或者拖到cmd中，按回车，参数的话，用空格接在bat文件的路径后面就行了。

#### 变量的赋值及使用
	SET p=9  
	echo %p%
tip：变量和值之间的等号不能有空格。

#### 关闭回显
	@echo off 
bat中的每句命令都会在命令提示符中打印出来，可以关闭回显，@表示连本条命令都不显示

#### 变量延迟
	setlocal enabledelayedexpansion 
使批处理能感知到动态变化，开启了之后，变量需要拿感叹号引用 !p!

#### 写入文件
	echo 123 > C:\Users\Clavis\Desktop\txt.txt
如果文件不存在，则会新建该文件

#### 写入多行文本到文件
	@echo off
	more +4 %0 >> C:\Users\Clavis\Desktop\txt.txt
	exit /b
	 
	123=0
	222=0
	333=0
	...
这段话的意思是把`exit \b`后面的全部内容写入txt.txt文件中，more+4就是从一个文件的第四行开始显示，%0表示批处理本身的路径。

这样做有个问题是，...后面如果还有命令，比如echo啥的，会当成文本直接写入txt文件，而不会去执行，如果中间想终止写入，也不知道咋终止TAT

如果不想这样，就只能一句句echo，觉得好low，所以最后还是换的shell脚本实现的...



