---
title: Python装饰器(初步)
categories:
- Python
tags:
- Python
- 装饰器
- 函数式编程
toc: true
---
<Excerpt in index | > 
简单介绍decorator的概念<!-- more -->
<The rest of contents | 余下全文>
现在有两个函数，如下
```python
def say_hello():
    print('hello')

def say_goodbye():
    print('goodbye')
```
现在要加一个功能，在调用每个函数的时候，打印'xxx called...'，xxx是函数名。前提是不能修改这两个函数里面的东西。实现方式是定义一个debug()函数，如下
```python
def debug(func):
    print(func.__name__ + ' called...')
    func()

def say_hello():
    print('hello')

def say_goodbye():
    print('goodbye')

debug(say_hello)
debug(say_goodbye)
```

这样可以实现要求的功能，但是问题很显然，就是现在调用的函数不是say_hello()和say_goodbye()本身了，而是外部嵌套的，这不是我们想要的，我们想要的是，调用的还是这两个函数名。实现如下
```python
def debug(func):
    def wrapper():
        print(func.__name__ + ' called...')
        func()
    return wrapper  # 返回一个函数名

def say_hello():
    print('hello')

def say_goodbye():
    print('goodbye')

say_hello = debug(say_hello)    # 将say_hello函数名作为参数传给wrapper函数
say_hello()                     # 通过执行say_hello()执行wrapper()

say_goodbye = debug(say_goodbye)
say_goodbye()
```
这样看起来还不错，但是，
say_goodbye = debug(say_goodbye)
这个语句看起来依然和很臃肿，我们不希望有这个东西存在，于是就有了装饰器，代码如下
```python
def debug(func):
    def wrapper():
        print(func.__name__ + ' called...')
        func()
    return wrapper  # 返回一个函数名

@debug
def say_hello():
    print('hello')

@debug
def say_goodbye():
    print('goodbye')

say_hello()

say_goodbye()
```
现在，这是一个简单的装饰器。但是在wrapper内部，我们执行func()，这样写没问题，因为在本例中，func为say_hello和say_goodbye，二者没有返回任何东西。但是看下面的例子
```python
def debug(func):
    def wrapper():
        print(func.__name__ + ' called...')
        func()
    return wrapper  # 返回一个函数名

@debug
def get_apple():
    return "apple"

res = get_apple()
print(res)
```
结果如下图：
![r1](https://raw.githubusercontent.com/poptensory/OnlineResource/master/decorator1.png)

因为get_apple()返回了字符串，而在wrapper()内部执行的时候，没有将get_apple()函数执行的结果进行return，所以为None。所以，在写decorator的时候，最好在wrapper内部返回这个函数执行的
结果，即使该函数不返回任何东西，也没有影响。代码如下
```python
def debug(func):
    def wrapper():
        print(func.__name__ + ' called...')
        return func()		# 在这个地方写成return func()而不是func()
    return wrapper  		# 返回一个函数名

@debug
def get_apple():
    return "apple"

res = get_apple()
print(res)
```
正确结果如下图：
![r2](https://raw.githubusercontent.com/poptensory/OnlineResource/master/decorator2.png)

对带有参数的函数，添加装饰器的方法如下
```python
def debug(func):
    def wrapper(sth):   # 添加参数
        print(func.__name__ + ' called...')
        return func(sth)
    return wrapper  # 返回一个函数名

@debug
def get_sth(sth):   # 添加参数
    return sth

print(get_sth("apple"))
print(get_sth("banana"))
```
结果如下图
![r3](https://raw.githubusercontent.com/poptensory/MarkdownPictures/master/decorator3.png)