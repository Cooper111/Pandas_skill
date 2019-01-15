# Pandas进阶

## 0x00 概述 
[参考链接](https://mp.weixin.qq.com/s?__biz=MzI1MTQ4MDAyMg==&mid=2247486683&idx=1&sn=4a0fffb70751818e6188c990670dfbfb&chksm=e9f31e62de849774ef678d48651e1c72b57ed4627997a1c49e8a8a6922c29a4430e2f04a43c6&mpshare=1&scene=23&srcid=1227QItNWBxPuFL5sfMHZxvz#rd)<br>
忘了在哪里看到这篇文章，决定对自己的Pandas功夫进行提升

## 0x01 使用Datetime数据节省时间
- pandas和numpy都有一个 dtypes 的概念
- 如果没有特殊声明，那么自定义的date_time将会使用一个 object 的dtype类型，作为 str 类型会极大的影响效率
- date_time列格式化为datetime对象数组（pandas称之为时间戳）节省时间
- 格式化再更快的方法
<pre/>
    >>> @timeit(repeat=3, number=100)
    >>> def convert_with_format(df, column_name):
    ...     return pd.to_datetime(df[column_name],
    ...                           format='%d/%m/%y %H:%M')
    Best of 3 trials with 100 function calls per trial:
    Function `convert_with_format` ran in average of 0.032 seconds.



## 0x02 pandas数据的循环操作
- 使用apply方法写一个函数，函数里面写逻辑代码
<pre/>
    def apply_tariff(kwh, hour):
        """计算每个小时的电费"""    
        if 0 <= hour < 7:
            rate = 12
        elif 7 <= hour < 17:
            rate = 20
        elif 17 <= hour < 24:
            rate = 28
        else:
            raise ValueError(f'Invalid hour: {hour}')
        return rate * kwh

- **最差**添加新特征常用方法：使用iloc获得数据喂给函数，生成list，放回df
<pre/>

    >>> # 不赞同这种操作
    >>> @timeit(repeat=3, number=100)
    ... def apply_tariff_loop(df):
    ...     """Calculate costs in loop.  Modifies `df` inplace."""
    ...     energy_cost_list = []
    ...     for i in range(len(df)):
    ...         # 获取用电量和时间（小时）
    ...         energy_used = df.iloc[i]['energy_kwh']
    ...         hour = df.iloc[i]['date_time'].hour
    ...         energy_cost = apply_tariff(energy_used, hour)
    ...         energy_cost_list.append(energy_cost)
    ...     df['cost_cents'] = energy_cost_list
    ... 
    >>> apply_tariff_loop(df)
    Best of 3 trials with 100 function calls per trial:
    Function `apply_tariff_loop` ran in average of 3.152 seconds.


- **常用**方法 apply()    
<pre/>
    >>> @timeit(repeat=3, number=100)
    ... def apply_tariff_withapply(df):
    ...     df['cost_cents'] = df.apply(
    ...         lambda row: apply_tariff(
    ...             kwh=row['energy_kwh'],
    ...             hour=row['date_time'].hour),
    ...         axis=1)
    ...
    >>> apply_tariff_withapply(df)
    Best of 3 trials with 100 function calls per trial:
    Function `apply_tariff_withapply` ran in average of 0.272 seconds.

- 推荐方法：使用itertuples() 和iterrows() 循环
>通过pandas引入itertuples和iterrows方法可以使效率更快。这些都是一次产生一行的生成器方法，类似scrapy中使用的yield用法
>.itertuples为每一行产生一个namedtuple，并且行的索引值作为元组的第一个元素。nametuple是Python的collections模块中的一种数据结构，其行为类似于Python元组，但具有可通过属性查找访问的字段
>.iterrows为DataFrame中的每一行产生（index，series）这样的元组。

<pre/>
    >>> @timeit(repeat=3, number=100)
    ... def apply_tariff_iterrows(df):
    ...     energy_cost_list = []
    ...     for index, row in df.iterrows():
    ...         # 获取用电量和时间（小时）
    ...         energy_used = row['energy_kwh']
    ...         hour = row['date_time'].hour
    ...         # 添加cost列表
    ...         energy_cost = apply_tariff(energy_used, hour)
    ...         energy_cost_list.append(energy_cost)
    ...     df['cost_cents'] = energy_cost_list
    ...
    >>> apply_tariff_iterrows(df)
    Best of 3 trials with 100 function calls per trial:
    Function `apply_tariff_iterrows` ran in average of 0.713 seconds.

- 矢量化操作：使用.isin()选择数据
>什么是矢量化操作？如果你不基于一些条件，而是可以在一行代码中将所有电力消耗数据应用于该价格(df ['energy_kwh'] * 28)，类似这种。这个特定的操作就是矢量化操作的一个例子，它是在Pandas中执行的最快方法。
>你将获得一个仅包含与这些小时匹配的行的DataFrame切片。在那之后，仅仅是将切片乘以适当的费率，这是一种快速的矢量化操作.比不是Pythonic的循环快315倍，比.iterrows快71倍，比.apply快27倍


-  还可以做的更好,可以使用Pandas的pd.cut() 函数
<pre/>
    @timeit(repeat=3, number=100)
    def apply_tariff_cut(df):
        cents_per_kwh = pd.cut(x=df.index.hour,
                               bins=[0, 7, 17, 24],
                               include_lowest=True,
                               labels=[12, 20, 28]).astype(int)
        df['cost_cents'] = cents_per_kwh * df['energy_kwh']
    >>> apply_tariff_cut(df)
    Best of 3 trials with 100 function calls per trial:
    Function `apply_tariff_cut` ran in average of 0.003 seconds.

### 0x03总结
- 时间没遇到过
- 示例代码见[此](https://github.com/Cooper111/Pandas_skill/blob/master/Pandas%E5%AE%9E%E7%94%A8%E8%BF%9B%E9%98%B6%E7%A4%BA%E4%BE%8B.ipynb)
- 老子还是爱用apply :)

