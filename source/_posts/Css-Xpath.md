---
title: 'Css-Xpath'
date: 2018-05-03 09:39:28
tags: Xpath
categories:
		- 数据分析和抓取
        - Scrapy
---
当我们抓取网页源码后，就需要从源码中提取信息，而提取信息的方式有如下几种：

	正则表达式
	解析库及解析方法：
			lxml
			Beautiful Soup
			pyquery
			XPath
			CSS选择器
## 用Css进行解析、过滤及存储
	sites = response.css('#site-list-content > div.site-item > div.title-and-desc')
    items = []

    for site in sites:
        item = Website()
        item['name'] = site.css('a > div.site-title::text').extract_first().strip()
        item['url'] = site.xpath('a/@href').extract_first().strip()
        item['description'] = site.css('div.site-descr::text').extract_first().strip()
        items.append(item)

    return items

----------
	words_to_filter = ['politics', 'religion']

    def process_item(self, item, spider):
        for word in self.words_to_filter:
            if word in item['description'].lower():
                raise DropItem("Contains forbidden word: %s" % word)
        else:
            return item
	
