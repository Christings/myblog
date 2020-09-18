---
title: 利用Scrapy下载世界银行excel文件
date: 2018-04-03 16:36:53
tags: [Scrapy]
categories:
		- 数据分析和抓取
		- Scrapy
---
>本文首发于我的博客：[http://gongyanli.com](http://gongyanli.com/%E5%88%A9%E7%94%A8Scrapy%E4%B8%8B%E8%BD%BD%E4%B8%96%E7%95%8C%E9%93%B6%E8%A1%8Cexcel%E6%95%B0%E6%8D%AE/)  
>代码传送门：[https://github.com/Gladysgong/wordbank](https://github.com/Gladysgong/wordbank)  
>简书: [https://www.jianshu.com/p/b8253ad8054e](https://www.jianshu.com/p/b8253ad8054e)  
>CSDN: [https://blog.csdn.net/u012052168/article/details/79806493](https://blog.csdn.net/u012052168/article/details/79806493)

## 一、总体思路
		我的目标是下载世界银行中各个指标的excel文件，刚好世界银行给我们提供了excel下载页面的url地址，这样子我们只需要构建url地址进行
	请求就好了，还蛮简单的，也不会太大劲。
		首先我需要把所有指标的地址拿到，于是我找到了这个地址**https://data.worldbank.org/indicator?tab=all**，通过这个地址拿到所
	有指标的href，再进行拼接，最后把拼接的结果进行请求。
![](http://p2lakvkq0.bkt.clouddn.com/worldbank.jpg)
## 二、item模块
	class WorldBankItem(scrapy.Item):
	    indi_url = scrapy.Field()  # 指标(indicator)的url
	    indi_name = scrapy.Field()  # 指标(indicator)的名字
## 二、爬虫模块
	1.解析url
	    def parse_urls(self, response):
	        item = WorldBankItem()
	        selector = scrapy.Selector(response)
	
	        indicators = selector.xpath('//*[@id="main"]/div[2]')
	        indi_url = indicators.xpath('section[@class="nav-item"]/ul/li/a/@href').extract()
	        # indi = re.findall(r'/indicator/.*/?view=chart', indicators, re.S)
	        indi_name = indicators.xpath('section[@class="nav-item"]/ul/li/a/text()').extract()
	
	        for each in indi_url:
	            each = each[:-10] + "downloadformat=excel"
	            # i.replace("view=chart", "downloadformat=excel") #使用replace进行替换时总是不成功，有待探索！
	            item['indi_url'] = each
	            print("indi_url", item['indi_url'])
	            yield item
	            yield scrapy.Request(url="http://api.worldbank.org/v2/en" + each,
	                                 callback=self.download_excel)
	
	        for each in indi_name:
	            print("indi_name:", each)
	            item['indi_name'] = each
	            # self.filenames = indi_name
	            yield item
	2.下载excel文件并写入
	    def download_excel(self, response):
	        name_temp = response.url.split("/")[-1]
	        name = name_temp.split("?")[-2]
	        print("storename:", name, '-', response.url)
	        filename = r"D:\workspace\scrapy\worldbank\worldbankexcelfiles\%s.xls" % name
	        resp = requests.get(response.url)
	        output = open(filename, 'wb')
	        output.write(resp.content)
	        output.close()
	        return None
## 四、数据持久化
	1.定义mysql类（属于我单独定义的）
		import pymysql
		class Mysql:
		    def __init__(self, host, user, pwd, db):
		        self.host = host
		        self.user = user
		        self.pwd = pwd
		        self.db = db
		
		    def __GetConnect(self):
		        if not self.db:
		            raise (NameError, '数据库不存在')
		        self.conn = pymysql.connect(host=self.host, user=self.user, password=self.pwd, database=self.db, charset='utf8')
		        cur = self.conn.cursor()
		        if not cur:
		            raise (NameError, '账号或密码错误')
		        else:
		            return cur
		
		    def ExecQuery(self, sql):
		        cur = self.__GetConnect()
		        cur.execute(sql)
		        resList = cur.fetchall()
		
		        self.conn.close()
		        return resList
		
		    def ExecNoQuery(self, sql):
		        cur = self.__GetConnect()
		        cur.execute(sql)
		        self.conn.commit()
		        self.conn.close()
	2.pipelines
		from worldbank.db.mysql import Mysql
		from worldbank.items import WorldBankItem
		
		
		class WorldbankPipeline(object):
		    def process_item(self, item, spider):
		        if isinstance(item, WorldBankItem):
		            mysql = Mysql(host='localhost', user='root', pwd='421498', db='saas')
		            if len(item['indi_name']) == 0:
		                pass
		            else:
		                newsql = "insert into worldbank_indi(indi_url,indi_name)values('%s','%s')" % (
		                    item['indi_url'], item['indi_name'])
		                print(newsql)
		                mysql.ExecNoQuery(newsql.encode('utf-8'))
		        else:
		            pass
		        return item
## 五、设置settings
	ITEM_PIPELINES = {
   		'worldbank.pipelines.WorldbankPipeline': 300,} # 记得开启此处
## 六、bug
		在爬虫模块parse_urls()中我不光拼接了url地址，我还把指标的url和name放进了item中，因为我这边考虑的，excel文件命名的时候我是
	用的url的一部分命名的，像这样子**EN.ATM.GHGO.KT.CE**，这是属于指标名字的简写，的确我们手工下载数据的时候也是以这个命名。但是像我
	这种对指标不熟悉的人，完全看不出简写的含义，所以我就想把简写以及指标的全名存储进入数据库，以方便对照，所以我用的yield item这样子来
	返回数据。
		但是实际存储的时候，总是报错**KeyError: 'indi_name'**，但是数据也确实存进了数据库，所以我很不理解，这个问题有待于探索，也希
	望知道的朋友可以告知。
		本来我也试过用Request中meta来传递item，然后一起返回，但是插入数据库的时候，报错主键的值必须唯一。
		有可能和用的数据库也有关系，用MySQL的数据来存储爬虫数据很不顺手，因为需要自己手工建立数据库和表，或者写代码建立。而MongoDB就很
	方便了，告诉数据库名字和表名，自动就帮我们创建了。
## 七、我是二傻
		原来上面的问题我早就解决了，只是我忘记了，果然好记性不如烂笔头。
	parse_url()换成如下：
	其实是把两个for改成了一个for，但是这样子就需要把list换成str来进行存储，并且存储的时候我遇到了转义字符的问题，报错如下：
	**pymysql.err.ProgrammingError: (1064, 'You have an error in your SQL syntax; check the manual that corresponds to 
	your MySQL server version for the right syntax to use near \'Recipe" of Machine Learning","https://i.ytimg.com/vi/
	DkgJ_VkU5jM/hqdefault.jpg",\' at line 4')**
	改pipelines文件，把字段包裹上pymysql.escape_string(),同时我已经将代码更新，可以自己去看。

		def parse_urls(self, response):
	        item = WorldBankItem()
	        selector = scrapy.Selector(response)
	
	        indicators = selector.xpath('//*[@id="main"]/div[2]/section[@class="nav-item"]/ul/li')
	
	        for i in indicators:
	            temp_url = i.xpath('a/@href').extract()  # 得到的结果为list
	            # indi_url = str(temp_url)[:-12] + "downloadformat=excel"
	            indi_url = str(temp_url).replace("view=chart", "downloadformat=excel")
	            item["indi_url"] = indi_url.replace("'", "").replace("[", "").replace("]", '')
	            # print('item["indi_url"]:', item["indi_url"])
	
	            indi_name = i.xpath('a/text()').extract()
	            item["indi_name"] = str(indi_name)
	            # print('item["indi_name"]:', item["indi_name"])
	            yield item
	
	            url = indi_url.replace("'", "").replace("[", "").replace("]", '')
	            yield scrapy.Request(url="http://api.worldbank.org/v2/en" + url, callback=self.download_excel)
