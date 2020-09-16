---
title: Requests
date: 2018-06-15 17:12:40
tags: Python3
categories:
		- Python3
		- Requests
---
## Python标准库中的urllib模块提供http请求

1.get请求

	import urllib.request

    f = urllib.request.urlopen('http://www.webxml.com.cn//webservices/qqOnlineWebService.asmx/qqCheckOnline?qqCode=424662508')
    result = f.read().decode('utf-8')
2.发送带请求头的get请求

    import urllib.request

    req = urllib.request.Request('http://www.example.com/')
    req.add_header('Referer','http://www.python.org/')
    r = urllib.request.urlopen(req)
    result = f.read().decode('utf-8')
    
## Requests
1.安装

    pip install requests
    pip install --upgrade requests
2.请求类型

    POST，GET，PUT，DELETE，HEAD 以及 OPTIONS
3.GET请求
    
    import requests

    # 无参数示例
    r = requests.get('https://httpbin.org/get')
    # 有参数示例
    r = requets.get('http://httpbin.org/get', params=d)

    传递URL参数：
        在URL中常见?符号，http://httpbin.org/get?key=val 这种带有?传递关键字参数的方式，requests可以通过params实现。
    d = {'k1':'v1', 'k2':'v2', 'k3':None, 'k4':['v4','v5']}  
        # 字典中键值为None的键不会被添加到URL中
        # 多个键值中间用&符号连接
        # 键值可是列表 例如'k4'
    print(r.url)
    执行结果为：http://httpbin.org/get?k1=v1&k2=v2&k4=v4&k4=v5
4.POST请求
    
    import requests
 
    # 基本样例    
    payload = {'key1': 'value1', 'key2': 'value2'}
    ret = requests.post("http://httpbin.org/post", data=payload)
    print(ret.text)
    # 输出结果
    {
      "args": {}, 
      "data": "", 
      "files": {}, 
      "form": {
        "key1": "value1", 
        "key2": "value2"
      }, 
      "headers": {
        "Accept": "*/*", 
        "Accept-Encoding": "gzip, deflate", 
        "Connection": "close", 
        "Content-Length": "23", 
        "Content-Type": "application/x-www-form-urlencoded", 
        "Host": "httpbin.org", 
        "User-Agent": "python-requests/2.18.4"
      }, 
      "json": null, 
      "origin": "不告诉你这里返回的是你的IP地址", 
      "url": "http://httpbin.org/post"
    }

    # 发送请求头和数据
    import requests
    import json

    url = 'https://api.github.com/some/endpoint'
    payload = {'some': 'data'}
    headers = {'content-type': 'application/json'}
    ret = requests.post(url, data=json.dumps(payload), headers=headers)
    print(ret.text)
    print(ret.cookies)
    # 输出结果
    {"message":"Not Found","documentation_url":"https://developer.github.com/v3"}
    <RequestsCookieJar[]>
5.响应内容
requests模块的返回对象是一个Response对象，可以从这个对象中获取需要的信息。下面 r 代表Response对象。

    r.text        文本响应内容
    r.context     二进制响应内容
    r.json()      JSON响应内容
    r.raw         原始相应内容
6.响应状态码
    
    r=requests.get('http://www.baidu.com')
    r.status_code

