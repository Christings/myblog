---
title: Request详解
date: 2018-06-15 17:12:40
tags: Requests
categories:
		- Python
		- Requests
---
## 一、在Python2中使用urllib和urllib2进行http请求
    
    (1) urllib2可以接受一个Request类的实例来设置URL请求的headers。
    (2) urllib仅可以接受URL，这意味着你不可以伪装你的User Agent字符串等。
    (3) urllib 有urlencode,而urllib2没有，这也是为什么总是urllib，urllib2常会一起使用的原因。

```
    req=urllib2.Request(url=url,data=postdata,headers=headers)
    result=urllib2.urlopen(req)
```

## 二、在Python3中使用urllib和http进行http请求

    http包会处理所有客户端--服务器http请求的具体细节，其中：
        （1）client会处理客户端的部分
        （2）server会协助你编写Python web服务器程序
        （3）cookies和cookiejar会处理cookie，cookie可以在请求中存储数据

    urllib包是基于http的高层库，它有以下三个主要功能：
        （1）request处理客户端的请求
        （2）response处理服务端的响应
        （3）parse会解析url

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

3.综合
```
import urllib.parse  
import urllib.request  

url = 'http://www.example.com/'  
user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'  
values = {  
'act' : 'login',  
'login[email]' : '',  
'login[password]' : ''  
}  
headers = { 'User-Agent' : user_agent }  
data = urllib.parse.urlencode(values)  

req = urllib.request.Request(url, data, headers)  
response = urllib.request.urlopen(req)  
the_page = response.read()  
print(the_page.decode("utf8")
```
    
## 三、第三方库Requests

    (1) Requests 使用的是 urllib3，继承了urllib2的所有特性。
    (2) 支持HTTP连接保持和连接池；支持使用cookie保持会话；支持文件上传；支持自动确定响应内容的编码；支持国际化的 URL 和 POST 数据自动编码。

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

    print(r.url) # 执行结果为：http://httpbin.org/get?k1=v1&k2=v2&k4=v4&k4=v5
    print(r.text) # 打印解码后的返回数据
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

