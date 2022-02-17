# 列表

列表

```python
# list函数常用于将迭代器或者生成器转化为列表
gen = range(10)
print(list(gen))

# 增加
a_list.append('str')
# 插入 在第二个位置插入str
a_list.insert(1,'str')
# insert计算代价高，如需在序列头尾插入元素，可使用collections.deque

# pop移除（与insert相反
a_list.pop(1) # 移除第2个位置的元素 默认删除最后一个数据
# remove移除
a_list.remove('str') # 移除 str 这个元素
# 根据下标删除
del a_list[1]
```

连接和联合列表

```python
# + 方法：
a_list + b_list
# extend方法
a_list.extend(b_list)
# 建议使用extend

everything = []
for chunk in a_list:
    everything.extend(chunk)
```

排序

```python
a.sort
```

二分搜索和已排序列表的维护

```python
import bisect

bisect.bisect(a_list,2) # 找到元素应当被插入的位置
bisect.insort(a_list,2) # 将元素插入到指定位置

# bisect不会检查列表是否已经排序，需在已排序列表中使用
```

切片

```python
切片：a_list[1,2]
赋值：a_list[3:4] = [6,3]
步进值：a_list[::2] # 每隔2个数取一个值
翻转：a_list[::-1]
```

enumerate

```python
a_list = ['foo', 'bar', 'baz']
mapping = {}
for i, v in enumerate(a_list):
    mapping[v] = i

print(mapping)
```

zip 可处理任意长度的序列，生成列表的长度由最短序列决定

```python
a_list = ['foo', 'bar', 'baz']
b_list = ['one', 'two', 'three']
zipped = zip(a_list,b_list)
print(list(zipped))
```

常用场景为同时遍历多个列表，有时会与enumerate连用

```python
seq1 = [1, 2, 3]
seq2 = [4, 5, 6]
for i, (a,b) in enumerate(zip(seq1,seq2)):
    print('{0},{1},{2}'.format(i,a,b))
```

拆分zip

```python
seq3 = zip(seq1,seq2)
seq3 = list(seq3)
first_num,second_num = zip(*seq3)
print(first_num)
print(second_num)
```