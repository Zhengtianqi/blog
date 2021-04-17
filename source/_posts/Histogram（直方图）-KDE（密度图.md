title: Histogram（直方图）&KDE（密度图)
author: ztq
tags:

  - python
categories:
  - python基础
date: 2021-04-17 18:54:00

---

# <center>Histogram（直方图）&KDE（密度图)   </center>

<h2>0 Pandas数据集的导入</h2>

<b3><b>0.1 pandas导入csv/txt文件</b></b3>

pd.read_csv()

常用参数：
filepath_or_buffer：文件路径（必填，其他参数按需求填写）

sep：指定分隔符，默认逗号','。

header：指定第几行作为表头。默认为0（即第1行作为表头），若没有表头，需设置header=None，可以是int或list。

names：指定列的名称，用list表示，默认None。

index_col：指定行索引，可以是一列或多列，默认None。

usecols：需要读取的列，可以使用列序列也可以使用列名，默认None。

prefix：给列名添加前缀。如prefix=x,会出来X0,X1,....，默认None。

skiprows：需要忽略的行数（从文件开始处算起)，或需要跳过的行号列表（从0开始），默认None。

skipfooter：需要忽略的行数（从最后一行开始算)

nrows：需要读取的行数（从文件头开始算起），默认None。

encoding：编码方式，乱码时使用，默认None。

例1：导入文件house-price-index-full-table.csv中数据：


```python
import pandas as pd
df1=pd.read_csv("Data/house-price-index-full-table.csv")
#df1=pd.read_csv("Data/house-price2.csv",encoding='gbk')
df1.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }

</style>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Quarter</th>
      <th>Quarter Start Date</th>
      <th>House Price Index (non seasonally adjusted)</th>
      <th>House Price Index (seasonally adjusted)</th>
      <th>Average price 1-bedroom flats</th>
      <th>Average price 2-bedroom flats</th>
      <th>Average price 2-bedroom houses</th>
      <th>Average price 3-bedroom houses</th>
      <th>Average price 4-bedroom houses</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Q1 2002</td>
      <td>01/01/2002</td>
      <td>96.9</td>
      <td>99.4</td>
      <td>166</td>
      <td>213</td>
      <td>265</td>
      <td>332</td>
      <td>416</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Q2 2002</td>
      <td>01/04/2002</td>
      <td>98.7</td>
      <td>98.3</td>
      <td>160</td>
      <td>268</td>
      <td>268</td>
      <td>314</td>
      <td>432</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Q3 2002</td>
      <td>01/07/2002</td>
      <td>103.4</td>
      <td>101.7</td>
      <td>160</td>
      <td>259</td>
      <td>284</td>
      <td>332</td>
      <td>474</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Q4 2002</td>
      <td>01/10/2002</td>
      <td>101.0</td>
      <td>100.9</td>
      <td>137</td>
      <td>242</td>
      <td>300</td>
      <td>333</td>
      <td>459</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Q1 2003</td>
      <td>01/01/2003</td>
      <td>95.0</td>
      <td>97.2</td>
      <td>156</td>
      <td>216</td>
      <td>285</td>
      <td>328</td>
      <td>380</td>
    </tr>
  </tbody>
</table>

</div>




```python
df2=pd.read_csv("Data/house-price2.csv",usecols=[0,1,2],nrows=5,encoding='gbk')
df2
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }

</style>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Quarter</th>
      <th>Quarter Start Date</th>
      <th>House Price Index (non seasonally adjusted)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Q1 2002</td>
      <td>01/01/2002</td>
      <td>96.9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Q2 2002</td>
      <td>01/04/2002</td>
      <td>98.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Q3 2002</td>
      <td>01/07/2002</td>
      <td>103.4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Q4 2002</td>
      <td>01/10/2002</td>
      <td>101.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Q1 2003</td>
      <td>01/01/2003</td>
      <td>95.0</td>
    </tr>
  </tbody>
</table>

</div>



例2：导入文件data.txt中数据：


```python
df3=pd.read_csv("Data/data.txt",sep='\t',header=None,names=['特征1','特征2','特征3','标签'])
df3
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }

</style>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>特征1</th>
      <th>特征2</th>
      <th>特征3</th>
      <th>标签</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>40920</td>
      <td>8.326976</td>
      <td>0.953952</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>14488</td>
      <td>7.153469</td>
      <td>1.673904</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>26052</td>
      <td>1.441871</td>
      <td>0.805124</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75136</td>
      <td>13.147394</td>
      <td>0.428964</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>38344</td>
      <td>1.669788</td>
      <td>0.134296</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>72993</td>
      <td>10.141740</td>
      <td>1.032955</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>35948</td>
      <td>6.830792</td>
      <td>1.213192</td>
      <td>3</td>
    </tr>
    <tr>
      <th>7</th>
      <td>42666</td>
      <td>13.276369</td>
      <td>0.543880</td>
      <td>3</td>
    </tr>
    <tr>
      <th>8</th>
      <td>67497</td>
      <td>8.631577</td>
      <td>0.749278</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>35483</td>
      <td>12.273169</td>
      <td>1.508053</td>
      <td>3</td>
    </tr>
  </tbody>
</table>

</div>



<b3><b>0.2 pandas导入excel文件</b></b3>

pd.read_excel()

常用参数：
io：excel文件路径（必填，其他参数按需求填写）

sheet_name：需要导入数据的工作表表名,可以是int\string\list，None导入所有工作表数据，默认0。

参数header、names、index_col、usecols、skiprows、nrows、skip_footer、encoding的用法与pd.read_csv相同。比如读取数据时想把第一列设为index，那么只需要简单的
pd.read_csv("new_wordvecter.csv",index_col=[0])

这里index_col可以设为列名

后续更改index可以使用df.index = df.iloc[:,"column"].tolist()或df.set_index('column')
例：读取Canada.xlsx文件中的数据


```python
df_canada=pd.read_excel("Data/Canada.xlsx",sheet_name='Canada by Citizenship',skiprows=20,skip_footer=2)
df_canada.head()
df_India_China=df_canada[df_canada['OdName'].isin(['China','India'])]
```


```python
df_India_China.columns
```




    Index([    'Type', 'Coverage',   'OdName',     'AREA', 'AreaName',      'REG',
            'RegName',      'DEV',  'DevName',       1980,       1981,       1982,
                 1983,       1984,       1985,       1986,       1987,       1988,
                 1989,       1990,       1991,       1992,       1993,       1994,
                 1995,       1996,       1997,       1998,       1999,       2000,
                 2001,       2002,       2003,       2004,       2005,       2006,
                 2007,       2008,       2009,       2010,       2011,       2012,
                 2013],
          dtype='object')




```python
df_India_China
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }

</style>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Coverage</th>
      <th>OdName</th>
      <th>AREA</th>
      <th>AreaName</th>
      <th>REG</th>
      <th>RegName</th>
      <th>DEV</th>
      <th>DevName</th>
      <th>1980</th>
      <th>...</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>36</th>
      <td>Immigrants</td>
      <td>Foreigners</td>
      <td>China</td>
      <td>935</td>
      <td>Asia</td>
      <td>906</td>
      <td>Eastern Asia</td>
      <td>902</td>
      <td>Developing regions</td>
      <td>5123</td>
      <td>...</td>
      <td>36619</td>
      <td>42584</td>
      <td>33518</td>
      <td>27642</td>
      <td>30037</td>
      <td>29622</td>
      <td>30391</td>
      <td>28502</td>
      <td>33024</td>
      <td>34129</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Immigrants</td>
      <td>Foreigners</td>
      <td>India</td>
      <td>935</td>
      <td>Asia</td>
      <td>5501</td>
      <td>Southern Asia</td>
      <td>902</td>
      <td>Developing regions</td>
      <td>8880</td>
      <td>...</td>
      <td>28235</td>
      <td>36210</td>
      <td>33848</td>
      <td>28742</td>
      <td>28261</td>
      <td>29456</td>
      <td>34235</td>
      <td>27509</td>
      <td>30933</td>
      <td>33087</td>
    </tr>
  </tbody>
</table>
<p>2 rows × 43 columns</p>

</div>



选取等于某些值的行记录 用 ==

df.loc[df[‘column_name’] == some_value]

选取某列是否是某一类型的数值 用 isin

df.loc[df[‘column_name’].isin(some_values)]


df['a']#取a列

df[['a','b']]#取a、b列

<h2>1 直方图及密度图的画法</h2>

直方图是表示变量频率分布的一种方法，是一个可以快速展示数据概率分布的工具，直观易于理解，并深受数据爱好者的喜爱。大家平时可能见到最多就是 matplotlib，seaborn 等高级封装的库包。<br/><br/>

直方图又称质量分布图，它是表示资料变化情况的一种主要工具。用直方图可以解析出资料的规则性，比较直观地看出产品质量特性的分布状态，对于资料分布状况一目了然，便于判断其总体质量分布情况。直方图表示通过沿数据范围形成分箱(bin)，然后绘制条以显示落入每个分箱的观测次数的数据分布。

核密度图（kde直方图的拟合曲线）可以看作是概率密度图

<b3><b>1.1 matplotlib画直方图及密度图</b></b3>

<b>(1)plt.hist()
用于画直方图。</b>

参数列表：
plt.hist(x, bins=None, range=None, density=None, weights=None, cumulative=False, bottom=None, histtype='bar', align='mid', orientation='vertical', rwidth=None, log=False, color=None, label=None, stacked=False, normed=None)

x：指定要绘制直方图的数据；输入值，这需要一个数组或者一个序列，不需要长度相同的数组。
bins：指定直方图条形的个数；
range：指定直方图数据的上下界，默认包含绘图数据的最大值和最小值；
density：布尔,可选。如果"True"，返回元组的第一个元素将会将计数标准化以形成一个概率密度，也就是说，直方图下的面积（或积分）总和为1。这是通过将计数除以数字的数量来实现的观察乘以箱子的宽度而不是除以总数数量的观察。如果叠加也是“真实”的，那么柱状图被规范化为1。(替代normed)
weights：该参数可为每一个数据点设置权重；
cumulative：是否需要计算累计频数或频率；
bottom：可以为直方图的每个条形添加基准线，默认为0；
histtype：指定直方图的类型，默认为bar，除此还有’barstacked’, ‘step’, ‘stepfilled’；
align：设置条形边界值的对其方式，默认为mid，除此还有’left’和’right’；
orientation：设置直方图的摆放方向，默认为垂直方向；
rwidth：设置直方图条形宽度的百分比；
log：是否需要对绘图数据进行log变换；
color：设置直方图的填充色；
label：设置直方图的标签，可通过legend展示其图例；
stacked：当有多个数据时，是否需要将直方图呈堆叠摆放，默认水平摆放；
normed：是否将直方图的频数转换成频率；(弃用，被density替代)
alpha：透明度，浮点数。
返回值：
n：直方图的值
bins：返回各个bin的区间范围
patches：返回每个bin的信息

```python
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(19260801)

mu1, sigma1 = 100, 15
mu2, sigma2 = 80, 15
x1 =  np.random.normal(mu1,sigma1,10000) # (均值,方差/标准差,个数)
x2 =  np.random.normal(mu2,sigma2,10000) 

# the histogram of the data
# 50：将数据分成50组
# color：颜色；alpha：透明度
# density：是密度而不是具体数值，是否对数据进行归一化
n1, bins1, patches1 = plt.hist(x1, bins=50, density=True,color='g', alpha=1)
n2, bins2, patches2 = plt.hist(x2, bins=50, density=True,color='r', alpha=0.2)

plt.plot(bins1[:-1],n1,'--')
plt.plot(bins2[:-1],n2,'--')
# plt.show()
```




    [<matplotlib.lines.Line2D at 0x21b1a893250>]




![png](/img/output_30_12.png)
    


<b>(2)添加分布曲线</b>


```python
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(19260801)

mu1, sigma1 = 100, 15
mu2, sigma2 = 80, 15
x1 =  np.random.normal(mu1,sigma1,10000) # (均值,标准差,个数)
x2 =  np.random.normal(mu2,sigma2,10000) 

# the histogram of the data
# 50：将数据分成50组
# color：颜色；alpha：透明度
# density：是密度而不是具体数值
n1, bins1, patches1 = plt.hist(x1, bins=50,  density=True, color='g', alpha=1)
n2, bins2, patches2 = plt.hist(x2, bins=50,  density=True, color='r', alpha=0.2)


plt.plot(bins1[:-1],n1,'--')
plt.plot(bins2[:-1],n2,'--')
plt.show()
```


![png](/img/output_32_02.png)
    

通俗的说，返回的第一个值是每个直方柱的y值（取决于normed和weights的取值），
而第二个值是每个直方柱的x值——如果分成10份，则有11个分割位置，因此返回数目为nbins+1。
<b>(3)多类型直方图</b>


```python
import numpy as np
import matplotlib.pyplot as plt

np.warnings.filterwarnings('ignore', category=np.VisibleDeprecationWarning)      

# 生成3组值，每组的个数可以不一样
x1,x2,x3 = [np.random.randn(n) for n in [10000, 5000, 2000]]

plt.figure(figsize=(8,5))
# 在 ax.hist 函数中先指定图例 label 名称
plt.hist([x1, x2, x3], bins=10, density=True, histtype='bar')
#plt.rcParams['font.family']='Times New Roman'
#plt.rcParams['axes.unicode_minus']=False

# 通过 ax.legend 函数来添加图例
plt.legend(list("ABC"))
plt.show()
```


![png](/img/output_35_02.png)
    


<b>(4)添加说明信息</b>

<b>添加title</b><br/><br/>
你可以给图示或图添加标题。但是默认不支持中文，需要自己添加中文字体。

windows的字体文件放在 C:\Windows\Fonts，通过fontproperties设置字体，fontsize设置字体大小.


```python
import numpy as np
import matplotlib.pyplot as plt

np.warnings.filterwarnings('ignore', category=np.VisibleDeprecationWarning)      

# 生成3组值，每组的个数可以不一样
x1,x2,x3 = [np.random.randn(n) for n in [10000, 5000, 2000]]

plt.figure(figsize=(8,5))
# 在 ax.hist 函数中先指定图例 label 名称
plt.hist([x1, x2, x3], bins=10, density=True, histtype='bar')

# 通过 ax.legend 函数来添加图例
plt.legend(list("ABC"))

plt.rcParams['font.family']='SimHei'
plt.rcParams['axes.unicode_minus']=False
plt.rcParams['font.size']='12'
plt.title("多类型直方图")
#songTi = matplotlib.font_manager.FontProperties(fname='C:\\Windows\\Fonts\\msyh.ttc')
#plt.title("多类型直方图",fontproperties=songTi,fontsize=12)
plt.show()
```


![png](/img/output_38_02.png)
    


<b>添加文字、网格、轴标签及轴范围</b>


```python
import numpy as np
import matplotlib.pyplot as plt

# Fixing random state for reproducibility
np.random.seed(19680801)

mu, sigma = 100, 15
x = mu + sigma * np.random.randn(10000)

# the histogram of the data
n, bins, patches = plt.hist(x, 50, density=True, color='g', alpha=0.75)


plt.xlabel('Smarts')
plt.ylabel('Probability')
plt.title('Histogram of IQ')
plt.text(60, .025, r'$\mu=100,\ \sigma=15$')  #(x,y,str,...)r raw string 
plt.xlim(40, 160)
plt.ylim(0, 0.03)
plt.grid(True)
plt.show()
```


![png](/img/output_40_02.png)
    


<b3><b>1.2 pandas画直方图及密度图</b></b3>

<b>（1）pandas的plot方法</b>

pandas默认的 plot 方法可以帮助我们快速地绘制各种图形<br/>
Pandas 提供了 plot() 方法可以快速方便地将 Series 和 DataFrame 中的数据进行可视化, 它是 matplotlib.axes.Axes.plot 的封装。代码执行后会生成一张图片，并直接显示在 notebook 上。<br/>

<b>基本使用</b>
plot 默认为折线图，折线图也是最常用和最基础的可视化图形，足以满足我们日常 80% 的需求：

df.plot()
s.plot()


我们可以在 plot 后增加调用来使用其他的图形，当然这些图形对数据结构也有自己的要求：

df.plot.line() # 折线的全写方式
df.plot.bar() # 柱状图
df.plot.barh() # 横向柱状图 （条形图）
df.plot.hist() # 直方图
df.plot.box() # 箱形图
df.plot.kde() # 核密度估计图
df.plot.density() # 同 df.plot.kde()
df.plot.area() # 面积图
df.plot.pie() # 饼图
df.plot.scatter() # 散点图
df.plot.hexbin() # 六边形箱体图，或简称六边形图
<b>使用方法</b>

<b>Series 使用</b>

使用 plot 时 x 轴为索引，y 轴为索引对应的具体值：


```python
import pandas as pd
import matplotlib
import matplotlib.pyplot as plt
ts = pd.Series(np.random.randn(20),
               index=pd.date_range('1/1/2000', periods=20)
              )

ts.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd7ad0d00>




![png](/img/output_49_12.png)
    


<b>DataFrame 使用</b><br/>

DataFrame 使用 plot 时 x 轴为索引，y 轴为索引对应的多个具体值：


```python
df = pd.DataFrame(np.random.randn(6, 4),
                  index=pd.date_range('1/1/2000', periods=6),
                  columns=list('ABCD'))
df.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd6a56610>




![png](/img/output_51_12.png)
    


<b>plot 绘图时常用的一些绘图参数。</b><br/>

图形类型
df.plot() 可以通过参数来指定具体图形类型：

df.plot(kind='pie') # 其他的名称和上文相同
s.plot(kind='pie')
直方图kind="hist"
密度图kind="kde"
生成或读取数据后，调用pandas的plot方法画图。默认情况下参数 kind="line" 表示图的类型为折线图。<br/>

```python
df = pd.DataFrame(np.random.randn(6, 4),
                  index=pd.date_range('1/1/2000', periods=6),
                  columns=list('ABCD'))
df.plot(kind='hist',title='this is a hist',grid=True)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x21b1ba68460>




![png](/img/output_55_12.png)
    


图的标题：


```python
df.plot(title='my plot')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd5f8df10>




![png](/img/output_57_12.png)
    


字体大小


```python
指定轴上的字体大小
```


```python
df.plot(fontsize=15)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd601e7c0>




![png](/img/output_60_12.png)
    


线条样式
style 可指定图的线条等样式，可参考可选的值 Matplotlib Line-style：
df[:5].plot(style=':') # 虚线
df[:5].plot(style='-.') # 虚实相间
df[:5].plot(style='--') # 长虚线
df[:5].plot(style='-') # 实线（默认）
df[:5].plot(style='.') # 点
df[:5].plot(style='*-') # 实线，数值为星星
df[:5].plot(style='^-') # 实线，数值为三角形

```python
df.plot(style=':') # 虚线
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd603bd30>




![png](/img/output_63_12.png)
    


对不同线分别给样式：


```python
df[:5].plot(style=[':', '--', '.-', '*-'])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd601a250>




![png](/img/output_65_12.png)
    


背景辅助线


```python
grid 会给 x 方向和 y 方向增加灰色辅助线：
```


```python
df.plot(grid=True)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd6162fd0>




![png](/img/output_68_12.png)
    


图例


```python
df.plot(legend=False)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd61e5cd0>




![png](/img/output_70_12.png)
    


可以反向排序图例：


```python
df.plot(legend='reverse')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x14fd5e02bb0>




![png](/img/output_72_12.png)
    


<b>（2）Pandas画直方图和密度图</b>


```python
import pandas as pd
import matplotlib.pyplot as plt
# 读入数据
Titanic = pd.read_csv('Data\Titanic.csv')
# 检查年龄是否有缺失
any(Titanic.Age.isnull())
# 不妨删除含有缺失年龄的观察
Titanic.dropna(subset=['Age'], inplace=True)
#设置绘图风格
plt.style.use('ggplot')
#处理中文乱码
plt.rcParams['font.sans-serif'] = ['Microsoft YaHei']
#坐标轴负号的处理
plt.rcParams['axes.unicode_minus']=False
# 绘制直方图
Titanic.Age.plot(kind = 'hist', bins = 20, color = 'steelblue', edgecolor = 'black', density = True, label = '直方图')
# 绘制核密度图
Titanic.Age.plot(kind = 'kde', color = 'red', label = '核密度图')
# 添加x轴和y轴标签
plt.xlabel('年龄')
plt.ylabel('频数')
# 添加标题
plt.title('泰坦尼克号乘客年龄分布')
# 显示图例
plt.legend()
# 显示图形
plt.show()
```

    <class 'pandas.core.series.Series'>




![png](/img/output_74_12.png)
    


<b3><b>1.3 seaborn画直方图及密度图</b></b3>

尽管上一幅图满足了两种图形的合成，但其表达的是所有乘客的年龄分布，如果按性别分组，研究不同性别下年龄分布的差异，该如何实现？针对这个问题，使用matplotlib模块或pandas模块都会稍微复杂一些，推荐使用seaborn模块中的distplot函数，因为该函数的代码简洁而易懂。关于该函数的语法和参数含义如下：
sns.distplot(a, bins=None, hist=True, kde=True, rug=False, fit=None,
	         hist_kws=None, kde_kws=None, rug_kws=None, fit_kws=None,
	         color=None, vertical=False, norm_hist=False, axlabel=None,
	         label=None, ax=None)a：指定绘图数据，可以是序列、一维数组或列表。
bins：指定直方图条形的个数。
hist：bool类型的参数，是否绘制直方图，默认为True。
kde：bool类型的参数，是否绘制核密度图，默认为True。
rug：bool类型的参数，是否绘制须图（如果数据比较密集，该参数比较有用），默认为False。
fit：指定一个随机分布对象（需调用scipy模块中的随机分布函数），用于绘制随机分布的概率密度曲线。
hist_kws：以字典形式传递直方图的其他修饰属性，如填充色、边框色、宽度等。
kde_kws：以字典形式传递核密度图的其他修饰属性，如线的颜色、线的类型等。
rug_kws：以字典形式传递须图的其他修饰属性，如线的颜色、线的宽度等。
fit_kws：以字典形式传递概率密度曲线的其他修饰属性，如线条颜色、形状、宽度等。
color：指定图形的颜色，除了随机分布曲线的颜色。
vertical：bool类型的参数，是否将图形垂直显示，默认为True。（改为False即为horizontal）
norm_hist：bool类型的参数，是否将频数更改为频率，默认为False。
axlabel：用于显示轴标签。 label：指定图形的图例，需结合plt.legend()一起使用。
ax：指定子图的位置。
从函数的参数可知，通过该函数，可以实现三种图形的合成，分别是直方图（hist参数）、核密度曲线（kde参数）以及指定的理论分布密度曲线（fit参数）。接下来，针对如上介绍的distplot函数，绘制不同性别下乘客的年龄分布图，具体代码如下：

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
# 读入数据
Titanic = pd.read_csv(r'Data\Titanic.csv')
# 检查年龄是否有缺失
any(Titanic.Age.isnull())
# 不妨删除含有缺失年龄的观察
Titanic.dropna(subset=['Age'], inplace=True)
#设置绘图风格
plt.style.use('ggplot')
#处理中文乱码
plt.rcParams['font.sans-serif'] = ['Microsoft YaHei']
#坐标轴负号的处理
plt.rcParams['axes.unicode_minus']=False
# 取出男性年龄
Age_Male = Titanic.Age[Titanic.Sex == 0]
# 取出女性年龄
Age_Female = Titanic.Age[Titanic.Sex ==1]

# seaborn模块绘制分组的直方图和核密度图
# 绘制男女乘客年龄的直方图
sns.distplot(Age_Male, bins = 20, kde = False, hist_kws = {'color':'steelblue'}, label = '男性')
# 绘制女性年龄的直方图
sns.distplot(Age_Female, bins = 20, kde = False, hist_kws = {'color':'purple'}, label = '女性')
plt.title('泰坦尼克号男女乘客的年龄直方图')
plt.xlabel('年龄')
plt.ylabel('频数')
# 显示图例
plt.legend()
# 显示图形
plt.show()

# 绘制男女乘客年龄的核密度图
sns.distplot(Age_Male, hist = False, kde_kws = {'color':'red', 'linestyle':'-'},
             norm_hist = True, label = '男性')
# 绘制女性年龄的核密度图
sns.distplot(Age_Female, hist = False, kde_kws = {'color':'black', 'linestyle':'--'},
             norm_hist = True, label = '女性')
plt.title('泰坦尼克号男女乘客的年龄核密度图')
plt.xlabel('年龄')
plt.ylabel('核密度值')
# 显示图例
plt.legend()
# 显示图形
plt.show()
```


![png](/img/output_79_02.png)
    




![png](/img/output_79_12.png)
    


为了避免四个图形混在一起不易发现数据背后的特征，将直方图与核密度图分开绘制。从直方图来看，女性年龄的分布明显比男性矮，说明在各年龄段下，男性乘客要比女性乘客多；再看核密度图，男女性别的年龄分布趋势比较接近，说明各年龄段下的男女乘客人数同步增加或减少。

把两种图合在一起画：


```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
# 读入数据
Titanic = pd.read_csv(r'Data\Titanic.csv')
# 检查年龄是否有缺失
any(Titanic.Age.isnull())
# 不妨删除含有缺失年龄的观察
Titanic.dropna(subset=['Age'], inplace=True)
#设置绘图风格
plt.style.use('ggplot')
#处理中文乱码
plt.rcParams['font.sans-serif'] = ['Microsoft YaHei']
#坐标轴负号的处理
plt.rcParams['axes.unicode_minus']=False
# 取出男性年龄
Age_Male = Titanic.Age[Titanic.Sex == 0]
# 取出女性年龄
Age_Female = Titanic.Age[Titanic.Sex ==1]

# seaborn模块绘制分组的直方图和核密度图
# 绘制男女乘客年龄的直方图
sns.distplot(Age_Male, bins = 20, kde = False, hist_kws = {'color':'steelblue'},
             label = ('男性','直方图'),norm_hist=True)
# 绘制女性年龄的直方图
sns.distplot(Age_Female, bins = 20, kde = False, hist_kws = {'color':'purple'},
             label = ('女性','直方图'),norm_hist=True)

# 绘制男女乘客年龄的核密度图
sns.distplot(Age_Male, hist = False, kde_kws = {'color':'red', 'linestyle':'-'},
             norm_hist = True, label = ('男性','核密度图'))
# 绘制女性年龄的核密度图
sns.distplot(Age_Female, hist = False, kde_kws = {'color':'black', 'linestyle':'--'},
             norm_hist = True, label = ('女性','核密度图'))
plt.title('泰坦尼克号男女乘客的年龄分布图')
plt.xlabel('年龄')
plt.ylabel('核密度值')
# 显示图例
plt.legend()
# 显示图形
plt.show()
```


![png](/img/output_81_02.png)
    


<b>（1）seaborn直方图</b>

seaborn的kdeplot函数专门用于画核密度估计图.

<b>（2）seaborn密度图</b>

seaborn的kdeplot函数专门用于画核密度估计图.

<h2>2 课堂练习</h2>

<b>（1）直方图</b>

以Titanic数据集为例绘制乘客的年龄直方图


```python
import pandas as pd
import matplotlib.pyplot as plt
# 读入数据
Titanic = pd.read_csv(r'Data\Titanic.csv')
# 检查年龄是否有缺失
any(Titanic.Age.isnull())
# 不妨删除含有缺失年龄的观察
Titanic.dropna(subset=['Age'], inplace=True)
#设置绘图风格
plt.style.use('ggplot')
#处理中文乱码
plt.rcParams['font.sans-serif'] = ['Microsoft YaHei']
#坐标轴负号的处理
plt.rcParams['axes.unicode_minus']=False
# 绘制直方图
plt.hist(x = Titanic.Age, # 指定绘图数据
         bins = 20, # 指定直方图中条块的个数
         color = 'steelblue', # 指定直方图的填充色
         edgecolor = 'black' # 指定直方图的边框色
         )
# 添加x轴和y轴标签
plt.xlabel('年龄')
plt.ylabel('频数')
# 添加标题
plt.title('泰坦尼克号乘客年龄分布')
# 显示图形
plt.show()
```


![png](/img/output_89_02.png)
    


<h2>3 直方图与条形图的区别</h2>

首先，条形图是用条形的长度表示各类别频数的多少，其宽度（表示类别）则是固定的；

      直方图是用面积表示各组频数的多少，矩形的高度表示每一组的频数或频率，宽度则表示各组的组距，因此其高度与宽度均有意义。

其次，由于分组数据具有连续性，直方图的各矩形通常是连续排列，而条形图则是分开排列。

最后，条形图主要用于展示分类数据，而直方图则主要用于展示数据型数据<br/><br/>

原文链接：https://blog.csdn.net/xjl271314/article/details/80295935

<h2>附录1-本小节用到的以前的知识复习</h2>
<h3>1)批量注释/去掉注释</h3> 

Ctrl+/

->常常出现在python函数定义的函数名后面，为函数添加元数据,描述函数的返回类型，从而方便开发人员使用。
def add(x, y) -> int:
  return x+y

<h3>2)中文字体设置</h3>

https://blog.csdn.net/dxawdc/article/details/110311549

<h3>3)函数定义</h3>

```python
s = '杰瑞你好啊'
def mylen():
    n = 0
    for i in s :
        n += 1
    print(n)
mylen()
#len1 = mylen()
#print(len1)
```

    5


<h2>附录2-错误提示及解决办法</h2>
<h3>1) 8722 missing from current font, matplotlib画图</h3>

负号问题，添加语句plt.rcParams['axes.unicode_minus']=False

更多请参考https://blog.csdn.net/seeker3/article/details/108432781?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.baidujs&dist_request_id=&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.baidujs


```python

```