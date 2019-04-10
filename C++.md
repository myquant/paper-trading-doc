[TOC]

# C++

## 示例
```cpp
int main()
{
	//token身份认证
	MyTrade mt ("xxx"); 
	
	// 示例中为掘金官方仿真服务地址，如接入掘金终端，则填空
	mt.set_endpoint ("");
	//登录账户
	mt.login("xxx");
	//事件回调启动函数

	int status = mt.start ();
	if (status == 0)
	{
		cout << "连接成功, 开始运行" << endl;
	}
	else
	{
		cout << "连接失败, 结束程序" << endl;
		mt.stop ();
	}
	
	// 保持进程不退出，否则回调不再生效
	getchar();

```
## 字段说明

### Account - 账户对象


| 属性                             | 类型                  | 说明                                                              |
|:--------------------------------|:---------------------|:-----------------------------------------------------------------|
| id                              | str                  | 账户id，实盘时用于指定交易账户                                                            |
| title                           | str                 | 账户标题，实盘时用于指定账户名称                                                           |
| cash                            | dict                 | [资金字典](#cash---资金对象 "资金字典")                                                  |
| positions(symbol='', side=None) | list                 | [持仓情况](#position---持仓对象 "持仓情况") 列表, 默认全部持仓, 可根据单一symbol（类型str）, [side](#positionSide---持仓方向 "PositionSide - 持仓方向")参数缩小查询范围 |
| position(symbol, side)          | dict | [持仓情况](#position---持仓对象 "持仓情况")，查询指定单一symbol（类型str）及持仓方向的持仓情况                                                           |

 



### Order - 委托对象

| 属性                   | 类型               | 说明                                                                       |
|:----------------------|:------------------|:--------------------------------------------------------------------------|
| strategy_id           | str               | 策略ID                                                                     |
| account_id            | str               | 账号ID                                                                     |
| account_name          | str               | 账户登录名                                                                  |
| cl_ord_id             | str               | 委托客户端ID，下单生成，固定不变                                                                |
| order_id              | str               | 委托柜台ID（系统字段）                                                                 |
| ex_ord_id             | str               | 委托交易所ID（系统字段）                                                                |
| symbol                | str               | 标的代码                                                   |
| side                  | int               | 买卖方向 取值参考 [OrderSide](#orderside---委托方向 "OrderSide - 委托方向")                          |
| position_effect       | int               | 开平标志 取值参考 [PositionEffect](#positioneffect---开平仓类型 "PositionEffect - 开平仓类型")              |
| position_side         | int               | 持仓方向 取值参考 [PositionSide](#positionside---持仓方向 "PositionSide - 持仓方向")                    |
| order_type            | int               | 委托类型 取值参考 [OrderType](#ordertype---委托类型 "OrderType - 委托类型")                          |
| order_duration        | int               | 委托时间属性 取值参考 [OrderDuration](#orderduration---委托时间属性 "OrderDuration - 委托时间属性")           |
| order_qualifier       | int               | 委托成交属性 取值参考 [OrderQualifier](#orderqualifier---委托成交属性 "OrderQualifier - 委托成交属性")         |
| order_src             | int               | 委托来源（系统字段）                                                                   |
| status                | int               | 委托状态 取值参考 [OrderStatus](#orderstatus---委托状态 "OrderStatus - 委托状态")                      |
| ord_rej_reason        | int               | 委托拒绝原因 取值参考 [OrderRejegectReason](#orderrejectreason---订单拒绝原因 "OrderRejectReason - 订单拒绝原因") |
| ord_rej_reason_detail | str               | 委托拒绝原因描述                                                             |
| price                 | float             | 委托价格                                                                   |
| stop_price            | float             | 委托止损/止盈触发价格                                                        |
| order_style           | int               | 委托风格 取值参考 [OrderStyle](#orderstyle---订单类型 "OrderStyle - 订单类型")                        |
| volume                | long              | 委托量                                                                     |
| value                 | int               | 委托额                                                                     |
| percent               | float             | 委托百分比                                                                  |
| target_volume         | long              | 委托目标量                                                                  |
| target_value          | int               | 委托目标额                                                                  |
| target_percent        | float             | 委托目标百分比                                                              |
| filled_volume         | long              | 已成量                                                                     |
| filled_vwap           | float             | 已成均价                                                                   |
| filled_amount         | float             | 已成金额                                                                   |
| created_at            | datetime.datetime | 委托创建时间                                                                |
| updated_at            | datetime.datetime | 委托更新时间                                                                |


### ExecRpt - 回报对象

| 属性                   | 类型               | 说明                                                                       |
|:----------------------|:------------------|:--------------------------------------------------------------------------|
| strategy_id           | str               | 策略ID                                                                     |
| account_id            | str               | 账号ID                                                                     |
| account_name          | str               | 账户登录名                                                                  |
| cl_ord_id             | str               | 委托客户端ID                                                                |
| order_id              | str               | 委托柜台ID                                                                 |
| exec_id             | str                | 委托交易所ID  
|symbol               |str                  |委托标的
| position_effect       | int               | 开平标志 取值参考 [PositionEffect](#positioneffect---开平仓类型 "PositionEffect - 开平仓类型")              |
| side                  | int               | 买卖方向 取值参考 [OrderSide](#orderside---委托方向 "OrderSide - 委托方向")                          |
| ord_rej_reason        | int               | 委托拒绝原因 取值参考 [OrderRejectReason](#orderrejectreason---订单拒绝原因 "OrderRejectReason - 订单拒绝原因")   |
| ord_rej_reason_detail | str               | 委托拒绝原因描述                                                             |
| exec_type             | int               | 执行回报类型 取值参考 [ExecType](#exectype---执行回报类型 "ExecType - 执行回报类型")                     |
| price                 | float             | 委托成交价格                                                                |
| volume                | long              | 委托成交量                                                                  |
| amount                | float             | 委托成交金额
|commission          |float                 |委托成交手续费
|cost                |float                 |委托成交成本金额
| created_at            | datetime.datetime | 回报创建时间                                                                |

### Cash - 资金对象

| 属性             | 类型               | 说明                                                                                    |
|:----------------|:------------------|:---------------------------------------------------------------------------------------|
| account_id      | str               | 账号ID                                                                                  |
| account_name    | str               | 账户登录名                                                                               |
| currency        | int               | 币种                                                                                    |
| nav             | float             | 净值                                                                                    |
| pnl             | float             | 净收益                                                                                  |
| fpnl            | float             | 浮动盈亏                                                                                |
| frozen          | float             | 持仓占用资金                                                                             |
| order_frozen    | float             | 挂单冻结资金                                                                             |
| available       | float             | 可用资金                                                                                |
| cum_inout       | float             | 累计出入金                                                                               |
| cum_trade       | float             | 累计交易额                                                                               |
| cum_pnl         | float             | 累计平仓收益(没扣除手续费)                                                                 |
| cum_commission  | float             | 累计手续费                                                                               |
| last_trade      | float             | 上一次交易额                                                                             |
| last_pnl        | float             | 上一次收益                                                                               |
| last_commission | float             | 上一次手续费                                                                             |
| last_inout      | float             | 上一次出入金                                                                             |
| change_reason   | int               | 资金变更原因 取值参考 [CashPositionChangeReason](#cashpositionchangereason---仓位变更原因 "CashPositionChangeReason - 仓位变更原因")  |
| change_event_id | str               | 触发资金变更事件的id                                                                      |
| created_at      | datetime.datetime | 资金初始时间                                                                             |
| updated_at      | datetime.datetime | 资金变更时间                                                                             |

### Position - 持仓对象

| 属性                | 类型               | 说明                                                                                    |
|:-------------------|:------------------|:---------------------------------------------------------------------------------------|
| account_id         | str               | 账号ID                                                                                  |
| account_name       | str               | 账户登录名                                                                               |
| symbol             | str               | 标的代码                                                                    |
| side               | int               | 持仓方向 取值参考 [PositionSide](#positionside---持仓方向 "PositionSide - 持仓方向")                                 |
| volume             | long              | 总持仓量; 如果要得到昨持仓量，公式为 `(volume - volume_today)`                                              |
| volume_today       | long              | 今日持仓量                                                                               |
| vwap               | float             | 持仓均价 `new_vwap=((position.vwap * position.volume)+(trade.volume*trade.price))/(position.volume+trade.volume)`
                                                                                |
| amount             | float             | 持仓额 `(volume*vwap*multiplier)`                                                       |
| price              | float             | 当前行情价格（回测时值为0）                                                                             |
| fpnl               | float             | 持仓浮动盈亏 `((price - vwap) * volume * multiplier)` （基于效率的考虑，回测模式fpnl只有仓位变化时或者一天更新一次,仿真模式3s更新一次）                                   |
| cost               | float             | 持仓成本 `(vwap * volume * multiplier * margin_ratio)`                                  |
| order_frozen       | int               | 挂单冻结仓位                                                                             |
| order_frozen_today | int               | 挂单冻结今仓仓位                                                                          |
| available          | long              | 可平总仓位 ，公式为`(volume - order_frozen)`; 如果要得到可平昨仓位，公式为 `(available - available_today)`          |
| available_today    | long              | 可平今仓位，公式为 `(volume_today - order_frozen_today)`(仅期货)                                         |
| last_price         | float             | 上一次成交价（回测时值为0）                                                                             |
| last_volume        | long              | 上一次成交量（回测时值为0）                                                                             |
| last_inout         | int               | 上一次出入持仓量（回测时值为0）                                                                          |
| change_reason      | int               | 仓位变更原因, 取值参考 [CashPositionChangeReason](#cashpositionchangereason---仓位变更原因 "CashPositionChangeReason - 仓位变更原因") |
| change_event_id    | str               | 触发资金变更事件的ID                                                                      |
| created_at         | datetime.datetime | 建仓时间                                                                                |
| updated_at         | datetime.datetime | 仓位变更时间

## 账户登录
### set_token - 设置用户token

**函数原型:**

```cpp
void set_token(const char *token);
```

**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| token | const char * | 系统权限密钥,可在终端系统设置-密钥管理中生成 |


**注意事项：**

不管是从构造函数传入还成员函数传入，`token`, `strategy_id`, `mode` 都是必须要设置的参数。



### login - 登录账户

**注意**  参数传入`account_ids` 字符串, 多个账户使用`,`分隔.

**函数原型:**

```
int login(const char *account_ids);
```

**参数：**

| 参数名           | 类型                                | 说明                                         |
|:----------------|:-----------------------------------|:--------------------------------------------|
| account_ids          | const char*                |  资金帐号                       |

## 使用消息推送功能
### start - 开启消息接收

启动eventloop，接收交易事件并触发回调函数. 返回int值, 0表示成功, 非0为失败

**函数原型:**

```cpp
int start();
```

**成功值**
成功返回0, 否则返回错误码.

**注意：**需要保持进程不退出，如：
```cpp
getchar()
```


### stop - 停止

停止eventloop，不再接收交易事件

**函数原型:**

```
stop()
```
## 交易事件类型

用start()启动交易事件函数后，可以通过指定的交易事件函数接收对应类型的事件（如果多个账号需要在事件回调值中区分）
### on_order_status - 委托变化

委托变化时触发


**函数原型:**

```cpp
virtual void on_order_status(Order *order);

```


**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| order |  Order* | 发生变化的委托 |


### on_execution_report - 执行回报

收到回报时触发


**函数原型:**

```cpp
virtual void on_execution_report(ExecRpt *rpt);

```


**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| rpt |  ExecRpt* | 收到的回报 |



### on_account_status - 实盘账号状态变化

实盘账号状态变化时触发, 比如实盘账号登录，退出登录等


**函数原型:**

```cpp
virtual void on_account_status(AccountStatus *account_status);

```


**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| account_status | AccountStatus * | 对应变化的账号|



### on_error - 错误产生

有错误产生时触发, 比如网络断开。


**函数原型:**

```cpp
virtual void on_error(int error_code, const char *error_msg);

```


**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| error_code | int | 错误码|
| error_msg | const char * | 错误信息|



### on_trade_data_connected - 交易已经连接上

交易已经连接时触发


**函数原型:**

```cpp
virtual void on_trade_data_connected();

```


### on_trade_data_disconnected - 交易连接断开了

交易连接断开时触发


**函数原型:**

```cpp
virtual void on_trade_data_disconnected();

```

## 委托和查询
### order_volume - 按指定量委托

按指定量委托, 如果调用成功，后续委托单状态变化将会触发on_order_status回调。

**函数原型:**

```cpp
Order order_volume(const char *symbol, int volume, int side, int order_type, int position_effect, double price = 0, const char *account = NULL);
```

**参数：**

| 参数名           | 类型                                | 说明                                         |
|:----------------|:-----------------------------------|:--------------------------------------------|
| symbol          | const char *                       | 标的代码，只能单个标的                        |
| volume          | int                                | 委托数量                                         |
| side            | int                                | 委托方向 参见 `enum OrderSide`              |
| order_type      | int                                | 委托类型 参见 `enum OrderType`       |
| position_effect | int                                | 开平类型 参见 `enum PositionSide`    |
| price           | double                             | 委托价格                                         |
| account         | const char * | 实盘账号id,关联多实盘账号时填写，可以从 get_accounts获取，也可以从终端实盘账号配置里拷贝。如果策略只关联一个账号，可以设置为NULL|
| 返回值 | Order | 一个Order结构, 如果函数调用失败， Order.status 值为 `OrderStatus_Rejected`, Order.ord_rej_reason_detail 为错误原因描述, 其它情况表示函数调用成功，Order.cl_ord_id 为本次委托的标识，可用于追溯订单状态或撤单  |

**示例：**

```cpp
//以11块的价格限价买入10000股浦发银行
Order o = order_volume("SHSE.600000", 10000, OrderSide_Buy, OrderType_Limit, PositionEffect_Open， 11);

```


**注意：**

**1.**仅支持一个标的代码，若交易代码输入有误，终端会拒绝此单，并显示`委托代码不正确`。

**2.**若下单数量输入有误，终端会拒绝此单，并显示`委托量不正确`。股票买入最小单位为`100`，卖出最小单位为`1`,如存在不足100股的持仓一次性卖出;期货买卖最小单位为`1`，`向下取整`。

**3.**若仓位不足，终端会拒绝此单，显示`仓位不足`。平仓时股票默认`平昨仓`，期货默认`平今仓`。应研究需要，`股票也支持卖空操作`。

**4.**Order_type优先级高于price,若指定OrderTpye_Market下市价单，使用价格为最新一个tick中的最新价，price参数失效。则price参数失效。若OrderTpye_Limit限价单，仿真模式价格错误，终端拒绝此单，显示委托价格错误，`回测模式下对价格无限制`。

**5.**函数调用成功并不意味着委托已经成功，只是意味委托单已经成功发出去， 委托是否成功根据on_order_status，或 get_order来判断。


### order_cancel - 委托撤单

撤销单个委托单, 如果调用成功，后续委托单状态变化将会触发on_order_status回调


**函数原型:**

```cpp
int order_cancel(const char *cl_ord_id, const char *account = NULL);

```

**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| cl_ord_id | const char * | 委托单的客户id, 可以在下单或查单时获得 |
| account   | const char * | 实盘账号id, 关联多实盘账号时填写，可以从 get_accounts获取，也可以从终端实盘账号配置里拷贝。如果策略只关联一个账号，可以设置为NULL|
| 返回值 | int | 成功返回0， 失败返回错误码 |



### order_cancel_all - 撤销所有委托

撤销所有委托, 如果调用成功，后续委托单状态变化将会触发on_order_status回调


**函数原型:**

```cpp
int order_cancel_all();
```

**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| 返回值 | int | 成功返回0， 失败返回错误码 |

### order_close_all - 平当前所有可平持仓

平当前所有可平持仓, 如果调用成功，后续委托单状态变化将会触发on_order_status回调


**函数原型:**

```cpp
DataArray<Order>* order_close_all();

```

**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| 返回值 | `DataArray<Order>*` | 一个Order结构数组 |


### get_unfinished_orders - 查询未结委托

查询所有未结委托


**函数原型:**

```cpp
DataArray<Order>* get_unfinished_orders(const char *account = NULL);
```

**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| account | const char * | 账号ID`account_id`, 如果输入为NULL, 则返回所有账号的委托 |
| 返回值 | `DataArray<Order>*` | 一个Order结构数组 |



### get_orders - 查询所有委托

查询所有委托单


**函数原型:**

```cpp
DataArray<Order>* get_orders(const char *account = NULL);
```

**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| account | const char * | 账号ID`account_id`, 如果输入为NULL, 则返回所有账号的委托 |
| 返回值 | `DataArray<Order>*` | 一个Order结构数组 |



### get_execution_reports - 查询成交

查询所有成交


**函数原型:**

```cpp
DataArray<ExecRpt>* get_execution_reports(const char *account = NULL);
```

**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| account | const char * | 账号ID`account_id`, 如果输入为NULL, 则返回所有账号的成交 |
| 返回值 | `DataArray<ExecRpt>*` | 一个ExecRpt结构数组 |


## 枚举常量

#### OrderStatus - 委托状态

```cpp
OrderStatus_Unknown = 0
OrderStatus_New = 1                   ## 已报
OrderStatus_PartiallyFilled = 2       ## 部成
OrderStatus_Filled = 3                ## 已成
OrderStatus_Canceled = 5              ## 已撤
OrderStatus_PendingCancel = 6         ## 待撤
OrderStatus_Rejected = 8              ## 已拒绝
OrderStatus_Suspended = 9             ## 挂起
OrderStatus_PendingNew = 10           ## 待报
OrderStatus_Expired = 12              ## 已过期
```

#### OrderSide - 委托方向

```cpp
OrderSide_Unknown = 0     
OrderSide_Buy = 1             ## 买入
OrderSide_Sell = 2            ## 卖出
```

#### OrderType - 委托类型

```cpp
OrderType_Unknown = 0  
OrderType_Limit = 1            ## 限价委托
OrderType_Market = 2           ## 市价委托
OrderType_Stop = 3             ## 止损止盈委托
```

#### OrderDuration - 委托时间属性
仅在实盘模式生效，具体执行模式请参考交易所给出的定义

```cpp
OrderDuration_Unknown = 0          
OrderDuration_FAK = 1           ## 即时成交剩余撤销(fill and kill)
OrderDuration_FOK = 2           ## 即时全额成交或撤销(fill or kill)
OrderDuration_GFD = 3           ## 当日有效(good for day)
OrderDuration_GFS = 4           ## 本节有效(good for section)
OrderDuration_GTD = 5           ## 指定日期前有效(goodltilldate)
OrderDuration_GTC = 6           ## 撤销前有效(goodtillcancel)
OrderDuration_GFA = 7           ## 集合竞价前有效(good for auction)
```

#### OrderQualifier - 委托成交属性
仅在实盘模式生效，具体执行模式请参考交易所给出的定义

```cpp
OrderQualifier_Unknown = 0          
OrderQualifier_BOC     = 1            ## 对方最优价格(best of counterparty)
OrderQualifier_BOP     = 2            ## 己方最优价格(best of party)
OrderQualifier_B5TC    = 3            ## 最优五档剩余撤销(best 5 then cancel)
OrderQualifier_B5TL    = 4            ## 最优五档剩余转限价(best 5 then limit)
```

#### ExecType - 执行回报类型

```cpp
ExecType_Unknown = 0
ExecType_New = 1                      ## 已报
ExecType_Canceled = 5                 ## 已撤销
ExecType_PendingCancel = 6            ## 待撤销
ExecType_Rejected = 8                 ## 已拒绝
ExecType_Suspended = 9                ## 挂起
ExecType_PendingNew = 10              ## 待报
ExecType_Expired = 12                 ## 过期
ExecType_Trade = 15                   ## 成交
ExecType_OrderStatus = 18             ## 委托状态
ExecType_CancelRejected = 19          ## 撤单被拒绝
```


#### PositionEffect - 开平仓类型

```cpp
PositionEffect_Unknown = 0
PositionEffect_Open = 1                  ## 开仓
PositionEffect_Close = 2                 ## 平仓, 具体语义取决于对应的交易所
PositionEffect_CloseToday = 3            ## 平今仓
PositionEffect_CloseYesterday = 4        ## 平昨仓

```

#### PositionSide - 持仓方向

```cpp
PositionSide_Unknown = 0
PositionSide_Long = 1            ## 多方向
PositionSide_Short = 2           ## 空方向
```

#### OrderRejectReason - 订单拒绝原因

```cpp
OrderRejectReason_Unknown = 0                          ## 未知原因
OrderRejectReason_RiskRuleCheckFailed = 1              ## 不符合风控规则
OrderRejectReason_NoEnoughCash = 2                     ## 资金不足
OrderRejectReason_NoEnoughPosition = 3                 ## 仓位不足
OrderRejectReason_IllegalAccountId = 4                 ## 非法账户ID
OrderRejectReason_IllegalStrategyId = 5                ## 非法策略ID
OrderRejectReason_IllegalSymbol = 6                    ## 非法交易标的
OrderRejectReason_IllegalVolume = 7                    ## 非法委托量
OrderRejectReason_IllegalPrice = 8                     ## 非法委托价
OrderRejectReason_AccountDisabled = 10                 ## 交易账号被禁止交易
OrderRejectReason_AccountDisconnected = 11             ## 交易账号未连接
OrderRejectReason_AccountLoggedout = 12                ## 交易账号未登录
OrderRejectReason_NotInTradingSession = 13             ## 非交易时段
OrderRejectReason_OrderTypeNotSupported = 14           ## 委托类型不支持
OrderRejectReason_Throttle = 15                        ## 流控限制
```

#### CancelOrderRejectReason - 取消订单拒绝原因

```cpp
CancelOrderRejectReason_OrderFinalized = 101           ## 委托已完成
CancelOrderRejectReason_UnknownOrder = 102             ## 未知委托
CancelOrderRejectReason_BrokerOption = 103             ## 柜台设置
CancelOrderRejectReason_AlreadyInPendingCancel = 104   ## 委托撤销中
```

#### OrderStyle - 订单类型

```cpp
OrderStyle_Unknown = 0
OrderStyle_Volume = 1                                  ## 按指定量委托
OrderStyle_Value = 2                                   ## 按指定价值委托
OrderStyle_Percent = 3                                 ## 按指定比例委托
OrderStyle_TargetVolume = 4                            ## 调仓到目标持仓量
OrderStyle_TargetValue = 5                             ## 调仓到目标持仓额
OrderStyle_TargetPercent = 6                           ## 调仓到目标持仓比例
```

#### CashPositionChangeReason - 仓位变更原因

```cpp
CashPositionChangeReason_Unknown = 0
CashPositionChangeReason_Trade = 1            ## 交易
CashPositionChangeReason_Inout = 2            ## 出入金 / 出入持仓
```

#### SecType - 标的类别
```cpp
SEC_TYPE_STOCK = 1                          ## 股票
SEC_TYPE_FUND = 2                           ## 基金
SEC_TYPE_INDEX = 3                          ## 指数
SEC_TYPE_FUTURE = 4                         ## 期货
SEC_TYPE_OPTION = 5                         ## 期权
SEC_TYPE_CONFUTURE = 10                     ## 虚拟合约
```


#### AccountStatus - 交易账户状态
```cpp

State_UNKNOWN = 0;       //未知
State_CONNECTING = 1;    //连接中
State_CONNECTED = 2;     //已连接
State_LOGGEDIN = 3;      //已登录
State_DISCONNECTING = 4; //断开中
State_DISCONNECTED = 5;  //已断开
State_ERROR = 6;         //错误
```
## 错误码

| 错误码 | 描述                                      |
|:------|:-----------------------------------------|
| 0     | 成功                                      |
| 1000  | 错误或无效的token                         |
| 1010  | 无法获取掘金服务器地址列表                   |
| 1011  | 消息包解析错误                             |
| 1012  | 网络消息包解析错误                          |
| 1013  | 交易服务调用错误                            |
| 1019  | 交易网关服务调用错误                        |
| 1020  | 无效的ACCOUNT_ID                          |
| 1021  | 非法日期格式                               |
| 1100  | 交易消息服务连接失败                        |
| 1101  | 交易消息服务断开                            |