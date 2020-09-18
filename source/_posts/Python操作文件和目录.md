---
title: Python操作文件和目录
date: 2018-03-27 12:42:03
tags: [Python]
categories:
		- Python
		- 基础篇
---
在Python中经常用到os和shutil模块对文件和目录进行操作，下面就对常用方法进行一个整理：
	
	1. os.getcwd() -- 获取当前Python脚本工作的目录路径
	
	2. os.listdir() -- 返回指定目录下的所有文件和目录名
	   os.listdir('c:\\')

	3. os.remove(filepath) -- 删除一个文件
	
	4. os.removedirs(r'd:\python') -- 删除多个空目录
	
	5. os.path.isfile(filepath) -- 检验给出的路径是否是一个文件
	
	6. os.path.iddir(filepath) -- 检验给出的路径是否是一个目录
	
	7. os.path.isabs() -- 判断是否是绝对路径
	
	8. os.path.exists() -- 检验路径是否真的存在
	   os.path.exists(r'd:\python')

	9. os.path.split() -- 分离一个路径的目录名和文件名
	   os.path.split('/home/swaroop/byte/code/poem.txt') 
	   结果是一个元组：('/home/swaroop/byte/code', 'poem.txt') 
	
	10. os.path.splitext() -- 分离扩展名
		os.path.split('/home/swaroop/byte/code/poem.txt') 
	    结果是一个元组：('/home/swaroop/byte/code/poem', '.txt') 

	11. os.path.dirname(filepath) --获取路径名

	12. os.path.basename(filepath)-- 获取文件名
	
	13. os.getenv()和os.putenv() -- 读取和设置环境变量
	
	14. os.linesep -- 给出当前平台使用的行终止符
		Windows使用'\r\n'，Linux使用'\n'而Mac使用'\r'
			
	15. os.system() -- 运行shell命令

	16. os.name -- 指示你正在使用的平台
	    对于Windows，它是'nt'，而对于Linux/Unix用户，它是'posix'

	17. os.rename(old， new) -- 重命名文件或目录

	18. os.makedirs(r“c：\python\test”) -- 创建多级目录：

	19. os.mkdir(“test”) -- 创建单个目录

	20. os.stat(file) --获取文件属性

	21. os.chmod(file) -- 修改文件权限与时间戳

	22. os.exit() -- 终止当前进程
	
	23. os.path.getsize(filename) -- 获取文件大小：
	
	24. shutil.copytree('olddir','newdir') -- 复制文件夹
		olddir和newdir都只能为目录，且newdir必须不存在

	25. shutil.copyfile('oldfile','newfile') -- 复制文件
		olddir和newdir都只能为文件
		shutil.copy('oldfile','newfile') -- 复制文件
		olddir只能为文件，newfile可以是文件，也可以是目标目录

	26. shutil.move('oldpos','newpos') -- 移动文件
		
	27. os.rmdir('dir') -- 只能是删除空目录
	
	28. shutil.rmtree('dir') -- 空目录、有内容目录均可删除
 