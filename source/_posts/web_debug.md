---
title: 前端调试注意事项
categories:
- Web前端
tags:
- 前端
- API测试
- ajax
toc: true
---
<Excerpt in index | > 
前端通过ajax测试后台API<!-- more -->
<The rest of contents | 余下全文>

# 跨域访问
设置Chrome浏览器可以跨域，用WebStorm可以存储cookie，只用Chrome无法写入cookie。
涉及调用后台API的都要跨域！！！

# 用好浏览器的F12
F12-network会显示http请求的细节，查找访问失败的原因。好好练习抓包分析。query的字段"name=xxx"的等号别漏掉了。

# 刷新页面没有？
多多刷新页面，经常清除Chrome缓存。利用alert等测试页面是否真正刷新。