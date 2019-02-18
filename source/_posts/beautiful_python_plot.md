---
title: 美就是生产力——Python绘图
categories:
- Python
tags:
- Python
- 绘图
- matplotlib
toc: true
---
<Excerpt in index | > 
如何用Python绘制自定义精美图形<!-- more -->
<The rest of contents | 余下全文>

简陋的图形，就像是这样的。
<div align=center>
<img alt='not good' src="https://raw.githubusercontent.com/poptensory/OnlineResource/master/BeautifulPythonPlot/ugly.png" width = "800" div align=right />
</div>

简陋的图形，虽然也能传达图片的含义，但是没有给人愉悦的感觉。精美的图形，是这样的：
<div align=center>
<img alt='better' src="https://raw.githubusercontent.com/poptensory/OnlineResource/master/BeautifulPythonPlot/better.png" width = "800" div align=right />
</div>

代码模板如下：
```Python
# PlotSettings.py
# 设置模块，可以设置一些字体大小，以及自定义图例

import matplotlib.font_manager as ft

# x轴和y轴的说明文字字体
labelsize = 30

# x轴和y轴的数字刻度字体
ticklabelsize = 30

# 图片容器的位置
left = 0.15
bottom = 0.17
top = 0.93
right = 0.97

# 图例字体大小
legendsize = 30


def set_plot(plt,ax):

    #get object
    xlabel = ax.get_xlabel()
    ylabel = ax.get_ylabel()

    ax.set_xlabel(xlabel, fontsize=labelsize)
    ax.set_ylabel(ylabel, fontsize=labelsize)

    plt.xticks(fontsize=ticklabelsize)
    plt.yticks(fontsize=ticklabelsize)

    # 调整子图的绝对位置
    plt.subplots_adjust(top=top, bottom=bottom, right=right, left=left)

    handles, labels = ax.get_legend_handles_labels()

    # 精确设置图例：各个字段的含义丰富
    ax.legend(handles,
              labels,
              prop=ft.FontProperties(size=legendsize, weight='light'),
              loc=0,
              borderaxespad=0.05,
              labelspacing=0.05,
              columnspacing=0.005,
              handletextpad=0.05,
              borderpad=0.1,
              ncol=1)
```

``` Python

# example.py
# Example如下

# @Encoding: utf-8
# @Author  : tensory
# @Time    : 2019/1/14 9:13

from BeautifulPlot import PlotSettings
import matplotlib.pyplot as plt

if __name__ == "__main__":
    # 生成测试数据
    x = [1, 2, 3, 4, 5]
    y1 = [e+3 for e in x]
    y2 = [2*e - 1 for e in x]

    # 创建一个figure对象，背景为 白色，比例为 12:9
    fig = plt.figure(facecolor='white', figsize=(12, 9))
    # 创建一个子图: 111表示1x1的图的第1个图
    ax = fig.add_subplot(111)

    # 设置标题和横纵轴的说明文字。
    plt.title('No title QAQ', fontsize=30)
    plt.xlabel('Time (days)')
    plt.ylabel('The network traffic')

    # 设置横纵轴的范围
    plt.ylim([0, 10])
    plt.xlim([1, 5])

    # 绘制每条曲线，并设置legend的内容，marker的样式和大小，线条的样式和宽度。注意：自带Latex公式语法
    plt.plot(x, y1, label='$y_1=x*x-x$', marker='s', markersize='10', linestyle='-', linewidth=5)
    plt.plot(x, y2, label='$y_2=x+10$',marker='p', markersize='10', linestyle='-', linewidth=5)

    # 设置坐标轴、坐标轴说明、图lengend的大小等
    PlotSettings.set_plot(plt, ax)

    plt.show()

    save_path_jpg = 'result.jpg'
    # edgecolor设置周围没有空白，不然左边会有多的空白
    fig.savefig(save_path_jpg, facecolor=fig.get_facecolor(), edgecolor='none')
```

更多的设置，可以直接参考matplotlib的turtorials。
https://matplotlib.org/tutorials/index.html