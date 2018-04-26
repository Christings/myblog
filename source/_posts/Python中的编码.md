---
title: Python中的编码
date: 2018-04-25 14:24:03
tags: Python
categories:
	- Python
---
## 一、编码起源--从ASCII编码开始
计算机只能处理数字，如果要处理文本，就必须先把文本转换为数字才能处理。最早的计算机在设计时采用8个比特（bit）作为一个字节（byte），所以一个字节能表示的最大的整数就是255（二进制11111111=十进制255）。如果要表示更大的整数，就必须用更多的字节。比如两个字节可以表示的最大整数是65535，4个字节可以表示的最大整数是4294967295。

	计算机是美国人发明的，最早只有127个字符被编码到计算机中，这个编码就是ASCII编码，包含大小写英文字母、数字以及一些符号。
	大写字母A：65
	小写字母z：122

	要处理中文一个字节显然不够了，至少需要2个字节，还不能和ASCII编码冲突，所以中国制定了GB2312编码，来对中文进行编码。
	同理，日本把日文编到Shift_JIS里，韩国把韩文编到Euc-kr里，所有就形成了一个问题，每个国家都有每个国家不同的标准，就会出现冲突。于是，
	在多语言混合的文本中，显示就会有乱码问题。
	于是，Unicode编码出现了，把所有语言统一到一套编码中，这样就解决了冲突问题。
## 二、Unicode编码

	Unicode标准在不断发展中，最常用的是用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要4个字节）。现代操作系统和大多数编程语言都直接支持Unicode。

	ASCII编码和Unicode编码的区别：

		ASCII编码是1个字节，而Unicode编码通常是2个字节。
	
		字母-A 用ASCII编码是十进制的65，二进制的01000001；
	
		字符-0 用ASCII编码是十进制的48，二进制的00110000，注意字符'0'和整数0是不同的；
	
		汉字-中 已经超出了ASCII编码的范围，用Unicode编码是十进制的20013，二进制的01001110 00101101。
	
		如果把ASCII编码的A用Unicode编码，只需要在前面补0就可以，因此，A的Unicode编码是00000000 01000001。
	
	新的问题又出现了：如果统一成Unicode编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用Unicode编码比ASCII编码需要多一倍的存储空间，在存储和传输上就十分不划算。
	
	所以，为了节约存储空间和传输时间，可以把Unicode编码转换为“可变长的编码”，即UTF-8编码。
## 三、UTF-8编码
UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。如果你要传输的文本包含大量英文字符，用UTF-8编码就能节省空间。

	字符			ASCII		Unicode					UTF-8
	A			01000001	00000000 01000001		01000001
	中			x			01001110 00101101		11100100 10111000 10101101

	从上面可以看出，UTF-8编码有一个额外的好处，就是ASCII编码实际上可以被看成是UTF-8编码的一部分，所以，大量只支持ASCII编码的历史遗留软件可以在UTF-8
	编码下继续工作。
## 四、计算机系统通用的字符编码工作方式
	在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。
	用记事本编辑的时候，从文件读取的UTF-8字符被转换为Unicode字符到内存里，编辑完成后，保存的时候再把Unicode转换为UTF-8保存到文件。
	浏览网页的时候，服务器会把动态生成的Unicode内容转换为UTF-8再传输到浏览器，所以你看到很多网页的源码上会有类似<meta charset="UTF-8" />
	的信息，表示该网页正是用的UTF-8编码。
![](http://p2lakvkq0.bkt.clouddn.com/code1.jpg)

![](http://p2lakvkq0.bkt.clouddn.com/code2.jpg)
## 五、Python中的字符串
	在Python3中，字符串以Unicode编码，所以此字符串是支持多语言的。
	单个字符：
		ord():获取字符的整数表示
		chr():把编码转换为对应的字符
	
	Python中的字符串类型为str，在内存中以Unicode表示，一个字符对应若干个字节。如果在网络上传输或保存在磁盘中，需要把str变为以字节为单位bytes。
	Python对bytes类型的数据用带b前缀的单引号或双引号表示：x=b'abc'

	注意区分'ABC'和b'ABC'，前者是str，后者虽然内容显示得和前者一样，但bytes的每个字符都只占用一个字节。
### 1.encode()方法
	以Unicode表示的str通过encode()方法可以编码为指定的bytes，如：

	>>> 'ABC'.encode('ascii')
	b'ABC'
	>>> '中文'.encode('utf-8')
	b'\xe4\xb8\xad\xe6\x96\x87'
	>>> '中文'.encode('ascii')
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)

	纯英文的str可以用ASCII编码为bytes，内容是一样的，含有中文的str可以用UTF-8编码为bytes。
	含有中文的str无法用ASCII编码，因为中文编码的范围超过了ASCII编码的范围，Python会报错。

	在bytes中，无法显示为ASCII字符的字节，用\x##显示。
	
### 2.decode()方法
	相反，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是bytes，这时就需要用decode()把bytes变为str。
	
	>>> b'ABC'.decode('ascii')
	'ABC'
	>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
	'中文'
	>>> b'\xe4\xb8\xad\xff'.decode('utf-8')
	Traceback (most recent call last):
	  ...
	UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 3: invalid start byte
	
	如果bytes中包含无法解码的字节，decode()会报错。但如果bytes中只有一小部分无效字节，可以用errors='ignore'来忽略错误字节。
### 3.小结

	1个中文字符经过UTF-8编码后通常会占用3个字节，而1个英文字符只占用1个字节。
	在操作字符串时，我们经常遇到str和bytes的互相转换。为了避免乱码问题，应当始终坚持使用UTF-8编码对str和bytes进行转换。
	由于Python源代码也是一个文本文件，所以当源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源
	代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：	

		#!/usr/bin/env python3
		# -*- coding: utf-8 -*-
	第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；
	第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

## 六、中文编码--关于gb2312,gbk,gb18030三者的关系
	
	GB2312：6763个汉字
	GBK：21003个汉字
	GB18030-2000：27533个汉字
	GB18030-2005：70244个汉字
	
	所以，GBKSS是GB2312的超集，GB18030是GBK的超集。
## 七、将UTF-8格式的文件转换成GBK格式的文件
	功能：在函数readFile中以utf-8编码方式读取文件，返回Unicode的结果，最后将Unicode结果以gbk格式写入文件。
	`
	def readFile(filePath,encoding="utf-8"):
	    with codecs.open(filePath,"r",encoding) as f:
	        return f.read()
 
	def writeFile(filePath,u,encoding="gbk"):
	    with codecs.open(filePath,"w",encoding) as f:
	        f.write(u)
 
	def UTF8_2_GBK(src,dst):
	    content = ReadFile(src,encoding="utf-8")
	    WriteFile(dst,content,encoding="gbk")
	`
## 八、报错
	
	UnicodeEncodeError: 'gbk' codec can't encode character u'\xa0' in position 4813: illegal multibyte sequence.
	在将Unicode编码成gbk的时候，不能将Unicode u'\xa0'编码成gbk。
	在进行格式转换的时候，如果某些字符不包含在bgk字符集中，就会报错。
	
	`
	# 方案一
	def writeFile(filePath, u, encoding='gbk'):
	    with codecs.open(filePath, 'wb') as f:
	        f.write(u.encode(encoding, errors='ignore')) 
	# ignore表示编码的时候，忽略掉无法编码的字符，解码同理。
	# 执行后发现把utf-8转换成了ansi格式，另外生成文件中每隔一行都有一行空行，所以加上b，使之以二进制流的形式写文件。

	# 方案二
	def utf8_2_gbk(src, dst):
	    content = readFile(src, encoding='utf-8')
	    writeFile(dst, content, encoding='gbk18030')
	`
	