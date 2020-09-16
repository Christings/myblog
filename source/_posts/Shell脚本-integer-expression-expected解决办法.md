---
title: Shell脚本--integer expression expected解决办法
date: 2018-07-26 09:54:56
tags: Shell
categories:
		- Linux
		- Shell
---
### 1.错误及原因
	在Shell脚本中，在进行参数比较时，比如[ $var -eq "en" ],此时就会出现“integer expression expected”的错误。这是因为参数-eq，-lt，-gt比较的都是数字和变量，而变量var如果是一个字符串的话，就会出现上述错误了。
### 2.解决办法
	此时可以采用一种迂回的办法，即用awk工具对变量进行比较，返回比较结果，再将比较结果与相应的数字量进行比较。
	如：
	[ `echo $var | awk -v tem="en" '{print($1>tem)? "1":"0"}'` -eq 0]
### 3.例子

	if [ `echo $var | awk -v tem="en" '{print($1>tem)? "1":"0"}'` -eq "0" ];
    	then
        	grep xml request_body_dump.file | grep "<v2:from>$var</v2:from><v2:to>zh-CHS</v2:to>" | head -n 1000 > xml
-zh-$var
        	grep xml request_body_dump.file | grep "<v2:from>zh-CHS</v2:from><v2:to>$var</v2:to>" | head -n 1000 >> xm
l-zh-$var
        	echo "$datename-success-xml-$var" >> std.log
    	else
        	grep xml request_body_dump.file | grep "<v2:from>$var</v2:from><v2:to>zh-CHS</v2:to>" | head -n 200 > xml-
zh-$var
        	grep xml request_body_dump.file | grep "<v2:from>zh-CHS</v2:from><v2:to>$var</v2:to>" | head -n 200 >> xml
-zh-$var
        	echo "$datename-success-xml-$var" >> std.log
    	fi

