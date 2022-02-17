# 1.groupby（split-apply-combine)

分组使用聚合函数

```python
df = pd.DataFrame({
    'key1': ['a', 'a', 'b', 'b', 'a'],
    'key2': ['one', 'two', 'one', 'two', 'one'],
    'data1': np.random.randn(5),
    'data2': np.random.randn(5)
})
print(df)
avg = df['data1'].groupby([df['key1'], df['key2']]).mean()
print(avg)

states = np.array(['ohio', 'ohio', 'california', 'california', 'ohio'])
years = np.array([2005, 2006, 2005, 2006, 2005])
res = df['data1'].groupby([states,years]).mean() # 更换分组键
print(res)

# 默认情况下，所有数值列均可以聚合

# .size() 返回一个包含组大小的信息 
size_res = df.groupby(['key1', 'key2']).size()

# 分组键中的缺失值不在返回结果内
```

遍历各分组

```python
pieces = dict(list(df.groupby(['key1'])))
pieces_a = pieces['a']
print(pieces_a)

# goupby默认在行(axis=0)轴向上分组，可设置axis=1在列方向上分组
```

选择一列或所有列的子集

```python
pieces = dict(list(df.groupby(['key1'])))
pieces_a = pieces['a']['data1']
print(pieces_a.mean())

res = df.groupby(['key1','key2'])['data2'].mean()
```

使用字典和Series分组

```python
people = pd.DataFrame(np.random.randn(5, 5),
                      columns=['a', 'b', 'c', 'd', 'e'],
                      index=['joe', 'steve', 'wes', 'jim', 'travis'])
people.loc[['wes'], ['b', 'c']] = np.nan
print(people)
# dict
mapping = {
    'a': 'red',
    'b': 'red',
    'c': 'blue',
    'd': 'blue',
    'e': 'red',
    'f': 'orange'
}
by_column = people.groupby(mapping, axis=1)
res = by_column.sum()
print(res)
# Series
map_series = pd.Series(mapping)
res = people.groupby(map_series, axis=1).sum()
print(res)
```

使用函数分组

```python
key_list = ['one', 'one', 'one', 'two', 'two']
print(dict(list(people.groupby([len, key_list]))))
```

根据索引层级分组

```python
columns = pd.MultiIndex.from_arrays([['us', 'us', 'us', 'jp', 'jp'],
                                     [1, 3, 5, 1, 3]],
                                    names=['cty', 'tenor'])
hier_df = pd.DataFrame(np.random.randn(4, 5), columns=columns)
print(hier_df)
res = hier_df.groupby(level='cty', axis=1)
print(dict(list(res)))
num = hier_df.groupby(level='cty', axis=1).count()
print(num)

# 单独取某一组数据
res = dict(list(res))
res_jp = res['jp']
print(res_jp)
```