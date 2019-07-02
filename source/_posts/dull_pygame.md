---
title: PyGame
categories:
- Python
tags:
- Python
- 游戏
toc: true
---
<Excerpt in index | > 
PyGame实现挡板弹球<!-- more -->
<The rest of contents | 余下全文>

下午用pygame做了一个挡板弹球小游戏，挺好玩的，效果如下。

![图的说明](https://raw.githubusercontent.com/poptensory/OnlineResource/master/2019-07-02_19-17-37.gif  "可选标题")

##### pygame的安装

```PYthon
# 第一种
pip install pygame
# 第二种
# 下载whl文件：https://pypi.org/project/pygame/1.9.4/#files
# 然后手动安装
pip install *.whl
```



##### 少废话，看代码

```python
import pygame
import random
from tkinter import  *

# 选择难度
root = Tk('11')
v = IntVar()
Radiobutton(root, text='简单', variable=v, value=1, ).pack(anchor=W)
Radiobutton(root, text='中等', variable=v, value=2, ).pack(anchor=W)
Radiobutton(root, text='很难', variable=v, value=3, ).pack(anchor=W)
mainloop()
v = v.get()
if v==1:
    speed = [4, 4]
    size = width, height = 640, 480  # 设置窗口大小
elif v==2:
    speed = [8, 8]
    size = width, height = 640, 480  # 设置窗口大小
else:
    speed = [12, 12]
    size = width, height = 800, 600  # 设置窗口大小

# 提示一下要开始游戏了，不然输个措手不及
import tkinter.messagebox
tkinter.messagebox.showinfo('提示','准备好开始游戏了吗')


# 开始游戏部分
pygame.init()  # 初始化pygame
screen = pygame.display.set_mode(size)  # 显示窗口
color = (0, 0, 0)  # 设置颜色
ball = pygame.image.load('ball.png')  # 加载图片
ballrect = ball.get_rect()  # 获取矩形区域
ballrect.left = int(random.random()*width)

clock = pygame.time.Clock()  # 设置时钟

# 挡板设置
board = pygame.image.load('board.png')
boardrect = board.get_rect()
boardrect.bottom = height	# 设置挡板在底部

scores = 0	# 分数初始化

while True:  # 死循环确保窗口一直显示
    clock.tick(60)  # 每秒执行60次。动画是静态图片快速移动形成的，这句话是这个意思。
    for event in pygame.event.get():  # 遍历所有事件
        if event.type == pygame.QUIT:  # 如果单击关闭窗口，则退出
            sys.exit()

    ballrect = ballrect.move(speed)  # 移动小球

    # 碰到左右边缘
    if ballrect.left < 0 or ballrect.right > width:
        speed[0] = -speed[0]

    # 碰到上边缘
    if ballrect.top < 0:
        speed[1] = -speed[1]

    # 碰到下边缘
    if ballrect.bottom > height:
        # 如果在挡板区间内部，得1分
        if ballrect.left > boardrect.left and ballrect.right < boardrect.right:
            scores += 1
            speed[1] = -speed[1]
        # 否则 失败
        else:
            msg = str(scores)
            if scores == 0:
                msg += ' 运气也是实力的一部分哦'
            tkinter.messagebox.showinfo('得分', msg)
            break

    # 根据方向键 移动挡板
    if event.type == pygame.KEYDOWN:
        speed2 = [1, 0]
        if event.key == 276:    # 左方向键
            speed2 = [-8, 0]
        elif event.key == 275:  # 右方向键
            speed2 = [8, 0]
        boardrect = boardrect.move(speed2)  # 移动挡板

    screen.fill(color)
    screen.blit(board, boardrect)
    screen.blit(ball, ballrect)
    pygame.display.flip()  # 更新全部显示

pygame.quit()
```

##### 参考

1. <https://www.pygame.org/wiki/GettingStarted> 
2. <https://blog.csdn.net/zha6476003/article/details/82940350> 



看懂了吧，程序员的生活就是这么的朴实无华，且枯燥。