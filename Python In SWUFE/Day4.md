# 时间日期和画图

今天主要介绍datetime、matplotlib及其如何利用Python绘制k线图的两篇博客分享，而**今天的小练习便是自己选取一只股票并获取数据来绘制其K线图**

## 1. datetime

datetime模块提供了丰富的日期（date）和时间（time）的操作方法，可以帮助我们实现日期格式化及简单的日期时间转换，下面通过用例进行介绍。

### 1.1 date

```Python
>>> from datetime import date
# 返回当前日期对象
>>> newday = date.today()
>>> newday
datetime.date(2019, 4, 25)

# 返回date对象的年份
>>> newday.year
2019
# 返回date对象的月份
>>> newday.month
4
# 返回date对象的日
>>> newday.day
25

# 返回指定格式的日期字符串
>>> newday.strftime("%Y-%m-%d")
'2019-04-25'

# 替换
>>> newday.replace(year=2018, month=12)
datetime.date(2018, 12, 25)
```

### 1.2 datetime

```Python
>>> from datetime import datetime, time, date

# 当前的时间datetime对象
>>> datetime.today()
datetime.datetime(2019, 4, 25, 22, 48, 41, 814131)

# 当前的日期和时间的datetime对象
>>> now = datetime.now()
>>> now
datetime.datetime(2019, 4, 25, 22, 49, 10, 219485)

# 将特定格式的日期时间字符串解析成datetime对象
>>> datetime.strptime("2016-10-10", "%Y-%m-%d")
datetime.datetime(2016, 10, 10, 0, 0)

# 从datetime中取出date
>>> now.date()
datetime.date(2019, 4, 25)

# 从datetime中取出time
>>> now.time()
datetime.time(22, 49, 10, 219485)

# 替换
>>> now.replace(year=2018)
datetime.datetime(2018, 4, 25, 22, 49, 10, 219485)
```

## 2. matplotlib

matplotlib是Python里面非常强大也非常重要的一个库，也是咱们日后会经常用到的一个工具，这里不敢托大，给大家推荐[莫烦的matplotlib教程](https://morvanzhou.github.io/tutorials/data-manipulation/plt/)，有文字有视频，大大们请尽情享受。

但此外，我还是要简要介绍一下matplotlib里面的finance这个子工具箱，从matplotlib 2.2.0版本开始，matplotlib.finance已经从matplotlib中剥离了，需要单独安装mpl_finance，并调用candlestick_ohlc()方法来绘制K线图，这一点是与后面的博客中所描述的会有不一样的地方。

## 3. Python绘制K线图

- 参考博客1，较为基础简单，作为今天的推荐内容(https://zhuanlan.zhihu.com/p/24282861)
- 参考博客2，操作上更加深入，可以当作拓展资料(https://zhuanlan.zhihu.com/p/29519040)

明天开始就是一些小项目，可能主要都会以项目实现过程的方式来与大家进行分享，其中每个人可能会遇到的bug不尽相同，大家可以及时交流，高频出现的问题下周可以做一个bug集合来梳理整合。