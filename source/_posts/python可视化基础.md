title: python可视化基础
author: ztq
tags:
  - python
categories:
  - python基础
date: 2021-04-17 18:45:00

---

# <center>Python可视化基础</center>

# 1 常用的Python可视化库

* <b>Matplotlib</b>
  是一个最基础的Python可视化库，作图风格接近MATLAB，所以称为matplotlib。一般都是从matplotlib上手Python数据可视化，然后开始做纵向与横向拓展。
* <b>Pandas</b>: 
  easy to use interface, built on Matplotlib
* <b>Seaborn</b>: 
  high-level interface, great default styles
* <b>ggplot</b>:
  based on R’s ggplot2, uses Grammar of Graphics
* <b>Plotly</b>: 
  创建交互动态图
* <b>Pyecharts</b>
  网页中的动态图

# 2 Jupyter Notebook

Jupyter这个名字是它要服务的三种语言的缩写：Julia，PYThon和R，这个名字与“木星（jupiter）”谐音。 

 * <h3> Jupyter Notebook打开方式</h3>

&emsp;&emsp;Anaconda prompt 输入Jupyter notebook

&emsp;&emsp;通过Anaconda Navigator打开

 * <h3>更改Jupyter Notebook的默认路径</h3>

&emsp;（1）找到配置文件的位置
jupyter notebook --generate-config
若不是首次执行，会出现是否覆盖的提示，选择No<br/>

&emsp;（2）打开配置文件，进下如下设置
c.NotebookApp.notebook_dir = '换成自己的目录'，保存
我最后用的格式是E:\\zxc\\dir这种形式<br/>

&emsp;（3）关闭重启jupyter  notebook。 

# 3 matplotlib

  <h3> 3.1 Matplotlib简介</h3>

&emsp;Matplotlib是 Python 2D-绘图领域使用最广泛的套件，可以简易地将数据图形化，并且提供多样化的输出格式。<br/>
&emsp;matplotlib有两个接口，一个是状态机层的接口，通过<font color=red>pyplot</font>模块来进行管理；<br/>
&emsp;一个是面向对象的接口，通过<font color=red>pylab</font>模块将所有的功能函数全部导入其单独的命名空间内。

在线文档https://matplotlib.org/<br/>

 <h3> 3.2 Matplotlib安装</h3>

（1） 使用pip安装

&emsp;1）Win+R输入cmd进入到CMD窗口下，执行python -m pip install -U pip setuptools进行升级。

&emsp;2）输入python -m pip install matplotlib进行自动的安装，系统会自动下载安装包

（2）使用conda安装
conda install matplotlib

<h3> 3.3 Matplotlib图表结构</h3>

Matplotlib基本图表结构<br/>
包括坐标轴（X轴、Y轴）、坐标轴标签（axisLabel）、
坐标轴刻度（tick）、坐标轴刻度标签（tick label）、绘图区（axes）、画布（figure）。

![avatar](C:/Users/zheng/Desktop/aaa/structure.png)

在绘图结构中，figure创建窗口，subplot创建子图。所有的绘画只能在子图上进行。plt表示当前子图，若没有就创建一个子图。

Figure：面板(图)，matplotlib中的所有图像都是位于figure对象中，一个图像只能有一个figure对象。

Subplot：子图，figure对象下创建一个或多个subplot对象(即axes)用于绘制图像


<h3> 3.4 Matplotlib基本应用</h3>

<b> 导入matplotlib</b>


```python
import matplotlib as mpl
print("matplotlib version",mpl.__version__)
```

    matplotlib version 3.2.2



```python
import matplotlib.pyplot as plt#为方便简介为plt
import numpy as np#画图过程中会使用numpy
import pandas as pd#画图过程中会使用pandas
```

<b>（1）画一个简单的图形</b>

1)简单的使用


```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(-1,1,50)#从(-1,1)均匀取50个点
y = 2 * x

plt.plot(x,y)
plt.show()
```


![png](/img/output_30_0111.png)
    


2）这里我们继续通过画出一个正弦曲线图来讲解下基本用法。<br/>
通过 np.linspace 方式生成 x，它包含了 50 个元素的数组，这 50 个元素均匀的分布在 [0, 2pi] 的区间上。然后通过 np.sin(x) 生成 y。


```python
x = np.linspace(0, 2 * np.pi, 50)
y = np.sin(x)
```

有了 x 和 y 数据之后，我们通过 plt.plot(x, y) 来画出图形，并通过 plt.show() 来显示


```python
plt.plot(x, y)
plt.show()
```


![png](/img/output_34_0111.png)
    


<center><b>Plot的基本用法</b></center>

折线图和散点图 
plot(x轴数据，y轴数据，展现形式)，参数是可以叠加的

>>> plot(x, y)        # plot x and y using default line style and color
>>> plot(x, y, 'bo')  # plot x and y using blue circle markers
>>> plot(y)           # plot y using x as index array 0..N-1
>>> plot(y, 'r+')     # ditto, but with red plusses

```python
plt.plot(x,y,'b:.')
plt.show()
```


![png](/img/output_37_0111.png)
    


<center><b>线条相关属性标记设置</b></center>

常用属性设置
颜色参数：
			c-cyan--青色
			r-red--红色
			m-magente--品红
			g-green--绿色
			b-blue--蓝色
			y-yellow--黄色
			k-black--黑色
			w-white--白色
线条样式：
			-  实线
			-- 虚线
			-. -.式
			:  细小虚线
            o  circle 
点的样式：
			s--方形
			h--六角形
			H--六角形
			*--星形
			+--加号
			x--x形
			d--菱形
			D--菱形
			p--五角星
            . 点标记
            , 像素标记
            o 圆标记

 更多请参见在线文档https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.plot.html#matplotlib.pyplot.plot
<b>（2）在一张图里绘制多图形</b>

有时候，可能需要在一个图纸里绘制多个图形，这里我们同时绘制了 (x, y), (x, y * 2)两个图形


```python
plt.plot(x, y)
plt.plot(x, y * 2)
plt.show()
```


![png](/img/output_42_0111.png)
    


绘制出图形之后，我们可以自己调整更多的样式，比如颜色、点、线。


```python
plt.plot(x, y, 'y*-')
plt.plot(x, y * 2, 'm--')
plt.show()
```


![png](/img/output_44_0111.png)
    


<b>（3）设置Figure</b>

  你可以认为Matplotlib绘制的图形都在一个默认的 figure 中，当然了，你可以自己创建 figure，好处就是可以控制更多的参数，常见的就是控制图形的大小，这里创建一个 figure，设置大小为 (6, 3)


```python
plt.figure(figsize=(6, 3))
plt.plot(x, y)
plt.plot(x, y * 2)
plt.show()
```


![png](/img/output_47_0111.png)
    


<b>（4）设置标题</b>

直接通过 plt.title 即可设置图形标题


```python
plt.plot(x, y)
plt.plot(x, y * 2)
plt.title("sin(x) & 2sin(x)")
plt.show()
```


![png](/img/output_50_0111.png)
    


<b>（5）设置坐标轴</b>

我们来看下如何设置坐标轴的范围以及名称。

坐标轴范围设置
plt.axis([xmin,xmax,ymin,ymax])<br/>
也可以通过xlim(xmin,xmax)，ylim(xmin,xmax)方法设置坐标轴范围

通过 xlabel 和 ylabel 来设置轴的名称。


```python
plt.plot(x, y)
plt.plot(x, y * 2)

plt.xlim((0, np.pi + 1))
plt.ylim((-3, 3))
plt.xlabel('X')
plt.ylabel('Y')

plt.show()
```


![png](/img/output_55_0111.png)
    


等价与下面的方式


```python
plt.plot(x, y)
plt.plot(x, y * 2)

plt.axis([0, np.pi + 1,-3,3])
plt.xlabel('X')
plt.ylabel('Y')

plt.show()
```


![png](/img/output_57_0111.png)
    


此外，我们也可以通过 xticks 和 yticks 来设置轴的刻度


```python
plt.plot(x, y)
plt.plot(x, y * 2)
plt.xticks((0, np.pi * 0.5, np.pi, np.pi * 1.5, np.pi * 2))
plt.show()
```


![png](/img/output_59_0111.png)
    


<center><b>xticks yticks</b></center>

plt.xticks()/plt.yticks()设置轴记号,人为设置坐标轴的刻度显示的值

<b>例1：xticks的用法</b>
xticks()函数原型：

xticks(ticks, [labels], **kwargs)

参数说明：
ticks：数组类型，用于设置X轴刻度间隔
[labels]：数组类型，用于设置每个间隔的显示标签
**kwargs：用于设置标签字体倾斜度和颜色等外观属性。（注：python里的双星号代表这个位置接收任意多个关键字参数，可参考：python学习：python的星号（*）和双星号（**）用法）
不使用xticks时


```python
import numpy as np
import matplotlib.pyplot as plt
import calendar
x2 = range(1,13,1)
y2 = range(1,13,1)
plt.plot(x2,y2)
plt.show()
```


![png](/img/output_65_0111.png)
    


使用xticks后


```python
import numpy as np
import matplotlib.pyplot as plt
import calendar
x2 = range(1,13,1)
y2 = range(1,13,1)
plt.plot(x2,y2)
plt.xticks(x2, calendar.month_name[1:13],color='blue',rotation=60) 
#参数x空值X轴的间隔，第二个参数控制每个间隔显示的文本，后面两个参数控制标签的颜色和旋转角度
plt.show()
```


![png](/img/output_67_0111.png)
    


例2参考：http://blog.sina.com.cn/s/blog_14478a3250102y73b.html

<b>（6）设置 label 和 legend</b>

设置 label 和 legend 的目的就是为了区分出每个数据对应的图形名称。


```python
plt.plot(x, y, label="sin(x)")
plt.plot(x, y * 2, label="2sin(x)")
# plt.legend()
plt.legend(loc='best')
plt.show()
```


![png](/img/output_71_0111.png)
 ![png](/img/loc.png)

<b>（7）添加注释</b>

有时候我们需要对特定的点进行标注，我们可以使用 plt.annotate 函数来实现。

这里我们要标注的点是 (x0, y0) = (π, 0)。

我们也可以使用 plt.text 函数来添加注释。


```python
plt.plot(x, y)

x0 = np.pi
y0 = 0

# 画出标注点
plt.scatter(x0, y0, s=50)

plt.annotate('sin(np.pi)=%s' % y0, xy=(np.pi, 0), xycoords='data', xytext=(+30, -30),
             textcoords='offset points', fontsize=16,
             arrowprops=dict(arrowstyle='->', connectionstyle="arc3,rad=.2"))

plt.text(0.5, -0.25, "sin(np.pi) = 0", fontdict={'size': 16, 'color': 'r'})

plt.show()
```


![png](/img/output_75_0111.png)
    


对于 annotate 函数的参数，做一个简单解释：<br/>
• 
'sin(np.pi)=%s' % y0 代表标注的内容，可以通过字符串 %s 将 y0 的值传入字符串；

• 
参数 xycoords='data' 是说基于数据的值来选位置;

• 
xytext=(+30, -30) 和 textcoords='offset points' 表示对于标注位置的描述 和 xy 偏差值，即标注位置是 xy 位置向右移动 30，向下移动30；

• 
arrowprops 是对图中箭头类型和箭头弧度的设置，需要用 dict 形式传入。



<b>（8）使用子图</b>

有时候我们需要将多张子图展示在一起，可以使用 subplot() 实现。即在调用 plot() 函数之前需要先调用 subplot() 函数。该函数的第一个参数代表子图的总行数，第二个参数代表子图的总列数，第三个参数代表活跃区域。


```python
ax1 = plt.subplot(2, 2, 1) # （行，列，活跃区）
plt.plot(x, np.sin(x), 'r')

ax2 = plt.subplot(2, 2, 2, sharey=ax1) # 与 ax1 共享y轴
plt.plot(x, 2 * np.sin(x), 'g')

ax3 = plt.subplot(2, 2, 3)
plt.plot(x, np.cos(x), 'b')

ax4 = plt.subplot(2, 2, 4, sharey=ax3) # 与 ax3 共享y轴
plt.plot(x, 2 * np.cos(x), 'y')

plt.show()
```


![png](/img/output_79_0111.png)
    


上面的 subplot(2, 2, x) 表示将图像窗口分为 2 行 2 列。x 表示当前子图所在的活跃区。

可以看到，上面的每个子图的大小都是一样的。有时候我们需要不同大小的子图。比如将上面第一张子图完全放置在第一行，其他的子图都放在第二行。


```python
ax1 = plt.subplot(2, 1, 1) # （行，列，活跃区）
plt.plot(x, np.sin(x), 'r')

ax2 = plt.subplot(2, 3, 4)
plt.plot(x, 2 * np.sin(x), 'g')

ax3 = plt.subplot(2, 3, 5, sharey=ax2)
plt.plot(x, np.cos(x), 'b')

ax4 = plt.subplot(2, 3, 6, sharey=ax2)
plt.plot(x, 2 * np.cos(x), 'y')

plt.show()
```


![png](/img/output_81_0111.png)
    


plt.subplot(2, 1, 1) 将图像窗口分为了 2 行 1 列, 当前活跃区为 1。

使用 plt.subplot(2, 3, 4) 将整个图像窗口分为 2 行 3 列, 当前活跃区为 4。

解释下为什么活跃区为 4，因为上一步中使用 plt.subplot(2, 1, 1) 将整个图像窗口分为 2 行 1 列, 第1个小图占用了第1个位置, 也就是整个第1行. 这一步中使用 plt.subplot(2, 3, 4) 将整个图像窗口分为 2 行 3 列, 于是整个图像窗口的第1行就变成了3列, 也就是成了3个位置, 于是第2行的第1个位置是整个图像窗口的第4个位置。


<b>（9）中文乱码解决</b>

Matplotlib 有个让人恼火的问题是，默认情况下，Matplotlib 中文会乱码。


```python
x = ['北京', '上海', '深圳', '广州']
y = [60000, 58000, 50000, 52000]
plt.plot(x, y)
plt.show()
```

    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:214: RuntimeWarning: Glyph 21271 missing from current font.
      font.set_text(s, 0.0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:214: RuntimeWarning: Glyph 20140 missing from current font.
      font.set_text(s, 0.0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:214: RuntimeWarning: Glyph 19978 missing from current font.
      font.set_text(s, 0.0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:214: RuntimeWarning: Glyph 28023 missing from current font.
      font.set_text(s, 0.0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:214: RuntimeWarning: Glyph 28145 missing from current font.
      font.set_text(s, 0.0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:214: RuntimeWarning: Glyph 22323 missing from current font.
      font.set_text(s, 0.0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:214: RuntimeWarning: Glyph 24191 missing from current font.
      font.set_text(s, 0.0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:214: RuntimeWarning: Glyph 24030 missing from current font.
      font.set_text(s, 0.0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:183: RuntimeWarning: Glyph 21271 missing from current font.
      font.set_text(s, 0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:183: RuntimeWarning: Glyph 20140 missing from current font.
      font.set_text(s, 0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:183: RuntimeWarning: Glyph 19978 missing from current font.
      font.set_text(s, 0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:183: RuntimeWarning: Glyph 28023 missing from current font.
      font.set_text(s, 0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:183: RuntimeWarning: Glyph 28145 missing from current font.
      font.set_text(s, 0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:183: RuntimeWarning: Glyph 22323 missing from current font.
      font.set_text(s, 0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:183: RuntimeWarning: Glyph 24191 missing from current font.
      font.set_text(s, 0, flags=flags)
    C:\Users\shili\anaconda3\lib\site-packages\matplotlib\backends\backend_agg.py:183: RuntimeWarning: Glyph 24030 missing from current font.
      font.set_text(s, 0, flags=flags)




![png](/img/output_85_1111.png)
    


可以看到，上面所有的中文都乱码了，显示成方框了，如何解决呢？<br/>
只需要配置下后台字体即可


```python
plt.rcParams['font.sans-serif']=['SimHei'] #用来正常显示中文标签
plt.rcParams['axes.unicode_minus']=False #用来正常显示负号

plt.plot(x, y)
plt.show()
```


![png](/img/output_87_0111.png)
    


<h3>3.5数据读取与导入</h3>

http://cc.nohup.cc/article/207/#menu_index_1以折线图为例讲解

# 总结

本节课我们了解了Python的画图包，matplotlib的基本用法及数据读取的方法。<br/>
请同学们课下结合实例进行练习

# 附:MarkDown基本使用

1）空格 
&nbsp;半角的不换行的空格（推荐使用）
&emsp;全角空格
&ensp;半角空格
2）删除：连按两次D即可删除当前行

3)换行：使用&lt;br\\&gt;实现

4)显示行号：在命令模式中的代码单元格按 L 打开数字。

5)颜色的使用 font

6) Markdown表格

| 表头   | 表头   |
| ------ | ------ |
| 单元格 | 单元格 |
| 单元格 | 单元格 |

表格效果如下：

| 表头   | 表头   |
| ------ | ------ |
| 单元格 | 单元格 |
| 单元格 | 单元格 |



<h3>其他</h3>

1)生新启动内核

2）生成html格式


```python
!jupyter nbconvert --to html Matplotlib.ipynb
```

    [NbConvertApp] Converting notebook Matplotlib.ipynb to html
    [NbConvertApp] Writing 642412 bytes to Matplotlib.html



```python

```