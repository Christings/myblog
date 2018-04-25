---
title: Python中的编码
date: 2018-04-25 14:24:03
tags: Python
categories:
	- Python
---
## 一、关于gb2312,gbk,gb18030三者的关系
	
	GB2312：6763个汉字
	GBK：21003个汉字
	GB18030-2000：27533个汉字
	GB18030-2005：70244个汉字
	
	所以，GBKSS是GB2312的超集，GB18030是GBK的超集。
## 二、将UTF-8格式的文件转换成GBK格式的文件
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
## 三、报错
	
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
	