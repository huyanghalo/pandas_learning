### TASk5 变形

针对**某种特征的**存储方式进行变换，存储方式有：long、wide

两者区别在于（1）：输出布局（2）：数据框结构

long表，wide表类似于整合前后的表

1. **pivot函数**

index:变形后的行索引                值为unique

columns:需要转到列索引的列    值为unique

values：行和列索引对应的数值   

```python
dfdf.pivot(index="Name",columns="Subject",values="Grade")
# 把成绩作为列展示
```

要求：新表中行列索引对应了唯一值。

pandas支持多级索引，通过将三个参数设为列表。

2. **pivot_table**

解决pandas必须满足唯一条件的问题

例如：

```python
df pd.DataFrame({'Class':[1, 1, 2, 2, 1, 1, 2, 2],
                    'Name':['San Zhang', 'San Zhang', 'Si Li', 'Si Li',                          'San Zhang', 'San Zhang', 'Si Li', 'Si Li'],                 'Examination': ['Mid', 'Final', 'Mid', 'Final',                                  'Mid', 'Final', 'Mid', 'Final'],
                  'Subject':['Chinese', 'Chinese', 'Chinese', 'Chinese',
                               'Math', 'Math', 'Math', 'Math'],
                  'Grade':[80, 75, 85, 65, 90, 85, 92, 88],
                   'rank':[10, 15, 21, 15, 20, 7, 6, 2]})

df1=df.pivot(index="Name",columns="Subject",values="rank")
df1

#Index contains duplicate entries, cannot reshape
```

参数：aggfunc                         使用的聚合函数或自定义函数

​			margins               进行边际汇总

**解决办法：**

```python
df.pivot_table(index = 'Name',columns="Subject",values="Grade",aggfunc="mean")

#也可用匿名函数
df2=df.pivot_table(index = 'Name',columns="Subject",values="Grade",aggfunc=lambda x:x.mean())
```

> 练一练：
>
> 这种关系不一定成立，拿题中例子来说，Name变量中张三和李四数量相同，数量不同时，就不能简单计算平均值作为边际汇总,例如以下情况就不成立：
>
> ```python
> df.loc[0:5].pivot_table(index = 'Name',columns="Subject",values="Grade",aggfunc=lambda x:x.mean(),margins=True)
> ```

3. **melt 函数**

pivot函数的逆运算，也就是将宽表转为长表

<img src="C:\Users\HuaWei\AppData\Roaming\Typora\typora-user-images\image-20201227210336002.png" alt="image-20201227210336002" style="zoom:30%;" />

```python
df2.melt(id_vars=["Name"],value_vars=["Chinese","Math"],var_name="Subject",value_name="Grade")
```

???这样不太可

4. **wide_to_long 函数**

考虑交叉组合

```python
df = pd.DataFrame({'Class':[1,2],'Name':['San Zhang', 'Si Li'],'Chinese_Mid':[80, 75], 'Math_Mid':[90, 85], 'Chinese_Final':[80, 75], 'Math_Final':[90, 85]})
pd.wide_to_long(df,stubnames=['Chinese', 'Math'],i = ['Class', 'Name'],j='Examination',sep='_',suffix='.+')
```

![image-20201227211429374](C:\Users\HuaWei\AppData\Roaming\Typora\typora-user-images\image-20201227211429374.png)

### 索引的变形

1. **unstack函数**

把行索引改变为列索引,参数是移动的层号，默认转化最内层，移动到列索引的最内层，同时支持同时转化多个层

以下图数据集为例：

<img src="C:\Users\HuaWei\AppData\Roaming\Typora\typora-user-images\image-20201227212410077.png" alt="image-20201227212410077" style="zoom:33%;" />

```python
df.set_index(["Name","Examination"]).unstack()
```

2. **stack函数**

与Unstack函数作用相反，将列索引层压入行索引

3. **explode函数**

```python
df_ex = pd.DataFrame({'A': [[1, 2],'my_str',{1, 2},pd.Series([3, 4])], 'B': 1})
df_ex.explode('A')
```

`explode`函数相当于展开某一列所有的元素，尤其对于由List,tuple,dictionary,dataframe等组成的复杂数据

```python
 pd.get_dummies(df.Examination)
 pd.get_dummies(df.rank)
```

后者会报错！

```python
'DataFrame' objects are mutable, thus they cannot be hashed
```



今天时间有点紧，所以先简单总结下知识点，习题task6补。