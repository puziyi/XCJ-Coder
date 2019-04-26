# 线性回归

大部分同学应该都有学习过计量经济学，并能熟练使用EViews这样软件建立回归模型并进行分析。所以咱们今天先了解一下线性回归相关的编程操作。在Python有一个模块叫做statsmodels，主要用于处理统计相关问题，功能也很强大（ARMA, VAR等都可以计算），今天咱们主要利用这个模块自带的数据集来看一下其中“线性回归”这一模型相关的方法和用法。

## 数据集获取及描述

今天咱们用到的数据集是美国各州的犯罪率数据，印象中是上个世纪的数据，但是不妨碍我们借助它来学习。主要变量及意义对照如下表所示，其中`endog_name` 和 `exog_name`分别是内生变量和外生变量的名字列表，而具体的变量则通过`endog`和`exog`来访问。

| 变量名  | 含义                                                                                                         |
| :------ | :----------------------------------------------------------------------------------------------------------- |
| state   | State name (51 includingWashington, DC)                                                                      |
| violent | Rate of violent crimes / 100,000 population. Includes murder, forcible rape, robbery, and aggravated assault |
| murder  | Rate of murders / 100,000 population                                                                         |
| hs_grad | Percent of the population having graduated from high school or higher                                        |
| poverty | Percent of individuals below the poverty line                                                                |
| white   | Percent of population that is one race - white only                                                          |
| single  | Percent of family households that have a single parent                                                       |
| urban   | Percent of population in urbanized areas                                                                     |

```Python
# 获取数据集
>>> import statsmodels.api as sm
>>> data = sm.datasets.statecrime.load_pandas()

# 查看内生变量及外生变量
>>> data.endog_name
'murder'
>>> data.exog_name
['urban', 'poverty', 'hs_grad', 'single']
```

## 普通最小二乘

有数据之后可以利用模块中给出的OLS，即普通最小二乘法进行估计，这里我们强制加入截距项，估计及展示回归报告的代码如下：

```Python
>>> mod = sm.OLS(data.endog, sm.add_constant(data.exog))
>>> res = mod.fit()
>>> print(res.summary())

                            OLS Regression Results
==============================================================================
Dep. Variable:                 murder   R-squared:                       0.813
Model:                            OLS   Adj. R-squared:                  0.797
Method:                 Least Squares   F-statistic:                     50.08
Date:                Fri, 26 Apr 2019   Prob (F-statistic):           3.42e-16
Time:                        22:14:02   Log-Likelihood:                -95.050
No. Observations:                  51   AIC:                             200.1
Df Residuals:                      46   BIC:                             209.8
Df Model:                           4
Covariance Type:            nonrobust
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
const        -44.1024     12.086     -3.649      0.001     -68.430     -19.774
urban          0.0109      0.015      0.707      0.483      -0.020       0.042
poverty        0.4121      0.140      2.939      0.005       0.130       0.694
hs_grad        0.3059      0.117      2.611      0.012       0.070       0.542
single         0.6374      0.070      9.065      0.000       0.496       0.779
==============================================================================
Omnibus:                        1.618   Durbin-Watson:                   2.507
Prob(Omnibus):                  0.445   Jarque-Bera (JB):                0.831
Skew:                          -0.220   Prob(JB):                        0.660
Kurtosis:                       3.445   Cond. No.                     5.80e+03
==============================================================================

Warnings:
[1] Standard Errors assume that the covariance matrix of the errors is correctly
 specified.
[2] The condition number is large, 5.8e+03. This might indicate that there are
strong multicollinearity or other numerical problems.
```

## 自行构造模型形式

上面所述的方法固然是非常简单，但是我们经常遇到的情况是需要灵活调整模型形式，那么再用刚刚的外生内生变量可能会带来一些麻烦，这时候，我们可以通过`from_formula`的方法灵活改变模型形式。如下的代码便是一个这种需求下的用例。

```Python
>>> import pandas as pd
>>> df = pd.concat([data.endog, data.exog], 1)
# 通过"~"分隔解释变量及被解释变量
>>> mod = sm.OLS.from_formula('murder ~ 1 + urban + single', data=df)
>>> res = mod.fit()
>>> print(res.summary())
                            OLS Regression Results
==============================================================================
Dep. Variable:                 murder   R-squared:                       0.776
Model:                            OLS   Adj. R-squared:                  0.766
Method:                 Least Squares   F-statistic:                     83.03
Date:                Fri, 26 Apr 2019   Prob (F-statistic):           2.61e-16
Time:                        22:28:33   Log-Likelihood:                -99.713
No. Observations:                  51   AIC:                             205.4
Df Residuals:                      48   BIC:                             211.2
Df Model:                           2
Covariance Type:            nonrobust
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
Intercept    -11.7079      1.348     -8.685      0.000     -14.418      -8.997
urban         -0.0152      0.013     -1.154      0.254      -0.042       0.011
single         0.6961      0.057     12.127      0.000       0.581       0.812
==============================================================================
Omnibus:                        0.909   Durbin-Watson:                   2.535
Prob(Omnibus):                  0.635   Jarque-Bera (JB):                0.489
Skew:                          -0.234   Prob(JB):                        0.783
Kurtosis:                       3.104   Cond. No.                         375.
==============================================================================

```

## 其他估计方法

我们知道OLS是假定同方差的，但是如果出现异方差情况，这时候OLS的估计值并不是最佳，因而此时可以将`mod.fit()`方法写成`mod.fit(cov_type='HC0')`的形式以使用怀特异方差一致估计的方法进行估计。此外，还有`WLS`（加权最小二乘）、`GLS`（广义最小二乘）、`GLSAR`（generalized
least squares with autoregressive errors）等方法可以在不同情况下选用。