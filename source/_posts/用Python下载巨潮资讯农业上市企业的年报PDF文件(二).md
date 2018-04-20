---
title: 用Python下载巨潮资讯农业上市企业的年报PDF文件(二)
date: 2018-04-17 13:30:10
tags: [爬虫]
categories:
		- 爬虫
		- Python
---
>本文首发于我的博客：[http://gongyanli.com/](http://gongyanli.com/%E7%94%A8Python%E4%B8%8B%E8%BD%BD%E5%B7%A8%E6%BD%AE%E8%B5%84%E8%AE%AF%E5%86%9C%E4%B8%9A%E4%B8%8A%E5%B8%82%E4%BC%81%E4%B8%9A%E7%9A%84%E5%B9%B4%E6%8A%A5PDF%E6%96%87%E4%BB%B6(%E4%BA%8C)/)   
>代码传送门：[https://github.com/Gladysgong/cninfo/tree/master/cninfo/pdf](https://github.com/Gladysgong/cninfo/tree/master/cninfo/pdf)  

前言：之前写过一篇利用文章——[PhantomJS+Selenium+Scrapy抓取巨潮资讯网企业信息(一)](http://gongyanli.com/PhantomJS-Selenium-Scrapy%E6%8A%93%E5%8F%96%E5%B7%A8%E6%BD%AE%E8%B5%84%E8%AE%AF%E7%BD%91%E4%BC%81%E4%B8%9A%E4%BF%A1%E6%81%AF/)，来抓取巨潮资讯企业基本信息的文章，后来我还需要拿这些企业的年报的PDF文件，所以我试过也用也用上面这种思路去抓取，但是太过复杂，需要动态点击的元素太多，实现起来很麻烦。后来仔细看年报这块儿是通过post请求加参数来发送request，所以就很简单了。
## 一、思路
![](http://p2lakvkq0.bkt.clouddn.com/cninfo3.jpg)
![](http://p2lakvkq0.bkt.clouddn.com/cninfo2.jpg)

	第一张图中我们选中了行业和所需要的年度报告，发送请求后会返回列表，里面有所有农业企业年度报告的链接。
	第二张图中我们看到了请求的url和所需参数，如图示绿色框所示，所以我们只需要构建这样的请求即可。

----------

	query = {
        'stock': '',
        'searchkey': '',
        # 'plate': PLATE,
        'plate': '',
        'category': CATEGORY,
        'trade': '农、林、牧、渔业',
        'column': 'szse',
        'columnTitle': '历史公告查询',
        'pageNum': page_num,
        'pageSize': MAX_PAGESIZE,
        'tabName': 'fulltext',
        'sortName': '',
        'sortType': '',
        'limit': '',
        'showTitle': '',
        'seDate': START_DATE + '~' + END_DATE,
    }
	r = requests.post(URL, query, HEADER, timeout=RESPONSE_TIMEOUT)

----------

	不复杂，把请求的返回内容存入csv文件，最后再通过downloader读取csv文件，最后根据csv中的链接把PDF文件下载下来。
## 二、代码实现
[代码](https://github.com/Gladysgong/cninfo/tree/master/cninfo/pdf)我和**PhantomJS+Selenium+Scrapy抓取巨潮资讯网企业信息(一)**这篇文章的代码放在一起了，放着cninfo的pdf文件中了，感兴趣自己可以看，可以跑起来的。



