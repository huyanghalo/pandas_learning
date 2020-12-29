### task6

#### 1. merge函数（数据框）与join函数（索引）

#### <img src="C:\Users\HuaWei\AppData\Roaming\Typora\typora-user-images\image-20201229232038107.png" style="zoom: 5%;" /><img src="C:\Users\HuaWei\AppData\Roaming\Typora\typora-user-images\image-20201229232207806.png" alt="image-20201229232207806" style="zoom:5%;" />

PS：为了证明并非复制粘贴，我把部分草稿给贴上来

```python
import pandas as pd
import numpy as np

df1=pd.DataFrame({"Name":["zhangsan","lisi"],"Age":[20,30]})
df2=pd.DataFrame({"Name":["lisi","wangwu"],"Gender":["F","M"]})
df1.merge(df2,on="Name",how="outer")

df3=pd.DataFrame({"Name":["zhangsan","zhangsan","lisi"],"Age":[20,30,50]})
df4=pd.DataFrame({"Name":["zhangsan","zhangsan","wangwu"],"Gender":["F","M","F"]})
df3.merge(df4,on="Name",how="left")

df1=pd.DataFrame({"Name":["zhangsan","lisi"],"Age":[20,30]})
df2=pd.DataFrame({"Name":["lisi","wangwu"],"Gender":["F","M"]})
df1.merge(df2,on="Name",how="left")

##列名不同，含义一样
df1=pd.DataFrame({"df_1Name":["zhangsan","lisi"],"Age":[20,30]})
df2=pd.DataFrame({"df_2Name":["lisi","wangwu"],"Gender":["F","M"]})
df1.merge(df2,left_on="df_1Name",right_on="df_2Name",how="left")

#相同列名，不同名字
df1=pd.DataFrame({"Name":["zhangsan"],"Age":70})
df2=pd.DataFrame({"Name":["zhangsan"],"Age":80})
df1.merge(df2,on="Name",how="left",suffixes=['_Chinese','_Math'])

#需要两个键的连接
df1=pd.DataFrame({"Name":["zhangsan","zhangsan"],"Age":[20,21],"class":["one","two"]})
df2=pd.DataFrame({"Name":["zhangsan","zhangsan"],"Gender":["F","M"],"class":["two","one"]})
df1.merge(df2,on=["Name","class"],how="left")

#索引连接

df1=pd.DataFrame({"Age":[20,30]},index=pd.Series(["zhangsan","lisi"],name="Name"))
df2=pd.DataFrame({"Gender":["F","M"]},index=pd.Series(["lisi","wangwu"],name="Name"))
df1.join(df2,on="Name",how="left")

#索引名一样但含义不一样
df1=pd.DataFrame({"Grade":70},index=pd.Series("zhangsan",name="Name"))
df2=pd.DataFrame({"Grade":80},index=pd.Series("zhangsan",name="Name"))
df1.join(df2,on="Name",how="left",lsuffix='_Chinese',rsuffix='_Math')

#多重索引
#多重索引不需要键吗？

df1=pd.DataFrame({"Age":[20,21]},index=pd.MultiIndex.from_arrays([["zhangsan","zhangsan"],["one","two"]],names=("Name","class")))
df2=pd.DataFrame({"Grade":["F","M"]},index=pd.MultiIndex.from_arrays([["zhangsan","zhangsan"],["two","one"]],names=("Name","class")))
df1.join(df2)
```

#### 2. concat函数(数据框与数据框之间)

函数有三个参数：axis、join、keys.

axis代表合并方向

join代表连接形式，有inner(取合并表的交集)、outer（取合并表的并集）

keys标记合并表中数据来自于哪个原表

```python
# 默认纵向连接
df1=pd.DataFrame({"Name":["zhangsan","lisi"],"Age":[20,30]})
df2=pd.DataFrame({"Name":["wangwu"],"Age":[40]})
pd.concat([df1,df2])
#横向连接
pd.concat([df1,df2],1)
#keys使用
pd.concat([df1,df2],keys=["a","b"])
```

 #### 3. assign 与 append函数(数据框与序列之间)

- append函数

```python
df1=pd.DataFrame({"Name":["zhangsan","lisi"],"Age":[20,30]})
s = pd.Series(['Wu Wang', 21], index = df1.columns)
df1.append(s,ignore_index=True)
#以df1的列名为索引
#下列这种方法合并
s = pd.Series(['Wu Wang', 21])
df1.append(s,ignore_index=True)

	Name	Age	0	1
0	zhangsan	20.0	NaN	NaN
1	lisi	30.0	NaN	NaN
2	NaN	NaN	Wu Wang	21.0
```

`ignore_index=True`表示忽略原序列的索引值

- assign函数

```python
s = pd.Series([80, 90])
df1.assign(Grade=s)
```

assign函数要为增加的序列添加变量名

#### 4.比较

```python
df1 = pd.DataFrame({'Name':['San Zhang', 'Si Li', 'Wu Wang']})
df2 = pd.DataFrame({'Name':['San Zhang', 'Li Si', 'Wu Wang']})
df1.compare(df2,keep_shape=True)

	Name
	self	other
0	NaN	NaN
1	Si Li	Li Si
2	NaN	NaN
```

`keep_shape=True`完整展示对比结果

但是以下不能比较

```python
df1 = pd.DataFrame({'Name':['San Zhang', 'Si Li', 'Wu Wang'],"Age":[20,30,40]})
df2 = pd.DataFrame({'Name':['San Zhang', 'Li Si'],"Age":[20,30]})
df1.compare(df2)
```

Q：比较限于数据结构一致的数据框？

#### 5.组合

```python
def choose_min(s1, s2):
    s2 = s2.reindex_like(s1)
    res = s1.where(s1<s2, s2)
    res = res.mask(s1.isna()) # isna表示是否为缺失值，返回布尔序列
    return res

df1 = pd.DataFrame({'A':[1,2], 'B':[3,4], 'C':[5,6]})
df2 = pd.DataFrame({'B':[5,6], 'C':[7,8], 'D':[9,10]}, index=[1,2])
df1.combine(df2, choose_min)
```

这个是复制粘贴的，还需要再看看