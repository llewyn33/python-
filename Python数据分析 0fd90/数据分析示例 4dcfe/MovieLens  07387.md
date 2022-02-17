# MovieLens 1M数据集

```python
unames = ['user_id', 'gender', 'age', 'occupation', 'zip']
users = pd.read_table('users.dat', sep='::', header=None, names=unames, engine='python')

rnames = ['user_id', 'movie_id', 'rating', 'timestamp']
ratings = pd.read_table('ratings.dat', sep='::', header=None, names=rnames, engine='python')

mnames = ['movie_id', 'title', 'genres']
movies = pd.read_table('movies.dat', sep='::', header=None, names=mnames, engine='python')

data = pd.merge(pd.merge(ratings, users), movies) # 三表合并

# 分性别的平均电影评分
mean_rating = data.pivot_table('rating', index='title', columns='gender', aggfunc='mean')
ratings_by_title = data.groupby('title').size() # 按标题分组并统计评分数量
active_titles = ratings_by_title.index[ratings_by_title >= 250] # 筛选出评分数>=250的电影index
mean_ratings = mean_rating.loc[active_titles] # 选出所需行
top_female_ratings = mean_ratings.sort_values(by='F', ascending=False) # 女性观众评分由高到低
print(top_female_ratings)

# 测量评价分歧

mean_rating['diff'] = mean_rating['F'] - mean_rating['M'] # 计算评分均值差异值
res_fm = mean_rating.sort_values(by='diff',ascending=False) # 按差异值从大到小排序 正数为女性喜欢而男性不喜欢
res_mf = res_fm[::-1] # 按差异值从小到大排序 负数为男性喜欢而女性不喜欢
print(res_fm)
print(res_mf)

# 不依赖性别标识，在观众中异议最大的电影可通过评分的方差或标准差来衡量
rating_std_by_title = data.groupby('title')['rating'].std() # 计算评分标准差
rating_std_by_title = rating_std_by_title.loc[active_titles] # 选出有效行
rating_std_by_title = rating_std_by_title.sort_values(ascending=False) # 从大到小排序
print(rating_std_by_title)
```