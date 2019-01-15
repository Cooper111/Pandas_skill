# 知识点
>data.iloc[train_indices]<br>

对于一个DataFrame A，A.loc[k]是读取A中index为k的那一行。A.iloc[k]是读取A中的第k行。


# 数据观测
<pre>

        #返回一个DataFrame对象
        housing  = pd.read_csv("housing.csv")
        #预览头n行
        housing.head(6)
        #查看此列的不同值统计
        housing["ocean_proximity"].value_counts()
        #查看表的列的总体情况count总数,mean均值,std(标准差),min，25%，50%，75%
        housing.describe()
        #数据展示，bins代表条数
        %matplotlib inline
        import matplotlib.pyplot as plt
        housing.hist(bins=50, figsize=(20,15))
        plt.show()
        #查看单列
        housing["median_income"].hist()

</pre>

# 选取数据集
##### loc与iloc函数
- loc函数

<pre>

    import pandas as pd
    import numpy
    # 导入数据
    df = pd.read_csv(filepath_or_buffer="D://movie.csv")
    #1：根据列中的元素，选取对应元素的数据集 
    df_new = df.set_index(["country"])
    #2：根据元素的选取条件来选取对应的数据集 
    df_new.loc[list(["Canada"])] # 1
    #3：根据元素的选取条件来来选取对应的数据集，并在符合条件的数据行添加flage标签
    df_new.loc[df_new["duration"]>160] # 2
    df_new.loc[((df_new["duration"] > 200) & (df_new["director_facebook_likes"] > 300 )),"flage"] =1 # 3
    #4：isin函数是series用来判断值是否在目标值是否在series 
    df_new.loc[df_new["duration"].isin([100])] # 4
    #5：query函数中用来判断条件符合的数据集并返回
    df_new.query("duration > 100 & index == 'UK'") # 5

</pre>

- iloc函数
>df_new.iloc[0:4]

>iloc比较简单，它是基于索引位来选取数据集，0:4就是选取 0，1，2，3这四行，需要注意的是这里是前闭后开集合


- loc实例<br>
  感jio要赋值了第二个参数就不加[],否则对DataFrame操作加[]

<pre>

    df.loc[peak_hours, 'cost_cents'] = df.loc[peak_hours, 'energy_kwh'] * 28
    
    train_test.loc[train_test['Name2_sum'] == 1 , 'Name2_new'] = 'one'
    
    train.loc[train['TotalBsmtSF']==0,'TotalBsmtSF'] = 1
    
    train.loc[train['TotalBsmtSF']==0,'TotalBsmtSF'] = 1
    
    print(train.loc[train['TotalBsmtSF']==0, ['TotalBsmtSF']].count())
    
    tmp = np.array(tmp.loc[tmp['TotalBsmtSF']>0, ['TotalBsmtSF']])[:, 0]

</pre>

- get_dummies
>pd.get_dummies(train_test,columns=["Sex"])
- isnull
>(DataFrame[DataFrame[..].isnull().values==False]
- mean
>train.groupby(['Pclass'])['Pclass','Survived'].mean()
- drop和del

<pre>

    (drop删除列，axis=1,inplace=True)
    ======================================================
    train = train.drop(train[(train['GrLivArea']>4000) & (train['SalePrice']<300000)].index)
    
    del train_test['Name2']
    
    missing_age_X_train = missing_age_train.drop(['Age'], axis=1)

</pre>

- apply
<pre>

    train_test['Ticket_Letter'] = train_test['Ticket_Letter'].apply(lambda x:np.nan if x.isnumeric() else x)

</pre>

- map
<pre>

    data.content = data.content.map(lambda x: filter_map(x))
    data.content = data.content.map(lambda x: get_char(x))

</pre>

- count()
<pre>

    age_null_count = dataset['Age'].isnull().sum()
    
    print(train.loc[train['TotalBsmtSF']==0, ['TotalBsmtSF']].count())
    
    percent = (train_test.isnull().sum()/train_test.isnull().count()).sort_values(ascending=False)

</pre>

- value_counts()
>Name2_sum = train_test['Name2'].value_counts().reset_index()


##  其他用到的函数(见KNN.md)

[pandas.DataFrame.pivot](https://www.cnblogs.com/sunbigdata/p/8134441.html)

[pandas 之 rename、reindex](https://blog.csdn.net/tz_zs/article/details/81355537)

[pandas中关于set_index和reset_index的用法](https://blog.csdn.net/jingyi130705008/article/details/78162758)

[python---pandas.merge](https://blog.csdn.net/zhouwenyuan1015/article/details/77334889)

[Pandas---排序sort_values](https://blog.csdn.net/wendaomudong_l2d4/article/details/80648633)


[PANDAS 数据合并与重塑（concat篇）](https://blog.csdn.net/stevenkwong/article/details/52528616)

[pandas的DataFrame、Series删除列](https://blog.csdn.net/qq_36523839/article/details/80061326)

# 小小项目: 两班成绩统计
- 合并两班成绩表
- 追加排名
- 做数据分析
- [项目地址](https://github.com/Cooper111/Analysis-of-twoClassse-s-score)