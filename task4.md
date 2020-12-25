:frowning_face:我在typora排的美美的版，到微信上就是:shit::shit:，无力吐槽啊~~

so ,i am here!

---



### <font color=darkcyan>Task4  分组</font>

#### <font color=olivedrab>一.概念</font>

1. **分组元素**

```python
df.groupby("Gender")["learn_pandas.csv"].sum()
#分组变量   数据   统计方法
#类似于sas中`class`分类变量
```

2. **多维分组**

```python
df.groupby(["Gender","name"])["learn_pandas.csv"].sum()

condition=df.weight>40
df.groupby(condition)["learn_pandas.csv"].sum()
```

3. **分组情况描述**

- 分组种类、数量、大小

```python
df.groupby("Gender")["Height"].ngroups
df.groupby("Gender")["Height"].groups#返回值为字典
df.groupby("Gender")["Height"].groups.values()#返回字典值
df.groupby("Gender")["Height"].size()
df.groupby("Gender")["Height"].get_group("a")#返回a组
#包括计数、均值、标准差、最大/小值、四分位数、
df.groupby([df["Gender"],df["School"]])["Height","Weight"].describe().head
```



#### <font color=olivedrab>二：分组相关方法</font>

**所有这些分组方法都是在`groupby`对象上完成的**

<img src="C:\Users\HuaWei\AppData\Roaming\Typora\typora-user-images\image-20201224115742707.png" alt="image-20201224115742707" style="zoom:50%;" />

##### 1.内置函数

- 聚合

关于聚合的函数有：

计数、最大/小值、均数、求和、标准差、标准误、四分位数、偏度系数、prod、any、all

```python
import pandas as pd
import numpy as np
df=pd.read_csv("learn_pandas.csv")

#两者效果一样，但会出现以下提示
df.groupby(["Gender","School"])["Height","Weight"].median()
df.groupby([df["Gender"],df["School"]])["Height","Weight"].median()
#/opt/conda/lib/python3.6/site-packages/ipykernel_launcher.py:1: FutureWarning: Indexing with multiple keys (implicitly converted to a tuple of keys) will be deprecated, use a list instead.
  """Entry point for launching an IPython kernel.
```

- 变换

关于变换的函数有：

累积求和、累积求乘，累积计数，累积最大值、累积最小值、排秩

```python
df.groupby([df["Gender"],df["School"]])["Height","Weight"].cummax()
df.groupby([df["Gender"],df["School"]])["Height","Weight"].rank()
```

##### 2.方法

- 聚合---**agg**方法
  - 对多列用多个（匿名）函数
  - 对指定列用指定（匿名）函数
  - :rotating_light: 不能跨列计算
  - :rotating_light: 得到的是——，和元数据shape不一样

```python
df1=df.groupby([df["Gender"],df["School"]])["Height","Weight"]

#指定列用指定函数，字典大法好
df1.agg({"Weight":["sum","max"],"Height":"min"})

#多列用多个函数，
df1.agg(["sum","max","min"])

#多列用多个函数，同时更改索引名
df1.agg({"Height":[("nsum","sum"),("idm","idxmax"),"skew"],"Weight":[("nsum","sum"),"idxmax","skew"]})

#还可使用匿名函数，匿名函数必须是能reduce的函数
df1.agg(lambda x:----)
```

> 练一练：
>
> ```python
> #怎么表示25%位数
> df.groupby([df["Gender"],df["School"]])["Height","Weight"].agg(["mean","std","min","quantile",'max'])
> ```

> 练一练：
>
> ```python
> #有误
> def fun(c):
>  res="low"
>  if c.Weight>c.Weight.quantile(0.75):
>      res="high"
>  elif c.Weight.between(quantile(0.25).quantile(0.75)):
>      res="normal"
>  return res
> df.groupby(fun(df))["Height"].mean()
> #纠正后
> con1=df.Weight<df.Weight.quantile(0.25)
> con2=df.Weight>df.Weight.quantile(0.75)
> con3=df.Weight.between(df.Weight.quantile(0.25),df.Weight.quantile(0.75))
> df1=df.Weight.mask(con1,"low").mask(con2,"normal").mask(con3,"high")
> df.groupby(df1)["Weight"].mean()
> ```





- 变换---**transform**方法
  - :rotating_light:得到的是和元数据shape一样的——（序列？数据框？）
  - 逐列进行运算，不会跨列（鸡肋哦，一般运算都会用到不同变量）

```python
df1.transform(lambda x:x.mean())#返回标量
df1.transform(lambda x:(x-x.mean()).abs())#返回Dataframe

```

> 练一练
>
> 对于 transform 方法无法像 agg 一样，通过传入字典来对指定列使用特定的变换，如果需要在一次 transform 的调用中实现这种功能，请给出解决方案。
>
> ```python
> 
> ```





- 过滤---**filter**方法
  - 对subframe也就是每组进行筛选
  - 索引是以列为单位进行筛选

```python
df.loc[df.Weight>50]
#df.groupby([df["Gender"],df["School"]] )["Height"].filter(lambda x:x.shape(0)>100)
```





- 跨组计算---**apply**方法

  - 自定义函数

  ```python
  def cu(a):
      
  gd.apply(cu)
  ```

  - 返回标量

  ```python
   gb = df.groupby(['Gender','Test_Number'])[['Height','Weight']]
  
   gb.apply(lambda x: 0)
  ```

  

  - 返回Series

  ```py
  gb.apply(lambda x: pd.Series([0,0],index=['a','b']))
  ```

  - 返回Dataframe

  ```python
  gb.apply(lambda x: pd.DataFrame(np.ones((2,2)),index = ['a','b'],columns=pd.Index([('w','x'),('y','z')])))
  ```

  



#### <font color=olivedrab>三：习题</font>

1. **练一练**

现有一份汽车数据集，其中 `Brand, Disp., HP` 分别代表汽车品牌、发动机蓄量、发动机输出。

```
In [45]: df = pd.read_csv('data/car.csv')

In [46]: df.head(3)
Out[46]: 
             Brand  Price Country  Reliability  Mileage   Type  Weight  Disp.   HP
0   Eagle Summit 4   8895     USA          4.0       33  Small    2560     97  113
1  Ford Escort   4   7402     USA          2.0       33  Small    2345    114   90
2   Ford Festiva 4   6319   Korea          4.0       37  Small    1845     81   63
```

1. 过滤出所属 `Country` 数超过2个的汽车，即若该汽车的 `Country` 在总体数据集中出现次数不超过2则剔除

```python
df1=df.groupby("Country")["Brand","Country","Reliability","Mileage","Type","Weight","Disp.","HP"]
df2=df1.filter(lambda x:x.query(Country)="Japan/USA")
df2
```

2. 按 `Country` 分组计算价格均值、价格变异系数、该 `Country` 的汽车数量，其中变异系数的计算方法是标准差除以均值，并在结果中把变异系数重命名为 `CoV` 。

```python
df3=df2.groupby("Country")["Price"]

df.groupby('Country').filter(lambda x:x.shape[0]>2).groupby('Country')['Price'].agg("mean",("Cov",lambda x:x.mean()/x.std()),"count")
```



1. 按照表中位置的前三分之一、中间三分之一和后三分之一分组，统计 `Price` 的均值。

```python
def fu(c):
    if c<c.quantile(0.333):
        res=0
    elif c.between(c.quantile(0.333),c.quantile(0.67)):
        res=1
    elif c>c.quantile(0.678):
        res=2
    return res
df.groupby("Price")
#按照表中相对位置
#不知道怎么搞位置
condition = ['Head']*20+['Mid']*20+['Tail']*20
df.groupby(condition)['Price'].mean()
```



1. 对类型 `Type` 分组，对 `Price` 和 `HP` 分别计算最大值和最小值，结果会产生多级索引，请用下划线把多级列索引合并为单层索引。

```python
df.groupby("Type")["Price","HP"].agg({"Price":"max","HP":"min"}).MultiIndex.map(lambda x: x[0]+"-"+x[1])
```



1. 对类型 `Type` 分组，对 `HP` 进行组内的 `min-max` 归一化。

```python
df1=df.groupby("Type")["HP"]
#还需要想想
```



1. 对类型 `Type` 分组，计算 `Disp.` 与 `HP` 的相关系数。

```python
df1=df.groupby("Type")[["Disp.","HP"]]    
df1.apply(lambda x:np.corrcoef(x["HP"].values,x["Disp."].values)[0,1])
```



**2.练一练**

* `groupby` 对象的构造方法是 `my_groupby(df, group_cols)`
* 支持单列分组与多列分组
* 支持带有标量广播的 `my_groupby(df)[col].transform(my_func)` 功能
* `pandas` 的 `transform` 不能跨列计算，请支持此功能，即仍返回 `Series` 但 `col` 参数为多列
* 无需考虑性能与异常处理，只需实现上述功能，在给出测试样例的同时与 `pandas` 中的 `transform` 对比结果是否一致