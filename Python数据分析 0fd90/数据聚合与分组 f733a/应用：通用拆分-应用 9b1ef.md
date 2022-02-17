# 应用：通用拆分-应用-联合

apply

```python
import pandas as pd

tips = pd.DataFrame({
    'total_bill': [16.99, 10.34, 21.01, 23.68, 24.59, 25.29],
    'tip': [1.01, 1.66, 3.50, 3.31, 3.61, 4.71],
    'smoker': ['no', 'yes', 'yes', 'yes', 'no', 'no'],
    'day': ['sun', 'mon', 'sun', 'tue', 'sun', 'sat'],
    'time': ['dinner', 'dinner', 'dinner', 'dinner', 'dinner', 'dinner'],
    'size': [2, 3, 3, 2, 4, 4]
    # 'tip_pct': [0.059447, 0.160542, 0.166587, 0.139780, 0.146808, 0.186240]
})
tips['tip_pct'] = tips['tip'] / tips['total_bill']
print(tips)

def top(df, n=5, column='tip_pct'):
    return df.sort_values(by=column, ascending=False)[:n]
res = top(tips)
res_group = tips.groupby(['smoker', 'day']).apply(top, n=3, column='total_bill') # 分组后调用函数
print(res_group)

# 压缩分组键 返回结果取消索引 group_keys=False
res_group = tips.groupby(['smoker', 'day'], group_keys=False).apply(top, n=3, column='total_bill')
```

分位数与桶分析

```python
# 等长桶

frame = pd.DataFrame({
    'data1': np.random.randn(1000),
    'data2': np.random.randn(1000)
})

***quartiles = pd.cut(frame.data1, 4)***

def get_stats(group):
    return {
        'min': group.min(),
        'max': group.max(),
        'count': group.count(),
        'mean': group.mean()
    }

grouped = frame.data2.groupby(quartiles)

group_new = grouped.apply(get_stats).unstack()
print(group_new)

# 等大小桶
frame = pd.DataFrame({
    'data1': np.random.randn(1000),
    'data2': np.random.randn(1000)
})

***grouping = pd.qcut(frame.data1, 10, labels=False)***

def get_stats(group):
    return {
        'count': group.count(),
        'min': group.min(),
        'max': group.max(),
        'mean': group.mean()
    }

grouped = frame.data2.groupby(grouping)

group_new = grouped.apply(get_stats).unstack()
print(group_new)
```

使用指定分组值填充缺失值

```python
# 使用平均值填充NA
s = pd.Series(np.random.randn(6))
s[::2] = np.nan
print(s)
s = s.fillna(s.mean())
print(s)

# 使用分组平均值来填充NA
states = ['ohio', 'new york', 'vermont', 'florida', 'oregon', 'nevada', 'california', 'idaho']
group_key = ['east'] * 4 + ['west'] * 4
data = pd.Series(np.random.randn(8), index=states)
data[['vermont', 'nevada', 'idaho']] = np.nan
print(data)
grouped = data.groupby(group_key).mean()  # 计算分组平均值
print(grouped)

fill_mean = lambda x: x.fillna(x.mean()) # 自定义填充函数

data_new = data.groupby(group_key).apply(fill_mean)
print(data_new)

# 使用规定值填充NA
states = ['ohio', 'new york', 'vermont', 'florida', 'oregon', 'nevada', 'california', 'idaho']
group_key = ['east'] * 4 + ['west'] * 4
data = pd.Series(np.random.randn(8), index=states)
data[['vermont', 'nevada', 'idaho']] = np.nan
print(data)

fill_values = {
    'east': 0.5,
    'west': -1
} # 规定填充值

fill_func = lambda x: x.fillna(fill_values[x.name]) # 自定义填充函数（name为内置属性）

data_new = data.groupby(group_key).apply(fill_func)
print(data_new)
```

随机采样与排列

```python
suits = ['h', 's', 'c', 'd']
card_val = (list(range(1, 11)) + [10] * 3) * 4
base_names = ['A'] + list(range(2, 11)) + ['J', 'Q', 'K']
cards = []
for suit in ['h', 's', 'c', 'd']:
    cards.extend(str(num) + suit for num in base_names)

deck = pd.Series(card_val, index=cards) # 构造一幅扑克牌
print(deck)

def draw(deck, n=5):
    return deck.sample(n) # 使用Series.sample定义取样函数

res = draw(deck)
print(res)

# 每个花色随机抽取两张
suits = ['h', 's', 'c', 'd']
card_val = (list(range(1, 11)) + [10] * 3) * 4
base_names = ['A'] + list(range(2, 11)) + ['J', 'Q', 'K']
cards = []
for suit in ['h', 's', 'c', 'd']:
    cards.extend(str(num) + suit for num in base_names)

deck = pd.Series(card_val, index=cards)

def draw(deck, n=5):
    return deck.sample(n)

# res = draw(deck)
# print(res)

get_suit = lambda card: card[-1] # 获取花色

draw_suit1 = deck.groupby(get_suit).apply(draw, n=2)
draw_suit2 = deck.groupby(get_suit, group_keys=False).apply(draw, n=2) # 去除索引
print(draw_suit1)
print(draw_suit2)
```

分组加权平均和相关性

```python
# 计算分组加权平均
df = pd.DataFrame({
    'category': ['a', 'a', 'a', 'a', 'b', 'b', 'b', 'b'],
    'data': np.random.randn(8),
    'weights': np.random.rand(8)
})
print(df)
grouped = df.groupby('category')
get_wavg = lambda x: np.average(x['data'], weights=x['weights']) # 使用np.average构建加权平均函数z
res = grouped.apply(get_wavg)
print(res)

# 计算年度相关性
close_px = pd.read_csv('stock_px_2.csv', parse_dates=True, index_col=0)
print(close_px.info())

spx_corr = lambda x: x.corrwith(x['SPX'])  # df.corrwith 计算相关性

rets = close_px.pct_change().dropna()  # df.pct_change 计算与之前元素的相差百分比

get_year = lambda x: x.year
by_year = rets.groupby(get_year) # 获取年份并按年份分组

res = by_year.apply(spx_corr)
print(res)

res_a_m = by_year.apply(lambda x: x['AAPL'].corr(x['MSFT'])) # 计算苹果和微软的年度相关性
print(res_a_m)
```

逐组线性回归

```python
import statsmodels.api as sm # 计量经济学库

close_px = pd.read_csv('stock_px_2.csv', parse_dates=True, index_col=0)
print(close_px)

rets = close_px.pct_change().dropna()  # df.pct_change 计算与之前元素的相差百分比

get_year = lambda x: x.year
by_year = rets.groupby(get_year) # 获取年份并按年份分组

# 自定义回归函数，该函数对每个数据块执行普通最小二乘(OLS)回归
def regress(data, yvar, xvars):
    Y = data[yvar]
    X = data[xvars]
    X['intercept'] = 1.
    result = sm.OLS(Y, X).fit()
    return result.params

res = by_year.apply(regress, 'AAPL', ['SPX'])
print(res)
```

数据透视表

```python
# 计算航方向上按 day 和 smoker 排列的分组平均值
tips = pd.read_csv('tips.csv')
tips['tip_pct'] = tips['tip'] / tips['total_bill']
print(tips)
res = tips.pivot_table(index=['day', 'smoker'])
print(res)

# 在 tip_pct 和 size 上聚合，并根据time分组
res = tips.pivot_table(['tip_pct', 'size'], index=['time', 'day'], columns='smoker')
print(res)

#传递 margins=True 扩充，会添加ALL行和列标签，其中的值为单层中所有数据的分组统计值
res = tips.pivot_table(['tip_pct', 'size'],
                       index=['time', 'day'],
                       columns='smoker',
                       aggfunc='mean', # 使用聚合函数，传递给aggfunc 默认为mean
                       margins=True, # 传递 margins=True 扩充，会添加ALL行和列标签，其中的值为单层中所有数据的分组统计值
                       fill_value=0) # 某些情况下产生空值，用0填充空值
print(res)
```

交叉表

```python
data = {
    'sample': ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10'],
    'nationality': ['usa', 'japan', 'usa', 'japan', 'japan', 'japan', 'usa', 'usa', 'japan', 'japan'],
    'handedness': ['r', 'l', 'r', 'r', 'l', 'r', 'r', 'l', 'r', 'r']
}
frame = pd.DataFrame(data, columns=['sample', 'nationality', 'handedness'],
                     index=['0', '1', '2', '3', '4', '5', '6', '7', '8', '9'])
print(frame)
res = pd.crosstab(frame.nationality, frame.handedness, margins=True)
print(res)

tips = pd.read_csv('tips.csv')
tips['tip_pct'] = tips['tip'] / tips['total_bill']
print(tips)
res = pd.crosstab([tips.time, tips.day], tips.smoker, margins=True) # 前两个参数可以是数组、Series或数组的列表
print(res)
```