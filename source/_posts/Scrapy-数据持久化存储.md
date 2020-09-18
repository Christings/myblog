---
title: Scrapy——数据持久化存储
date: 2018-01-12 17:57:03
tags: [Scrapy]
categories:
		- 数据分析和抓取
		- Scrapy
---
>本文首发于我的博客：[gongyanli.com](http://gongyanli.com/Scrapy%E2%80%94%E2%80%94%E6%95%B0%E6%8D%AE%E6%8C%81%E4%B9%85%E5%8C%96%E5%AD%98%E5%82%A8/)

前言：本文主要讲解Scrapy的数据持久化，主要包括存储到数据库、json文件以及内置数据存储
## 持久化存储——JSON
>pipelins.py
    
    `import json
	 from scrapy.exceptions import DropItem
	 class myPipeline(object):
	 	def __init__(self):
			self.file=open('test.json','wb')
		
		def process_item(self,item,spider):
			if item['title']:
				line=json.dumps(dict(item))+'\n'
				self.file.write(line)
				return item
			else:
				raise DropItem("Missing title in %s" % item)`

## 持久化存储——MongoDB数据库
    `class myPipeline(object):
     def __init__(self):
        self.client = pymongo.MongoClient(host=settings['MONGO_HOST'], port=settings['MONGO_PORT'])
        self.db = self.client[settings['MONGO_DB']]
        # self.coll = self.db[settings['MONGO_COLL2']]
        self.chinacwa = self.db['chinacwa']
        self.iot = self.db['iot']
        self.ny135 = self.db['ny135']
        self.productprice = self.db['productprice']
        self.allproductprice = self.db['allproductprice']

     def process_item(self, item, spider):
        if isinstance(item, ChinacwaItem):
            try:
                if item['article_title']:
                    item = dict(item)
                    self.chinacwa.insert(item)
                    print("插入成功")
                    return item
            except Exception as e:
                spider.logger.exceptionn("")`

## 持久化存储——内置数据存储
settings

	1.JSON
	> FEED_FORMAT:json
	> 所用的内置输出类：JsonItemExporter
	
	2.JSON lines
	> FEED_FORMAT:jsonlines
	> 所用的内置输出类：JsonLinesItemExporter

	3.CSV
	> FEED_FORMAT:csv
	> 所用的内置输出类：CsvItemExporter

	4.XML
	> FEED_FORMAT:xml
	> 所用的内置输出类：XmlItemExporter

	5.Pickle
	> FEED_FORMAT:pickle
	> 所用的内置输出类：PickleItemExporter
	
	6.Marshal
	> FEED_FORMAT:marshal
	> 所用的内置输出类：MarshalItemExporter

	> 使用方法：
	$ scrapy crawl mySpider -o test.csv