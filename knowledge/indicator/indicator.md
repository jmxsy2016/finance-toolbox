# MACD

MACD由EMA（指数移动平均）线计算而来，因此我们需要构建一个短期EMA和一个长期EMA，常规选择周期为12和26。

1、线1：先得到一个DIF ： EMA12 - EMA26

2、线2：在得到一个DEA：DIF的9日加权移动平均

3、柱子：(DIF - DEA)*2

（*我们macd就看到这3个东东，2根线和1个柱*）

读了一下backtrader的源码，EMA的因子alpha是：

```python
alpha=2.0 / (1.0 + self.p.period)
```

$$\alpha$$：所以ema12就是0.153846，ema26是0.074

啥叫$$\alpha$$来着，EMA的公式是：

$$S_{t} = \alpha Y_{t-1} + (1-\alpha)S_{t-1}$$

- $$S_{t}$$是当期移动平均值
- $$Y_{t-1}$$是上期的股票值

正则化：

$$x_{new} = \frac{x-x_{min}}{x_{max}-x_{min}}$$

三个变参：12短、26长、9移

# RSI

RSI指标，相对强弱指标，RSI指标是韦尔斯-王尔德首创的，发表在他的《技术交易系统新思路》一书。

$$RSI=\frac{上升价格平均数}{上升平均数+下跌平均数} * 100%$$

RSI值反映了短期内涨势相对于跌势的强弱，也就是说RSI值越大，则涨势相对跌势越强，RSI值越小，则涨势相对于跌势越弱。

默认是14天，过去14天里上涨的价格假设有9天，合计是70元，平均就是70➗14=5；5天下跌，合计是42元，平均42➗14=3，那么今天的RSI= 5/(3+5)= 5/8

其实挺简单的，呵呵。

RSI一定在0与100之间，50为均衡点，30到70为正常交易状态。

使用：

- RSI>80，超买，易回调或转势，建议卖出
- RSI>90，严重超买，极可能短期回吐，建议立即卖出；
- RSI 50为中界线，大于50为多头，小于50为空头；
- RSI<20时，超卖，反弹概率高，建议买入
- RSI<10时，严重超卖，可能随时回升，进场好时机

但是，RSI只能作超买或超卖警告，不一定是入市讯号，大牛市或大熊市时，超买和超卖都停留一段时间，容易跌入RSI的陷阱。

短期RSI上穿过长期RSI，形成金叉，买入信号；

而当短期RSI由上向下穿过长期RSI时，形成死叉，为卖出信号。

# KDJ

参考：[1](https://zhuanlan.zhihu.com/p/199199490)，

KDJ 全名为随机指标(Stochastics)，由美国的乔治·莱恩(George Lane)博士所创，其综合了动量观念、强弱指标及移动平均线的优点。由K、D、J 三条曲线组成。研究高低价位与收盘价的关系，即通过计算当日或最近数日的最高价、最低价及收盘价等价格波动的真实波幅。

先定义RSV：

$$RSV = \frac{C_t - L_9}{H_9 - L_9} \times100$$ 

- $$C_t$$: 当日的收盘价Close
- $$L_9$$: 9日的最**低**价
- $$H_9$$: 9日的最**高**价

在定义K：

K 值为 RSV 值 3 日平滑移动平均线

$$K_t = 0.33RSV_t + (1-0.33)K_{t-1}$$就是一个指数平滑啊

再定义D：

D 值为 K 值的 3 日平滑移动平均线

$$D_t = 0.33K_t + (1-0.33)D_{t-1}$$ 指数平滑

最后定义J：

三倍 K 值减二倍D 值

$$J_t = 3D_t - 2K_t$$

使用：

- K 线向上突破 D 线，买进信号，上涨行情
- K线跌破 D 线，卖出信号，下跌行情
- K、D、J 这三值 20 以下为超卖区，是买入信号
- K、D、J 这三值 80 以上为超买区，是卖出信号
- KDJ也存在“钝化”，就是持续时间长了，有可能就失效了
- 敏感性而言，J 值最强，K 值次之，D 值最慢
- 安全性而言，J 值最差，K 值次之，D 值最稳
- KDJ 交叉；KDJ背离 K 线、D 线、J 线； KDJ同股价配合，多个角度可被用来做判断依据
    
    

# [维加斯通道](https://zhuanlan.zhihu.com/p/21441642)

永明推荐的，**[vegas](https://www.yahuicj.com/edu/basis/2626457-1.htm)，**

使用144和169两条均线，指数平均。

当通道向上或向下运行，并且通道保持一定宽度，则表示中期趋势已经确立并相对稳定发展。当通道走平运行并且收窄，则表示市场处于震荡之中。其在交易方面的用法与支撑阻力位的交易方法类同，囊获了见位交易，破位交易和顶位交易。

# 平均K线图（Heikin-Ashi）

![img50](/images/20230223/1677130895050.jpg)

计算方法:

假设普通蜡烛图的**今天**的开市价、最高价、最低价及收盘价则简称为：O、H、L、C，**昨日**收盘价为：C'

假设平均K线图的**今天**的开市价、最高价、最低价及收盘价为：open、high、low、close，**昨日**收盘价为：open'

那么，他们的之间的关系为：

$$

\begin{align*}

& open =  \frac{open'+close'}{2} \\

& close =  \frac{O+H+L+C}{4} \\

& high =  \max(H,open,close) \\

& low =  \min(L,open,close)

\end{align*}
$$

解释：

- 平均k线的开盘价 = 是昨天平均k线开、昨日普通k线收的平均（注意！开用的是平均k线的）
- 平均k线的收盘价 = 今天普通k线开、普通k线收、普通k线高、普通k线低的平均
- 平均k线的最高价 = 是今天普通k线高、平均k线开、平均k线收，取最大
- 平均k线的最低价 = 是今天普通k线低、平均k线开、平均k线收，取最小

好处：
- 图表看起来更“平滑”
- 

直观感受一下：

![img50](/images/20230223/1677133008895.jpg)

注意：
- 由于平均K线图（Heikin-Ashi）取的是平均值，因此K线显示的当前价格可能与市场实际交易价格是不一致的

参：[1](https://zhuanlan.zhihu.com/p/78616686)，[2](https://cn.tradingview.com/support/solutions/43000619436/),[3](https://www.oanda.com/bvi-ft/lab-education/technical_analysis/average-foot/)