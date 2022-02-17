# 通用函数

一元通用函数

```python
arr = np.arange(10)
arr_sqrt = np.sqrt(arr)
print(arr_sqrt)
arr_exp = np.exp(arr)
print(arr_exp)
```

二元通用函数

```python
# add maximun 
arr1 = np.random.randn(8)
arr2 = np.random.randn(8)
arr3 = np.maximum(arr1, arr2)
print(arr3)

# modf
arr = np.random.randn(7) * 5
a,b = np.modf(arr)
print(a) # 小数部分
print(b) # 整数部分
```