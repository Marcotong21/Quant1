对于期货做市商而言，为期货合约确定**公允价**是非常重要的。传统衡量公允价的方法包括mid、weight mid。

### mid
mid的问题在于，其变化具有高度的自相关性，且其信号相对更加低频。此外，它并没有使用volume的信息，缺少有效的信息。

### weighted midprice
计算公式为 
$$\frac{Ask1*BidVolume1 +Bid1*AskVolume1}{BidVolume1+AskVolume1}$$

其中卖价用买单加权，买价用卖单加权，是因为市场合理的价格应更偏向挂单更少的那一侧。vwap的缺陷是其存在跟多的噪音，以及有时计算会更加反直觉。

### Micro price：更优的公允价
详细参考：论文：https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2970694

作者github（ipynb）：https://github.com/sstoikov/microprice

本质上是构建一个离散时间、有限状态的马尔科夫模型，其中状态由买卖盘不平衡度(imbalance)和买卖价差(spread)这两个订单簿特征离散化而成。该模型利用了马尔可夫模型从高频数据中提取状态转移规律,并将其应用到无限期预期计算中,从而获得比中间价格和加权中间价格更准确的价格预测值。

具体步骤为：

0. 选择参数，包括将imbalance离散为n_imb个组，将spread离散为n_spread个组，dt为向未来看的tick数

1.计算当前状态下,下一时刻价格保持不变或发生变动的概率矩阵Q、R1。

2.计算状态转移矩阵T（不管价格是否变化）,进而得到整体转移矩阵B=(1-Q)^-1 * T。

3.基于Q、R1和价格变动量K,计算出当前状态下的一步预期价格调整量G1。

4.利用状态转移矩阵B,对G1进行无限步长前向递推,得到收敛的无限期预期调整量G*。G*由于是基于远期预期计算的,因此可以过滤掉微观噪音,是对中间价格mid的重要补充调整。

5.最终的micro_price定义为: Micro-Price = Mid-Price + G*

其中G*是一个向量,每个元素对应不同状态的调整量。

（注：因此该结果得到的偏移量一共只有有限个状态（例如10*2=20，只有20个偏移量的结果）直接将20个值构成的序列无法构成连续的因子；我考虑将这个偏移加到z因子中作为改进，效果还行；但不如weight mid。）
