---
title: 'Css,Xpath'
date: 2018-05-03 09:39:28
tags: 
---
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
	
