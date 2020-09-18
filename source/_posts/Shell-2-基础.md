---
title: Shell-2-基础
date: 2018-07-11 16:43:14
tags: Shell
categories:
		- 工具篇
		- Shell
---
### 1.变量
	
	1) 局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
	2) 环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
	3) shell变量 shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行
	
	
	定义变量：不加$符号 
	my_name='gyl'
	
	使用变量：$
	echo $my_name
	echo ${my_name}
	
	只读变量：不能被改变
	readonly myUrl
	
	删除变量：unset不能删除只读变量
	unset my_book
### 2.字符串
	字符串可以用单引号和双引号，也可不用引号，单双引号的区别跟PHP类似。
	
	单引号：''
		单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
		单引号字串中不能出现单引号（对单引号使用转义符后也不行）；
	str='this is a string'
	
	双引号：""
		双引号里可以有变量
		双引号里可以出现转义字符
	my_name='gyl'
	str="hello,I know you are \"$my_name\"! \n"
	
	拼接字符串：
	echo $greeting $greeting1
	
	获取字符串长度：
	echo ${#str}
	
	提取字符串：
	echo ${str:1:4}
	
	查找子字符串：
	echo `expr index "$string" is` # 反引号，而非单引号
### 3.数组
	bash支持一维数组（不支持多维数组），并且没有限定数组的大小
	下标从0开始，初始时不需要定义数组大小（与PHP类似）
	
	定义数组：
	数组名=(值1 值2 ... 值n)
	array_name=(value0 value1 value2 value3)
	
	读取数组：
	${数组名[下标]}
	valuen=${array_name[n]}
	valuen=${array_name[@]} # @符号可获取数组中的所有元素
	
	获取数组长度：
	length=${#array_name[@]}
	length=${#array_name[*]} # 获取数组元素的个数
	lengthn=${#array_name[n]} # 获取数组单个元素的长度
### 4.注释
	#
	
	:<<EOF
	注释内容...
	注释内容...
	注释内容...
	EOF
### 5.传递参数
	$0:执行的文件名
	$1:第一个参数
	$2:第二个参数，以此类推
	
	$#	传递到脚本的参数个数
	$*	以一个单字符串显示所有向脚本传递的参数。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。
	$$	脚本运行的当前进程ID号
	$!	后台运行的最后一个进程的ID号
	$@	与$*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。
	$-	显示Shell使用的当前选项，与set命令功能相同。
	$?	显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。
### 6.基本运算符
	原生bash不支持简单的数学运算，但是可以通过其他命令来实现，如 awk 和 expr，expr 最常用。
	
	表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
	完整的表达式要被 ` ` 包含，注意这个字符不是常用的单引号，在 Esc 键下边。
	val=`expr 2 + 2`
#### 6.1 算术运算符
	+ - * / % = == !=
	乘号(*)前边必须加反斜杠(\)才能实现乘法运算；
	在 MAC 中 shell 的 expr 语法是：$((表达式))，此处表达式中的 "*" 不需要转义符号 "\" 。
#### 6.2 关系运算符
	关系运算符只支持数字，不支持字符串，除非字符串的值是数字。
	
	-eq ：检测两个数是否相等
	-nq ：检测两个数是否不相等
	-gt ：检测左边的数是否大于右边的
	-lt ：检测左边的数是否小于右边的
	-ge ：检测左边的数是否大于等于右边的
	-le ：检测左边的数是否小于等于右边的
#### 6.3 布尔运算符
	！ ：非运算
	-o ：或运算
	-a ：与运算
#### 6.4 逻辑运算符
	&& ：逻辑中的AND
	|| ：逻辑中OR	
#### 6.5 字符串运算
	=
	!=
	-z ：检测字符串长度是否为0，为0返回true
	-n ：检测字符串长度是否为0，不为0返回true
	str：检测字符串是否为空，不为空返回true
#### 6.6 文件测试运算符
	文件测试运算符用于检测 Unix 文件的各种属性
	
	-b file	检测文件是否是块设备文件，如果是，则返回 true。
	-c file	检测文件是否是字符设备文件，如果是，则返回 true。	
	-d file	检测文件是否是目录，如果是，则返回 true。	
	-f file	检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。	
	-g file	检测文件是否设置了 SGID 位，如果是，则返回 true。	
	-k file	检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。	
	-p file	检测文件是否是有名管道，如果是，则返回 true。	
	-u file	检测文件是否设置了 SUID 位，如果是，则返回 true。	
	-r file	检测文件是否可读，如果是，则返回 true。	
	-w file	检测文件是否可写，如果是，则返回 true。	
	-x file	检测文件是否可执行，如果是，则返回 true。	
	-s file	检测文件是否为空（文件大小是否大于0），不为空返回 true。	
	-e file	检测文件（包括目录）是否存在，如果是，则返回 true。
### 7.echo	
	echo -e "OK! \n"	# -e开启转义
	echo “it is a test.” > myfile # 结果定向到文件
	echo '$name\"' # 原样输出字符串，不进行转义或取变量（用单引号）
	ehco `data` # 线上命令执行结果，用反引号而不是单引号，此处显示当前日期
### 7.printf
	默认 printf 不会像 echo 自动添加换行符，我们可以手动添加 \n
	
	printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  
	printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876 
### 8.test
	数值--6.2
	字符--6.5
	文件--6.6
### 9.流程控制
	if格式：
		if condition
		then
			command1 
			command2
			...
			commandN 
		fi
		或if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi
	
	if else格式：
		if condition
		then
			command1 
			command2
			...
			commandN 
		else
			command
		fi
	
	if else-if else 格式：
		if condition1
		then
			command1
		elif condition2 
		then 
			command2
		else
			commandN
		fi

	for格式：
		for var in item1 item2 ... itemN
		do
			command1
			command2
			...
			commandN
		done
		或for var in item1 item2 ... itemN; do command1; command2… done;
	
	while格式：
		while condition
		do
			command
		done
		
	until格式：until循环直到条件为true时停止
		until condition
		do
			command
		done
		
	case格式：
		case 值 in
		模式1)
			command1
			command2
			...
			commandN
			;;
		模式2）
			command1
			command2
			...
			commandN
			;;
		esac
		
	break和continue：跳出循环和跳出当前循环
### 10.函数
	1、可以带function fun() 定义，也可以直接fun() 定义,不带任何参数。
	2、参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255）
	
	函数返回值在调用该函数后通过 $? 来获得。
	注意：所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。
	
	函数参数：$1表示第一个参数，以此类推
	函数调用：funWithParam 1 2 3 4 5 34 87
	注意，$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。
	
	$#	传递到脚本的参数个数
	$*	以一个单字符串显示所有向脚本传递的参数
	$$	脚本运行的当前进程ID号
	$!	后台运行的最后一个进程的ID号
	$@	与$*相同，但是使用时加引号，并在引号中返回每个参数。
	$-	显示Shell使用的当前选项，与set命令功能相同。
	$?	显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。
### 11.输入输出重定向
	注意:文件描述符:
		0 通常是标准输入（STDIN）
		1 是标准输出（STDOUT）
		2 是标准错误输出（STDERR）
		
	command > file	将输出重定向到 file。
	command < file	将输入重定向到 file。
	command >> file	将输出以追加的方式重定向到 file。
	n > file	将文件描述符为 n 的文件重定向到 file。
	n >> file	将文件描述符为 n 的文件以追加的方式重定向到 file。
	n >& m	将输出文件 m 和 n 合并。
	n <& m	将输入文件 m 和 n 合并。
	<< tag	将开始标记 tag 和结束标记 tag 之间的内容作为输入。
	
	

