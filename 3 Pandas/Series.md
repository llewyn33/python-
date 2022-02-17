# Series

是一种一维的数组型对象，包含一个值序列，并包含了数据标签(索引index)

```python
obj = pd.Series([4,7,-5,3])
value = obj.values # 获取值
index = obj.index # 获取索引

# 设置索引
obj = pd.Series([4, 7, -5, 3], index=['d', 'b', 'a', 'c'])
val = obj[['a', 'b', 'c']]
print(val)
# 筛选过滤
obj_new = obj[obj >0] 
print(obj_new)
```

可以将series看做一个长度固定且有序的字典

```python
sdata = {
    'Ohio': 35000,
    'Texas': 71000,
    'Oregon': 16000,
    'Utah': 5000
}

obj = pd.Series(sdata)
print(obj)

# 设置索引顺序
states = ['California', 'Ohio', 'Oregon', 'Texas']
obj = pd.Series(sdata, index=states)
print(obj)
print(pd.isnull(obj)) # 检查缺失值 == obj.isnull()
print(pd.notnull(obj)) # 检查缺失值 ==obj.notnull()

# Series对象自身和索引都有name属性
obj.name = 'population'
obj.index.name = 'state'
print(obj)

# 索引可以通过位置赋值进行修改
obj = pd.Series([4, 7, -5, 3])
print(obj)
obj.index = ['a', 'b', 'c', 'd']
print(obj)
```