# task-202107

[IC](http://cffex.com.cn/zz500/) 是在中金所交易的、标的为中证500指数价格的期货，按照规则在 2021.7.1 这一天它存在四个到期日的合约，其中最近的两个为 IC2107 (2021.7.16 到期) 和 IC2108 (2021.8.20 到期) 。利用相似衍生品中**交易最活跃**的一个合约，来为其他合约定价，是一种常见的手段，包括期权交易中参考 underlying (或 future of underlying) 来决定 implied forward 也是如此。

请用 2021.7.1 当天 IC2107 和 IC2108 两个合约的行情数据来验证这一点: 选择合适的指标来说明 IC2107 对于 IC2108 的未来价格变动有重要的影响 （即用最活跃的合约来预测其他合约的未来价格变化）。

我们可以设立简单的框架如下：

1. 任意 t 时刻，将 IC2108 在未来 k (s) 的价格变动量 (p_{t+k} - p_t) 作为预测目标 (y)
2. 从 t 时刻及之前任意长窗口内的 IC2107、IC2108 的行情中提取因子 (x^i_t)，因子可以有任意多个
3. 目标是找到若干（或一个）对目标有解释力的因子，并说明其解释的程度

提示：

1. 对 y 有解释力的因子类型非常多，这里主要关注的是一种 lead-lag 关系，可以参考：[Epps Effect](https://en.wikipedia.org/wiki/Epps_effect), [OU Process](https://en.wikipedia.org/wiki/Ornstein%E2%80%93Uhlenbeck_process)
2. 数据为交易所原始信息，其中 ExTime 为交易所提供的时间戳，这里假定我们真实接收到的时间与之完全一致
3. 构造的因子涵盖关键的 lead-lag 关系即可，无需对结果做过多优化（不关心构造因子时用到的参数是否最佳）
4. 以直观的方式呈现结果，此外还应该提供思路和实现的代码

附数据格式说明：

|      列      |   说明               |
|:------------:|:-------------------:|
| ExTime       | 交易所时间戳 |
| InstrumentID | 合约代码 |
| LastPrice    | （产生切片之前）最近一次成交的价格 |
| BidPrice1~5  | 最优买价 |
| AskPrice1~5  | 最优卖价 |
| Ask/BidVolume1~5 | 对应价格上的挂单量 |
| Volume | 当日到切片时刻为止的累计成交量 |
| Turnover | 当日到切片时刻为止的累计成交额 |

其余字段可以忽略。

目前中金所的行情推送机制时，每 500ms 产生一次当时订单簿以及累计成交的快照，如本次快照和上次无变化（即 500ms 内没有成交、订单簿变化）则不推送，否则推送。交易所内所有标的产生快照的时间点一致，且间隔一定为 500ms 的整数倍。
