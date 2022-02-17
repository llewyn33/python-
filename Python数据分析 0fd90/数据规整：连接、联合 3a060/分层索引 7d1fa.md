# 分层索引

```python
data = pd.Series(np.random.randn(9),
                 index=[['a', 'a', 'a', 'b', 'b', 'c', 'c', 'd', 'd', ],
                        [1, 2, 3, 1, 3, 1, 2, 2, 3]])
print(data)
data = data.unstack() # 重排 .unstack()的反操作为.stack()
print(data)

frame = pd.DataFrame(np.arange(12).reshape((4, 3)),
                     index=[['a', 'a', 'b', 'b'], [1, 2, 1, 2]],
                     columns=[['ohio', 'ohio', 'colorado'],
                              ['green', 'red', 'green']])
frame.index.names = ['key1','key2']
frame.columns.names = ['state','color']
print(frame)
print(frame['ohio']['green']) # 选出所需列
```

重排序和分层排序

```python
# swaplevel 交换层级
frame_swaplevel = frame.swaplevel('key1','key2')
# 交换层级后按某一index排序
frame_sort = frame.swaplevel(0,1).sort_index(level='key2')
```

按层级进行汇总统计

```python
# 按索引汇总求和
frame_sumk2 = frame.sum(level = 'key2')
frame.sum(level='color',axis = 1)
```

使用dataframe的列进行索引

```python
frame = pd.DataFrame({
    'a': range(7),
    'b': range(7, 0, -1),
    'c': ['one', 'one', 'one', 'two', 'two', 'two', 'two'],
    'd': [0, 1, 2, 0, 1, 2, 3]
})
print(frame)
frame2 = frame.set_index('c', 'd') # 反操作为.reset_index()
```