# 从Bitly获取USA.gov数据

```python
# 读取文件并转换为python数据
fp = open('example.txt')
records = [json.loads(line) for line in fp]
frame = pd.DataFrame(records)

# 处理 tz 列数据缺失值及空值并时区计数
clean_tz = frame['tz'].fillna('missing')
clean_tz[clean_tz == ''] = 'unknown'
tz_count = clean_tz.value_counts()
print(tz_count)

# 对于 a 列数据切片并计数
frame_a = frame['a']
res = pd.Series([x.split()[0] for x in frame_a.dropna()])
res_count = res.value_counts()
print(res_count)

# 识别使用系统并打标签
c_frame = frame[frame_a.notnull()] # 获取所有有效数据
c_frame = c_frame.copy()
c_frame['os'] = np.where(c_frame['a'].str.contains('Windows'), 'Windows', 'Others')
print(c_frame)
# 根据 tz os 分组计算
by_zt_os = c_frame.groupby(['tz', 'os'])
agg_counts = by_zt_os.size().unstack().fillna(0)
indexer = agg_counts.sum(1).argsort()  # 从小到大排序并返回index
count_subset = agg_counts.take(indexer[-10:])  # take选出行 indexer[-10:]为最后10个（最大的10个）的index
print(count_subset)

res = agg_counts.sum(1).nlargest(10) # 计数并选出最大的10个
print(res)
```