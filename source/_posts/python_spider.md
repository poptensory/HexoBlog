---
title: Python爬虫笔记
categories:
- Python
tags:
- 爬虫
- Python
toc: true
---
<Excerpt in index | > 
Python爬虫的一点笔记<!-- more -->
<The rest of contents | 余下全文>

包括requests和urllib的使用，设置常用的反爬虫机制。
```python
# @Encoding: utf-8
# @Author  : tensory
# @Time    : 2018/11/14 20:40


import requests
import time
import random
import urllib
from bs4 import BeautifulSoup

cookie = 'xxx'

# User-Agent
ua1 = "User-Agent:Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_8; en-us) AppleWebKit/534.50 (KHTML, like Gecko) Version/5.1 Safari/534.50"
ua2 = "User-Agent:Mozilla/5.0 (Windows; U; Windows NT 6.1; en-us) AppleWebKit/534.50 (KHTML, like Gecko) Version/5.1 Safari/534.50"
ua3 = "User-Agent:Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0"
ua4 = "User-Agent:Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0)"
ua5 = "User-Agent:Mozilla/5.0 (Windows NT 6.1; rv:2.0.1) Gecko/20100101 Firefox/4.0.1"
ua6 = "User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_0) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.56 Safari/535.11"
ua_list = [ua1, ua2, ua3, ua4, ua5, ua6]

# IP代理池
ip_list = ['58.56.149.198:53281', '123.7.61.8:53281', '115.46.96.46:8123', '171.38.24.185:8123', '171.38.24.185:8123', 
           '111.75.223.9:35918', '116.192.175.93:41944', '36.33.32.158:59019', '1.198.72.4:53128', '49.71.81.43:3128', 
           '60.216.101.46:59351', '119.1.97.193:60916', '182.88.89.7:8123', '114.99.31.129:34596', '42.59.86.81:1133', '182.88.89.189:8123',
           '121.237.136.34:18118', '119.254.94.71:42788', '175.148.71.214:1133', '175.155.138.60:1133', '182.88.89.4:8123', '121.31.192.27:8123',
           '221.239.86.26:46164', '220.166.195.160:8118', '114.225.170.44:53128', '180.168.13.26:8000', '182.207.232.135:50465', 
           '121.225.26.169:3128', '124.232.133.201:30819', '112.67.128.30:8123', '210.22.176.146:32153', '171.38.25.79:8123', '180.119.65.184:3128', 
           '221.229.18.126:3128', '106.75.226.36:808', '115.223.82.100:8010', '42.51.195.184:808', '182.88.134.189:8123', '175.165.129.155:1133', 
           '221.1.200.242:38652', '222.76.204.110:808', '116.192.171.51:48565', '122.241.73.67:808', '112.98.126.100:41578', '115.46.65.155:8123', 
           '113.108.242.36:47713', '113.103.14.36:3128', '180.119.65.76:3128', '101.92.105.68:8123', '221.229.18.170:808', '171.39.78.245:8123', 
           '61.170.179.89:50799', '117.114.149.10:43727', '211.147.239.101:57281', '219.147.8.148:8000',
           '115.46.98.154:8123', '42.59.87.72:1133', '183.172.208.129:8118', '218.59.228.18:61976', '140.207.25.114:41471']

url = "xxxxxxxxxxxxxxxxx"
ua = ua_list[random.randint(0, len(ua_list) - 1)]
ip = "https://"+ip_list[random.randint(0, len(ip_list)-1)]
proxy = {"https":ip}

# 设置headers，包括UA, Cookie, Connection
headers = {
    "User-Agent": ua,
    "Cookie": cookie,
    "Connection": "close"
}

# 随机休眠
time.sleep(random.random())
# 获取HTML(代理在此设置)
html = requests.get(url, headers=headers, proxy=proxy).content.decode()

# 解析
soup = BeautifulSoup(html, 'lxml')
res = []
for link in soup.find_all("td", class_="hall-time"):
    res.append(link.contents[1].string)

# 如果是Ajax异步加载的
url = ""
time.sleep(random.random())
req = urllib.request.Request(url)
req.add_header('X-Requested-With', 'XMLHttpRequest')
response = urllib.request.urlopen(req)

# 难以爬取的网站例如动态加载的页面，可以通过PhantomJS+Selenium模拟用户行为，执行下载得到的HTML+JS代码
```