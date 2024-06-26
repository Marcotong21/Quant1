# 20181213-东吴证券-反转因子的精细结构
A股市场呈现较为显著的中长期反转效应。(例如20日收益率(Ret20)因子)但是，令人遗憾的是，反转效应的稳定性很不理想。我们注意到，传统反转因子本质上是一段区间的涨跌
幅，因此可以很自然地拆分为许多更小的时段。那么，我们可以发问，会不会存在这样的情况：组成传统反转因子的各个时段中，某些时段贡献了很强的反转，而剩余时段只是贡献了很弱的反转，甚至有的是贡献了动量效果？<br>
所谓切割问题是，找到一个好的切割方案，使得因子呈现较强的反转因子，以及较弱的动量因子。<br>
W式切割：<br>
1. 在每个月底，对于股票s，回溯其过去N个交易日的数据（为方便处理，N取偶数）<br>
2. 对于股票s，逐日计算平均单笔成交金额D（D=当日成交金额/当日成交笔数），将N个交易日按D值从大到小排序，前N/2个交易日称为高D组，后N/2个交易日称为低D组。<br>
3. 将高D组交易日的涨跌幅加总，得到因子M_high；将低D组交易日的涨跌幅加总，得到因子M_low <br>
4. 反转因子M = M_high - M_low <br>
![1702981245643](https://github.com/Marcotong21/Quant/assets/125079176/03078f9f-f32f-4a7f-a81a-203e661d6f0e)
由于M_high是强反转(rankIC=-0.082)、M_low是弱动量(rankIC=0.018)，M因子大概率会是更强的反转因子。



# 20191223-开源证券-市场微观结构研究系列（1）：A股反转之力的微观来源
**结论：反转之力的微观来源，是大单成交**

对W式切割的改进：不再根据平均值切割，而根据分位数切割。结果发现，切割标准选用越高的分
位值，W 式切割的效果越好。这意味着，决定反转强度的因素，主要来源于成交金额分布的高分位区，也即**大单成交区**。

例如将15/16的分位数作为切割标准，分位数较高的10日作为M_high,较低的10日作为M_low。结果是low有0.02的IC，有较强的动量特性；high有-0.06的IC，有很强的反转特性。
![1710530483372](https://github.com/Marcotong21/Quant/assets/125079176/f10cfe1b-71e5-49b2-9c25-d0b00b2a13cf)

回测结果：
![1710531806261](https://github.com/Marcotong21/Quant/assets/125079176/2d254fc4-d2aa-4c64-a21e-684be6579cd3)
可以发现M_high相关因子的表现最好，这也印证了大单成交区域的较强反转特性。
