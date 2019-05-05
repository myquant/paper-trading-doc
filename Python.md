# Python接口

## API接入示例

### 接口查询和委托示例
```python

# 本示例运行于python3.6及以上版本
from gmtrade.api import *

# token身份认证，掘金登录后可在仿真交易官网获取
set_token("token")

# 示例中为掘金官方仿真服务地址，如接入掘金终端，则填空
set_endpoint("api.myquant.cn:9000")

# 登录账户，账户ID由登录并申请仿真账户后，可复制获取；account_alias为账号别名，选填
a1 = account(account_id='', account_alias='')
login(a1)  # 注意，可以输入账户也可以输入账户组成的list

# 获取登录账户的资金，如登录多个账户需要指定账户ID
cash = get_cash()
print(f"get_cash cash={cash}")

# 获取登录账户的持仓，如登录多个账户需要指定账户ID
poses = get_positions()
print(f"get_positions poes={poses}")

# 限价、定量委托买入浦发银行股票 
data = order_volume(symbol='SHSE.600000', volume=1000, side=OrderSide_Buy, order_type=OrderType_Limit, position_effect=PositionEffect_Open, price=11)
```

### 事件方式接收信息示例

事件方式用于更及时获取交易信息
```python

# 本示例运行于python3.6及以上版本
from gmtrade.api import *

# token身份认证，掘金登录后可在仿真交易官网获取
set_token("token")

# 示例中为掘金官方仿真服务地址，如接入掘金终端，则填空
set_endpoint("api.myquant.cn:9000")

# 登录账户，账户ID由登录并申请仿真账户后，可复制获取；account_alias为账号别名，选填
a1 = account(account_id='', account_alias='')
login(a1)  # 注意，可以输入账户也可以输入账户组成的list

# 回报到达时触发
def on_execution_report(rpt):
    print(f'exec_rpt_count={rpt}')

# 委托状态变化时触发
def on_order_status(order):
    print(f'order_stats_count={order}')

# 交易服务连接成功后触发
def on_trade_data_connected():
    print('已连接交易服务.................')

# 交易服务断开后触发
def on_trade_data_disconnected():
    print('已断开交易服务.................')

# 回报到达时触发
def on_account_status(account_status):
    print(f'on_account_status status={account_status}')

if __name__ == "__main__":
    # start函数用于启动回调事件接收，非必要；事件函数为非阻塞函数，如需要同步执行需自行阻塞
    # filename=__file__用于指定当前运行的文件，如需要指定其他文件filename=‘xxx’
    status = start(filename=__file__)
    if status == 0:
        print('连接交易服务成功.................')
    else:
        print('接交易服务失败.................')
        stop()

# 开始交易业务


# 保持进程不退出，否则回调不再生效
info = input("输入任字符退出")
```
## 字段说明

### Order - 委托对象

| 属性                   | 类型               | 说明                                                                       |
|:----------------------|:------------------|:--------------------------------------------------------------------------|
                                                                 |
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
                                                                |
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

构建资金帐号

**函数原型:**

```python
account(account_id='', account_alias='')
```

**参数：**

| 参数名           | 类型                                | 说明                                         |
|:----------------|:-----------------------------------|:--------------------------------------------|
| account_id           | str               | 帐号id                                                                     |
| account_alias        | str               | 帐号别名, 可以不填                                                        |

返回 `Account` 对象

**注意**  参数传入`account` 对象集合, 约定第1个登陆成功的account对象为默认帐号, 如需要指定其他账号交易需要在函数中指定account

**函数原型:**

```
login(accounts)
```

**参数：**

| 参数名           | 类型                                | 说明                                         |
|:----------------|:-----------------------------------|:--------------------------------------------|
| accounts          | iterable\[account\]                |  资金帐号集合                       |

## 使用消息推送功能

启动eventloop，接收交易事件并触发回调函数. 返回int值, 0表示成功, 非0为失败

### start - 启动推送
**函数原型:**

```
start(filename=None, token=None, endpoint=None)
```

**参数：**

| 参数名           | 类型                                | 说明                                         |
|:----------------|:-----------------------------------|:--------------------------------------------|
| filename          | str               | 回调函数所在的文件. 当前文件可使用特殊变量 `__file__` |
| token          | str               | token身份认证，登录后可在仿真交易官网获取|
| endpoint         | str               | 仿真服务地址，不填默认本地终端服务地址|

**注意：**需要保持进程不退出，如：
```python
# 保持进程不退出，否则回调不再生效
    info = input("输入任字符退出"))
```


### stop - 停止

停止eventloop，不再接收交易事件

**函数原型:**

```
stop()
```
## 交易事件类型

用start()启动交易事件函数后，可以通过指定的交易事件函数接收对应类型的事件（如果多个账号需要在事件回调值中区分）
### on_order_status - 委托状态更新事件

响应委托状态更新事情，下单后及委托状态更新时被触发。

**函数原型:**

```
on_order_status( order)
```

**参数：**

| 参数名   | 类型                    | 说明      |
|:--------|:-----------------------|:----------|
| order   | [order](#order---委托对象 "Order---委托对象")        | 委托 |


**示例：**

```python
def on_order_status(order):
    print(order)
```


**输出：**

```
  status    ord_rej_reason  account_id      position_side    volume  symbol         target_percent    percent  updated_at             value    side    position_effect    target_volume    price    order_style  created_at           ord_rej_reason_detail    strategy_id      target_value    order_type
--------  ----------------  ------------  ---------------  --------  -----------  ----------------  ---------  -------------------  -------  ------  -----------------  ---------------  -------  -------------  -------------------  -----------------------  -------------  --------------  ------------
    8                 3       strategy_id             1     18229  SZSE.002528        -0.0999982        0.1  2017-07-27 07:00:01   100261       2                  2           -18229      5.5              3   2017-07-27 07:00:01    仓位不足 可用=16479       strategy_id      -100260            2

```


### on_execution_report - 委托执行回报事件

响应委托被执行事件，委托成交后被触发。

**函数原型:**

```
on_execution_report( execrpt)
```

**参数：**

| 参数名   | 类型                    | 说明                     |
|:--------|:-----------------------|:-------------------------|
          |
| execrpt | [execrpt](#execrpt---回报对象 "ExecRpt---回报对象")    | 回报 |


**示例：**

```python
def on_execution_report(execrpt):
    print(execrpt)
```


### on_account_status - 交易账户状态更新事件


**函数原型:**
```
on_account_status(account)
```

**参数：**

| 参数名   | 类型                    | 说明                     |
|:--------|:-----------------------|:-------------------------|
| account | object,  包含account_id(账户id), account_name(账户名),ConnectionStatus([账户状态](#accountstatus---交易账户状态)) | 交易账户状态对象，仅响应 已连接，已登录，已断开 和 错误  事件。 |

### on_error - 错误事件

描述：
当发生异常情况，比如断网时、终端服务崩溃是会触发
**函数原型:**

```
on_error(code, info)
```

**参数：**

| 参数名     | 类型                         | 说明                         |
|:----------|:----------------------------|:----------------------------|
| code | int | 错误码 |
| info | str | 错误信息 |

**示例：**

```python
def on_error(code, info):
    stop()
```

### on_trade_data_connected - 交易通道网络连接成功事件

描述：
目前监控SDK的交易和终端的链接情况，终端之后部分暂未做在内。账号连接情况可通过终端内账户连接指示灯查看

**函数原型:**

```
on_trade_data_connected()
```

**示例：**
```python
def on_trade_data_connected():
    print ('链接成功')
```

### on_trade_data_disconnected - 交易通道网络连接断开事件

描述：
目前监控SDK的交易和终端的链接情况，终端交易服务崩溃后会触发，终端之后部分暂未做在内。账号连接情况可通过终端内账户连接指示灯查看

**函数原型:**

```
on_trade_data_disconnected()
```

**示例：**
```python
def on_trade_data_disconnected():
    print ('链接失败')
```


## 委托和查询

### order_volume - 按指定量委托

**函数原型:**

```
order_volume(symbol, volume, side, order_type,position_effect, price=0,order_duration=OrderDuration_Unknown, order_qualifier=OrderQualifier_Unknown,account='')
```

**参数：**

| 参数名           | 类型                                | 说明                                         |
|:----------------|:-----------------------------------|:--------------------------------------------|
| symbol          | str                                | 标的代码                        |
| volume          | int                                | 数量                                         |
| side            | int                                | 参见[订单委托方向](#orderside---委托方向 "orderside---委托方向")              |
| order_type           | int                                | 参见[订单委托类型](#ordertype---委托类型 "ordertype---委托类型")              |
| position_effect | int                                | 参见[开平仓类型](#positioneffect---开平仓类型 "positioneffect---开平仓类型")     |
| price           | float                                | 价格                                         |
| order_duration  | int                                | 参见[ 委托时间属性](#orderduration---委托时间属性 "orderduration---委托时间属性")  |
| order_qualifier | int                                | 参见[ 委托成交属性](#orderqualifier---委托成交属性 "orderqualifier---委托成交属性") |
| account         | account id or account name or None | 帐户                                         |

**示例：**

```python
data = order_volume(symbol='SHSE.600000', volume=10000, side=OrderSide_Buy, order_type=OrderType_Limit, position_effect=PositionEffect_Open, price=11)
```

**返回：**

```
 status    volume  account_id    created_at             position_side  symbol         target_percent    percent    value    side    position_effect    target_volume    filled_amount    filled_volume    order_style    filled_vwap    price  strategy_id      target_value    order_type
--------  --------  ------------  -------------------  ---------------  -----------  ----------------  ---------  -------  ------  -----------------  ---------------  ---------------  ---------------  -------------  -------------  -------  -------------  --------------  ------------
       3     10000  strategy_id   2017-07-06 07:00:01                1  SHSE.600000              0.11       0.11   110000       1                  1            10000           110000            10000              1             11       11  strategy_id            110000             1
```
**注意：**

**1.**仅支持一个标的代码，若交易代码输入有误，终端会拒绝此单，并显示`委托代码不正确`。

**2.**若下单数量输入有误，终端会拒绝此单，并显示`委托量不正确`。股票买入最小单位为`100`，卖出最小单位为`1`,如存在不足100股的持仓一次性卖出;期货买卖最小单位为`1`，`向下取整`。

**3.**若仓位不足，终端会拒绝此单，显示`仓位不足`。平仓时股票默认`平昨仓`，期货默认`平今仓`。应研究需要，`股票也支持卖空操作`。

**4.**Order_type优先级高于price,若指定OrderTpye_Market下市价单，使用价格为最新一个tick中的最新价，price参数失效。则price参数失效。若OrderTpye_Limit限价单，仿真模式价格错误，终端拒绝此单，显示委托价格错误，`回测模式下对价格无限制`。

**5.**输入无效参数报`NameError`错误，缺少参数报`TypeError`错误。



### order_batch - 批量委托接口

**函数原型:**

```
order_batch(orders, combine=False, account='')
```

**参数：**

| 参数名   | 类型                                | 说明                 |
|:--------|:-----------------------------------|:--------------------|
| orders  | list\[order\]                      |  委托对象列表，其中委托至少包含交易接口的必选参数，参见[委托](e#Order - 委托对象 "Order 委托对象")|
| combine | bool                               | 是否是组合单, 默认不是 |
| account | account id or account name or None | 帐户                 |

**示例：**

```python
    order_1 = {'symbol': 'SHSE.600000', 'volume': 100, 'price': 11, 'side': 1,
               'order_type': 2, 'position_effect':1}
    order_2 = {'symbol': 'SHSE.600004', 'volume': 100, 'price': 11, 'side': 1,
               'order_type': 2, 'position_effect':1}
    orders = [order_1, order_2]
    batch_orders = order_batch(orders, combine=True)
    for order in batch_orders:
        print(order)
```

**返回：**

```
   status    volume  account_id    created_at             position_side  symbol         target_percent    percent    value    side    position_effect    target_volume    filled_amount    filled_volume    order_style    filled_vwap    price  strategy_id      target_value    order_type
--------  --------  ------------  -------------------  ---------------  -----------  ----------------  ---------  -------  ------  -----------------  ---------------  ---------------  ---------------  -------------  -------------  -------  -------------  --------------  ------------
       3      9000  strategy_id   2017-07-06 07:00:01                1  SHSE.600000             0.099        0.1   100000       1                  1             9000            99000             9000              3             11       11  strategy_id             99000             1

```

**注意：**

**1.**每个order的symbol仅支持一个标的代码，若交易代码输入有误，终端会拒绝此单，并显示`委托代码不正确`。

**2.**若下单数量输入有误，终端会拒绝此单，并显示`委托量不正确`。`下单数量严格按照指定数量下单`，需注意股票买入最小单位为100。

**3.**若仓位不足，终端会拒绝此单，`显示仓位不足`。应研究需要，`股票也支持卖空操作`。

**4.**Order_type优先级高于price,若指定OrderTpye_Market下市价单，则price参数失效。若OrderTpye_Limit限价单，仿真模式价格错误，终端拒绝此单，显示委托价格错误，`回测模式下对价格无限制`。

**5.**输入无效参数报NameError错误，缺少参数不报错，可能会出现下单被拒。


### order_cancel - 撤销委托

**函数原型:**

```
order_cancel(wait_cancel_orders)
```

**参数：**

| 参数名              | 类型         | 说明                                |
|:-------------------|:------------|:-----------------------------------|
| wait_cancel_orders | list\[str\] | 委托对象列表 or 单独委托对象，至少包含cl_ord_id， 参见[委托](#order---委托对象 "Order---委托对象") |


**示例：**

```python
order_1 = {'symbol': 'SHSE.600000', 'cl_ord_id': 'cl_ord_id_1', 'price': 11, 'side': 1, 'order_type':1 }
order_2 = {'symbol': 'SHSE.600004', 'cl_ord_id': 'cl_ord_id_2', 'price': 11, 'side': 1, 'order_type':1 }
orders = [order_1, order_2]
order_cancel(wait_cancel_orders=orders)

```

### order_cancel_all - 撤销所有委托

**函数原型:**

```
order_cancel_all()
```

**示例：**

```python
order_cancel_all()
```

### order_close_all - 平当前所有可平持仓

**函数原型:**

```
order_close_all()
```

**示例：**

```python
order_close_all()
```

### get_unfinished_orders - 查询日内全部未结委托

**函数原型:**

```
get_unfinished_orders()
```

**返回值:**

| 类型           | 说明                 |
|:--------------|:--------------------|
| list\[order\] | 委托对象列表，参见[委托](#order---委托对象 "Order---委托对象") |

**示例：**

```python
get_unfinished_orders()
```

**返回：**

```
   status    volume  account_id    created_at             position_side  symbol         target_percent    percent    value    side    position_effect    target_volume    filled_amount    filled_volume    order_style    filled_vwap    price  strategy_id      target_value    order_type
--------  --------  ------------  -------------------  ---------------  -----------  ----------------  ---------  -------  ------  -----------------  ---------------  ---------------  ---------------  -------------  -------------  -------  -------------  --------------  ------------
       3      9000  strategy_id   2017-07-06 07:00:01                1  SHSE.600000             0.099        0.1   100000       1                  1             9000            99000             9000              3             11       11  strategy_id             99000             1

```

### get_orders - 查询日内全部委托

**函数原型:**

```
get_orders()
```

**返回值:**

| 类型           | 说明                 |
|:--------------|:--------------------|
| list\[order\] | 委托对象列表，参见[委托](#order---委托对象 "Order---委托对象") |

**示例：**

```python
get_orders()
```

**返回：**

```
   status    volume  account_id    created_at             position_side  symbol         target_percent    percent    value    side    position_effect    target_volume    filled_amount    filled_volume    order_style    filled_vwap    price  strategy_id      target_value    order_type
--------  --------  ------------  -------------------  ---------------  -----------  ----------------  ---------  -------  ------  -----------------  ---------------  ---------------  ---------------  -------------  -------------  -------  -------------  --------------  ------------
       3      9000  strategy_id   2017-07-06 07:00:01                1  SHSE.600000             0.099        0.1   100000       1                  1             9000            99000             9000              3             11       11  strategy_id             99000             1

```


### get_execution_reports - 查询日内全部执行回报

**函数原型:**

```
get_execution_reports()
```

**返回值:**

| 类型           | 说明                 |
|:--------------|:--------------------|
| list\[execrpt\] | 回报对象列表， 参见[成交回报](#execrpt---回报对象 "ExecRpt---回报对象")   |

**示例：**
```python
get_execution_reports()
```

## 枚举常量

#### OrderStatus - 委托状态

```python
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

```python
OrderSide_Unknown = 0     
OrderSide_Buy = 1             ## 买入
OrderSide_Sell = 2            ## 卖出
```

#### OrderType - 委托类型

```python
OrderType_Unknown = 0  
OrderType_Limit = 1            ## 限价委托
OrderType_Market = 2           ## 市价委托
OrderType_Stop = 3             ## 止损止盈委托
```

#### OrderDuration - 委托时间属性
仅在实盘模式生效，具体执行模式请参考交易所给出的定义

```python
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

```python
OrderQualifier_Unknown = 0          
OrderQualifier_BOC     = 1            ## 对方最优价格(best of counterparty)
OrderQualifier_BOP     = 2            ## 己方最优价格(best of party)
OrderQualifier_B5TC    = 3            ## 最优五档剩余撤销(best 5 then cancel)
OrderQualifier_B5TL    = 4            ## 最优五档剩余转限价(best 5 then limit)
```

#### ExecType - 执行回报类型

```python
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

```python
PositionEffect_Unknown = 0
PositionEffect_Open = 1                  ## 开仓
PositionEffect_Close = 2                 ## 平仓, 具体语义取决于对应的交易所
PositionEffect_CloseToday = 3            ## 平今仓
PositionEffect_CloseYesterday = 4        ## 平昨仓

```

#### PositionSide - 持仓方向

```python
PositionSide_Unknown = 0
PositionSide_Long = 1            ## 多方向
PositionSide_Short = 2           ## 空方向
```

#### OrderRejectReason - 订单拒绝原因

```python
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

```python
CancelOrderRejectReason_OrderFinalized = 101           ## 委托已完成
CancelOrderRejectReason_UnknownOrder = 102             ## 未知委托
CancelOrderRejectReason_BrokerOption = 103             ## 柜台设置
CancelOrderRejectReason_AlreadyInPendingCancel = 104   ## 委托撤销中
```

#### OrderStyle - 订单类型

```python
OrderStyle_Unknown = 0
OrderStyle_Volume = 1                                  ## 按指定量委托
OrderStyle_Value = 2                                   ## 按指定价值委托
OrderStyle_Percent = 3                                 ## 按指定比例委托
OrderStyle_TargetVolume = 4                            ## 调仓到目标持仓量
OrderStyle_TargetValue = 5                             ## 调仓到目标持仓额
OrderStyle_TargetPercent = 6                           ## 调仓到目标持仓比例
```

#### CashPositionChangeReason - 仓位变更原因

```python
CashPositionChangeReason_Unknown = 0
CashPositionChangeReason_Trade = 1            ## 交易
CashPositionChangeReason_Inout = 2            ## 出入金 / 出入持仓
```

#### SecType - 标的类别
```python
SEC_TYPE_STOCK = 1                          ## 股票
SEC_TYPE_FUND = 2                           ## 基金
SEC_TYPE_INDEX = 3                          ## 指数
SEC_TYPE_FUTURE = 4                         ## 期货
SEC_TYPE_OPTION = 5                         ## 期权
SEC_TYPE_CONFUTURE = 10                     ## 虚拟合约
```


#### AccountStatus - 交易账户状态
```python

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
