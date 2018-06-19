---
title: python-turtle
date: 2018-06-19 10:48:21
categories: python
tags:
    - python
    - turtle
    - 画图
---

[TOC]

![images](https://images2017.cnblogs.com/blog/1182370/201709/1182370-20170902065255655-264716312.png)

海龟绘图(Turtle Graphics),turtle库是python的内部库,使用导入即可 import turtle

先说明一下turtle绘图的基础知识:

> ## 1.画布(canvas)
画布就是turtle为我们展开用于绘图区域, 我们可以设置它的大小和初始位置

> ### 1.1 设置画布大小

reensize(canvwidth=None, canvheight=None, bg=None)

参数分别为画布的宽(单位像素), 高, 背景颜色

如:
```python
turtle.screensize(800, 600, "green")
turtle.screensize() #返回默认大小(400, 300)
```

> ## 2. 画笔

> ### 2.1 画笔的状态

在画布上,默认有一个坐标原点为画布中心的坐标轴, 坐标原点上有一只面朝x轴正方向小乌龟. 这里我们描述小乌龟时使用了两个词语:`坐标原点`(位置),面朝`x轴正方向(方向)`, turtle绘图中, 就是使用位置方向描述小乌龟(画笔)的状态

> ### 2.2 画笔的属性

画笔(画笔的属性，颜色、画线的宽度)

- turtle.pensize()：设置画笔的宽度；
- turtle.pencolor(); 没有参数传入,返回当前画笔颜色,传入参数设置画笔颜色,可以是字符串如"green", "red",也可以是RGB 3元组,
- turtle.speed(speed): 设置画笔移动速度,画笔绘制的速度范围[0,10]整数, 数字越大越快

```python
>>> pencolor('brown')
>>> tup = (0.2, 0.8, 0.55)
>>> pencolor(tup)
>>> pencolor()
'#33cc8c'
```

> ### 2.3 绘图命令

操纵海龟绘图有着许多的命令,这些命令可以划分为3种:一种为运动命令，一种为画笔控制命令,还有一种是全局控制命令

**(1)画笔运动命令:**


命令 | 说明
--| --
turtle.forward(distance) |  向当前画笔方向移动distance像素长
turtle.backward(distance)   | 向当前画笔相反方向移动distance像素长度
turtle.right(degree) |  顺时针移动degree°
turtle.left(degree) | 逆时针移动degree°
turtle.pendown() |  移动时绘制图形,缺省时也为绘制
turtle.goto(x,y) |  将画笔移动到坐标为x,y的位置
turtle.penup()  | 移动时不绘制图形,提起笔，用于另起一个地方绘制时用
turtle.speed(speed) | 画笔绘制的速度范围[0,10]整数
turtle.circle() | 画圆,半径为正(负),表示圆心在画笔的左边(右边)画圆

**(2)画笔控制命令:**


命令 | 说明
--| --
turtle.pensize(width) | 绘制图形时的宽度
turtle.pencolor() | 画笔颜色
turtle.fillcolor(colorstring) | 绘制图形的填充颜色
turtle.color(color1, color2) | 同时设置pencolor=color1, fillcolor=color2
turtle.filling() | 返回当前是否在填充状态
turtle.begin_fill() | 准备开始填充图形
turtle.end_fill() | 填充完成；
turtle.hideturtle() | 隐藏箭头显示；
turtle.showturtle() | 与hideturtle()函数对应

**(3) 全局控制命令**

命令 | 说明
--| --
turtle.clear()  | 清空turtle窗口，但是turtle的位置和状态不会改变
turtle.reset()  | 清空窗口，重置turtle状态为起始状态
turtle.undo()   | 撤销上一个turtle动作
turtle.isvisible() | 返回当前turtle是否可见
stamp() | 复制当前图形
turtle.write(s[,font=("font-name",font_size,"font_type")]) |    写文本，s为文本内容，font是字体的参数，里面分别为字体名称，大小和类型；font为可选项, font的参数也是可选项


> ## 3. 命令详解

> ### 3.1 turtle.circle(radius, extent=None, steps=None)
描述: 以给定半径画圆
参数:
radius(半径); 半径为正(负),表示圆心在画笔的左边(右边)画圆
extent(弧度) (optional);
steps (optional) (做半径为radius的圆的内切正多边形,多边形边数为steps)

举例:
```python
circle(50) # 整圆;
circle(50,steps=3) # 三角形;
circle(120, 180) # 半圆
```

> ## 4. 绘图举例

> ### 4.1 太阳花

```python
import turtle as t
import time
t.color("red", "yellow")
t.speed(10)
t.begin_fill()
for _ in range(50):
    t.forward(200)
    t.left(170)
end_fill()
time.sleep(1)
```

> ### 4.2 绘制小蟒蛇

```python
import turtle

def drawSnake(rad, angle, len, neckrad):
    for _ in range(len):
        turtle.circle(rad, angle)
        turtle.circle(-rad, angle)
    turtle.circle(rad, angle/2)
    turtle.forward(rad/2)  # 直线前进
    turtle.circle(neckrad, 180)
    turtle.forward(rad/4)

if __name__ == "__main__":
   turtle.setup(1500, 1400, 0, 0)
   turtle.pensize(30)  # 画笔尺寸
   turtle.pencolor("green")
   turtle.seth(-40)    # 前进的方向
   drawSnake(70, 80, 2, 15)
```

> ### 4.3 绘制五角星

```python
import turtle
import time


turtle.pensize(5)
turtle.pencolor("yellow")
turtle.fillcolor("red")

turtle.begin_fill()

for _ in range(5):
    turtle.forward(200)
    turtle.right(144)
turtle.end_fill()
time.sleep(2)

turtle.penup()
turtle.goto(-150,-120)
turtle.color("violet")
turtle.write("Done", font=('Arial', 40, 'normal'))
time.sleep(1)
```
