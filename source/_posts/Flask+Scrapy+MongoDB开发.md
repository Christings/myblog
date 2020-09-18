---
title: Flask+Scrapy+MongoDB开发
date: 2018-04-04 11:01:32
tags: [Flask,Scrapy,MongoDB]
categories:
		- 数据分析和抓取
		- Scrapy
---
>代码传送门：[https://github.com/Gladysgong/flask_scrapy_aiot](https://github.com/Gladysgong/flask_scrapy_aiot)  

前言：最近心血来潮，想把以前做的事情总结一下，这个小demo是2017年8月左右写的把，很幼稚也有很多需要改进的地方。如果看了的朋友觉得有
帮助的话，github上动动小手指给我个star把，我现在很需要star，你懂得啦，多谢多谢可爱的各位捧场。
## 一、总体思路
	1.利用Scrapy抓取一些农业网站的信息，存储进入MongoDB数据库；
	2.利用Flask搭建我们的后端；
	3.读取MongoDB中存储的信息，并展示在前端；
![](http://p2lakvkq0.bkt.clouddn.com/flask.jpg)   

	目录结构如下，分为flask_aiot和scrapy_aiot两块，其中flask——aiot中包含前后端代码，而scrapy_aiot中包含爬虫代码。很简单把，
	现在让我们开启这趟Flask+Scrapy+MongoDB之旅把！
## 二、scrapy-aiot爬虫模块
在这个模块中，我抓取了5个网站的信息存储进入MongoDB数据库，数据只要是文章和价格，下面具体来看把。
### 1.items定义
	
	# chinacwa——中国智慧农业网
	class ChinacwaItem(scrapy.Item):
	    # 文章标题、关键字、图片地址、摘要、内容地址、内容
	    article_id = scrapy.Field()
	    article_title = scrapy.Field()
	    article_keywords = scrapy.Field()
	    article_imageurl = scrapy.Field()
	    article_abstract = scrapy.Field()
	    article_url = scrapy.Field()
	    article_content = scrapy.Field()
	
	# iot——国家农业物联网
	class IotItem(scrapy.Item):
	    article_id = scrapy.Field()
	    article_title = scrapy.Field()
	    article_keywords = scrapy.Field()
	    article_abstract = scrapy.Field()
	    article_url = scrapy.Field()
	    article_content = scrapy.Field()
	
	# ny135——中国农业物联网
	class Ny135Item(scrapy.Item):
	    article_id = scrapy.Field()
	    article_title = scrapy.Field()
	    article_keywords = scrapy.Field()
	    article_abstract = scrapy.Field()
	    article_url = scrapy.Field()
	    article_content = scrapy.Field()
	
	# productprice——农产品价格
	class ProductpriceItem(scrapy.Item):
	    product_name = scrapy.Field()
	    product_lowestprice = scrapy.Field()
	    product_averageprice = scrapy.Field()
	    product_highestprice = scrapy.Field()
	    product_specification = scrapy.Field()
	    product_unit = scrapy.Field()
	    product_releasedate = scrapy.Field()
	
	# AProductsPrice——全国农产品价格
	class AllProductsPriceItem(scrapy.Item):
	    product_name = scrapy.Field()
	    product_price = scrapy.Field()
	    product_market = scrapy.Field()
	    product_releasedate = scrapy.Field()
### 2.爬虫文件
主要就是发起请求，然后解析response中的内容。对于这几个网站我都是用的CrawlSpider，因为可以做到整站抓取，很方便。来看看iot.py
文件中的内容把。其余四个文件就不一一展示了，可以去github下载来看。[传送门](https://github.com/Gladysgong/flask_scrapy_aiot)

	# 国家农业物联网
	class IotSpider(CrawlSpider):
	    name = "IotSpider"
	    allowed_domains = ['iot-cn.org']
	    start_urls = ['http://www.iot-cn.org']
	    rules = [
	        Rule(LinkExtractor(allow=('/news/show.php')),
	             callback='parse_article',
	             follow=True)
	    ]
	
	    def parse_article(self, response):
	        item = IotItem()
	        sel = Selector(response)
	        article_title = sel.xpath('//div[@class="m"]/div[1]/div[@class="left_box"]/h1[@id="title"]/text()').extract()[0]
	        article_keywords = sel.xpath(
	            '//div[@class="m"]/div[1]/div[@class="left_box"]/div[@class="keytags"]/a/text()').extract()
	        article_abstract = sel.xpath(
	            '//div[@class="m"]/div[1]/div[@class="left_box"]/div[@class="introduce"]/text()').extract()[0]
	        article_url = response.url
	        article_content = sel.xpath(
	            '//div[@class="m"]/div[1]/div[@class="left_box"]/div[@id="content"]/div').xpath('string(.)').extract()[0]
	
	        item['article_title'] = article_title
	        item['article_keywords'] = article_keywords
	        item['article_abstract'] = article_abstract
	        item['article_url'] = article_url
	        item['article_content'] = article_content
	
	        print("article_title:", article_title)
	        print("article_keywords:", article_keywords)
	        print("article_abstract:", article_abstract)
	        print("article_url:", article_url)
	        print("article_content:", article_content)
	
	        yield item
### 3.数据持久化--MongoDB
	# 农业物联网
	class AiotPipeline(object):
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
	                spider.logger.exceptionn("")
	        elif isinstance(item, IotItem):
	            try:
	                if item['article_title']:
	                    item = dict(item)
	                    self.iot.insert(item)
	                    print("成功")
	                    return item
	            except Exception as e:
	                spider.logger.exceptionn("")
	        elif isinstance(item, Ny135Item):
	            try:
	                if item['article_title']:
	                    item = dict(item)
	                    self.ny135.insert(item)
	                    print("插入")
	                    return item
	            except Exception as e:
	                spider.logger.exceptionn("ny135存储失败")
	        elif isinstance(item, ProductpriceItem):
	            try:
	                if item["product_name"]:
	                    item = dict(item)
	                    self.productprice.insert(item)
	                    print("农产品价格数据")
	                    return item
	            except Exception as e:
	                spider.logger.exceptionn("农产品价格数据存储失败")
	        elif isinstance(item, AllProductsPriceItem):
	            try:
	                if item["product_name"]:
	                    item = dict(item)
	                    self.allproductprice.insert(item)
	                    print("全国农产品价格数据")
	                    return item
	            except Exception as e:
	                spider.logger.exceptionn("全国农产品价格数据存储失败")
## 三、flask_aiot模块
	功能点:登录，注册，数据展示，搜索
	熟悉Flask的朋友可以直接看懂，不熟悉Flask的话就得需要看点儿东西呢，写到这里打算抽时间写一篇如何使用Flask来创建一个web网站的
	的文章，有了这样子的基础后，就能很轻易往下看了。
### 1.定义model
	class Chinacwa(mongo.Document):
	    article_id = mongo.IntField(required=True)
	    article_title = mongo.StringField(required=True)
	    article_keywords = mongo.StringField(required=True)
	    article_url = mongo.StringField(required=True)
	    article_abstract = mongo.StringField(required=True)
	    article_content = mongo.StringField(required=True)
	
	    meta = {
	        'collection': 'chinacwa',
	        'ordering': ['-article_id'],
	        'indexes': ['-article_id']
	    }
	
	class Iot(mongo.Document):
	    article_title = mongo.StringField(required=True)
	    article_keywords = mongo.StringField(required=True)
	    article_url = mongo.StringField(required=True)
	    article_abstract = mongo.StringField(required=True)
	    article_content = mongo.StringField(required=True)
	
	    meta = {'collection': 'iot'}
	
	class Ny135(mongo.Document):
	    article_title = mongo.StringField(required=True)
	    article_keywords = mongo.StringField(required=True)
	    article_url = mongo.StringField(required=True)
	    article_abstract = mongo.StringField(required=True)
	    article_content = mongo.StringField(required=True)
	    meta = {'collection': 'ny135'}
	
	class AllProductPrice(mongo.Document):
	    product_name = mongo.StringField(required=True)
	    product_price = mongo.StringField(required=True)
	    product_market = mongo.StringField(required=True)
	    product_releasedate = mongo.StringField(required=True)
	    meta = {'collection': 'allproductprice'}
	
	
	class User(UserMixin, mongo.Document):
	    # uid = mongo.IntField(requires=True)
	    email = mongo.StringField(max_length=255, requires=True)
	    username = mongo.StringField(max_length=255, requires=True)
	    # password = mongo.StringField(requires=True)
	    password_hash = mongo.StringField(requires=True)
	    # confirmed=mongo.BooleanField(default=False)
	    # password_hash = mongo.StringField(requires=True)
	    # meta = {'collection': 'user'}
	
	    @property
	    def password(self):
	        raise AttributeError('password is not a readable attribute')
	
	    @password.setter
	    def password(self, password):
	        self.password_hash = generate_password_hash(password)
	
	    def verify_password(self, password):
	        return check_password_hash(self.password_hash, password)
	
	    def __repr__(self):
	        return '<User %r>' % self.email
	
	    def get_id(self):
	        try:
	            # return unicode(self.username)
	            return self.email
	        except AttributeError:
	            raise NotImplementedError('No `username` attribute - override `get_id`')
	
	    def __unicode__(self):
	        return self.email
	
	        # def confirm(self,token):
	        #     s=Serializer(current_app.config['SECRET_KEY'])
	        #     try:
	        #         data=s.loads(token)
	        #     except:
	        #         return False
	        #     if data.get('confirm')!=self.username
	        #         return False
	        #     self.confirmd=True
	
	@login_manager.user_loader
	def load_user(email):
	    try:
	        user = User.objects.get(email=email)
	    except User.DoesNotExist:
	        user = None
	    return user
### 2.待续未完

