---
title: Nginx那些事儿
categories:
- Web前端
tags:
- Nginx
- Http服务器
- 反向代理
toc: true
---
<Excerpt in index | > 
本文介绍Nginx的功能<!-- more -->
<The rest of contents | 余下全文>

最近需要将一个前端项目部署到服务器，后台部署在另一台服务器。查阅相关资料后，了解到需要一个http服务器，Apache、Nginx甚至python自带http服务器都可以实现。
但是最常用的是Nginx，并且Nginx除了http服务器功能之后，还可以实现负载均衡、反向代理、正向代理的功能，因此毫不犹豫地选择Nginx。

# Nginx的安装
Ubuntu上Nginx的安装非常简单，代码如下。其他Linux系统貌似复杂一点。
```
# 更新apt-get
sudo apt-get update

# 安装nginx
sudo apt-get install nginx

# 查看nginx
whereis nginx
```

查看nginx的结果如下图
![whereis nginx](https://raw.githubusercontent.com/poptensory/MarkdownPictures/master/nginx_server.png "")
其中`/etc/nginx`包含最重要的配置文件。

# 静态资源部署(http服务器)
静态资源，例如文本、图片、html页面等。部署在服务器，就可以通过[服务器IP+端口号]的方式访问这些资源了。需要做的有4步。
1. 上传静态资源到服务器
例如，我们将web文档(HTML+JS)上传到主目录下的一个目录，上传之后的文件结构和内容如下：
```
/home/tensory/MyWeb/web/a.html
/home/tensory/MyWeb/web/b.html
/home/tensory/MyWeb/web/c.html
```
服务器的IP地址为`192.168.1.111`。
我们希望实现：浏览器地址栏输入`192.168.1.111:9990/web/a.html`能够看到页面`a.html`，诸如此类。


2. 配置
进入/etc/nginx下，找到`nginx.conf`文件，进行如下配置(相应位置改为自己的)：
```
# 设置用户
user tensory tensory;

# 设置进程数
worker_processes  4;

# 日志文件的目录，这个目录可能需要自己建立，不然后面无法启动Nginx
error_log  logs/error.log  notice;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;    
    keepalive_timeout  65;

	# 重点修改这里
     server {
     	
     	# IP和端口号设置：IP不能带有http等前缀；可以不设置端口，默认端口80，这样访问的时候地址栏可以不用写端口，默认80。
        server_name 192.168.1.111;
        listen	9990;
        
        location / {
        	# 改为自己的
            root        /home/tensory/MyWeb;
            
            # 这个index.html为Nginx自带的页面，不用改动
            index       index.html;
        }   
    }   
}   
```
配置文件完成，注意别漏掉或多写{或}。

3. 测试、载入配置

对于配置文件，一定要进行测试，然后再载入Nginx。代码如下(方便起见，可以写成脚本)：
```
# 测试
sudo nginx -t

# 测试如果提示成功，则载入配置(载入并重启)
sudo nginx -s reload
```

4. 测试访问
浏览器输入`192.168.1.111:9990/web/a.html`就能看到a.html页面，b.html和c.html同样可以看到。

# 反向代理(ajax跨域问题)
部署完成之后，能看到页面，但是a.html里面的ajax无法获取数据，原因是禁止跨域访问。

假设a.html文件中包含ajax请求，请求的是API是后台提供的，且后台部署在`192.168.1.222`服务器上，端口号为`8880`。假设ajax请求如下：
```
$.ajax{
	url:http://192.168.1.222:8880/api/getMoney,
	type:"GET",
	......
	success: function(data){
	......
	}
}
```
解决办法即反向代理。将ajax请求的url改为：`(端口号一定要和静态资源访问端口号相同，都是9990)`
```
$.ajax{
	url:http://192.168.1.111:9990/api/getMoney,		// 注意这个端口号一定要和静态资源访问端口号相同，不然产生新的跨域。
	type:"GET",
	......
	success: function(data){
	......
	}
}
```
在前面的基础上，修改Nginx的配置文件`nginx.conf`，如下：
```
# 设置用户
user tensory tensory;

# 设置进程数
worker_processes  4;

# 日志文件的目录，这个目录可能需要自己建立，不然后面无法启动Nginx
error_log  logs/error.log  notice;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;    
    keepalive_timeout  65;

	# 重点修改这里
     server {
     	
     	# IP和端口号设置：IP不能带有http等前缀；可以不设置端口，默认端口80，这样访问的时候地址栏可以不用写端口，默认80。
        server_name 192.168.1.111;
        listen	9990;
        
        location / {
        	# 改为自己的
            root        /home/tensory/MyWeb;
            
            # 这个index.html为Nginx自带的页面，不用改动
            index       index.html;
        }
        
        #####################################
        # 添加下面的location内容			#
        #####################################
        
        location /api {
        	# 改为后台api所在的服务器的IP+端口
            proxy_pass	http://192.168.1.222:8880;
            
            # cookie设置
        	proxy_cookie_domain	192.168.1.111	192.168.1.222;
        }    
    }   
}   
```

测试并重新载入配置文件，就可以通过ajax访问到后台数据了。上面这个过程叫做，反向代理。

通过设置转发路径proxy_pass，将进入`http://192.168.1.111:9990`的请求转发到proxy_pass的路径，即：`http://192.168.1.222:8880`，即后端API的路径。

# 正向代理
反向代理反过来。

# 负载均衡
负载均衡解决高并发请求，做法是将被请求方复制，置于多台服务器，请求的时候按照一定的概率(可以设置)或者轮询的方式访问这些服务器。
好处是：
1. 提高并发量：多台服务器分担压力。
2. 稳定：如果某台服务器宕掉，会转发到其他服务器。