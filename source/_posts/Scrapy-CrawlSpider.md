---
title: Scrapy--CrawlSpider
date: 2018-04-23 15:17:20
tags: [Scrapy]
categories:
		- 数据分析和抓取
		- Scrapy
---
### 一、CrawlSpider

	爬取规则,不带callback表示向该类url递归爬取
    rules = (
        Rule(SgmlLinkExtractor(allow=(r'https://news.cnblogs.com/n/page/\d',))),
        Rule(LinkExtractor(allow=(r'https://news.cnblogs.com/n/page/\d',))),
        Rule(SgmlLinkExtractor(allow=(r'https://news.cnblogs.com/n/\d+',)), callback='parse_content'),
        Rule(LinkExtractor(allow=(r'https://news.cnblogs.com/n/\d+',)), callback='parse_content'),
    )