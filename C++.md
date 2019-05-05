
# C++
## 接口介绍
-------

  本接口用于股票、期货各品种的交易，可适配于掘金量化终端的实盘交易和掘金仿真交易柜台
  特别适用于量化实盘投资和仿真交易，具有运行稳定、多语言支持、形式简洁、格式统一的特点

-------

## pi接入示例

### 接口查询和委托示例

```cpp

#include <iostream>
#include "gmtrade.h"
#include "gmtrade_def.h"

//引入命名空间
using namespace gmtrade;
using namespace std;

int main ()
{
	//token身份认证
	//不关注交易事件, 可以直接使用Trade类对象调用交易函数进行交易
	Trade mt ("token");

	//设置仿真服务地址api.myquant.cn:9000
	mt.set_endpoint ("api.myquant.cn:9000");

	//登录账户ID
	mt.login ("account_id");

	//查持仓
	DataArray<Position> *ps = mt.get_position ();
	if (ps->status () == 0)
	{
		//遍历持仓
		for (int i = 0; i < ps->count (); i++)
		{
			Position &p = ps->at (i);
			cout << "account_id: " << p.account_id << endl;
			cout << "available: " << p.available << endl;
			cout << "symbol: " << p.symbol << endl;
			cout << "fpnl: " << p.fpnl << endl;
			cout << "volume: " << p.volume << endl;
			cout << "vwap: " << p.vwap << endl;
		}
		//内存释放
		ps->release ();
	}

	//下单, 以市场价买入1000股浦发银行
	Order order = mt.order_volume ("SHSE.600000", 1000, OrderSide_Buy, OrderType_Market, PositionEffect_Open);

	//撤单
	mt.order_cancel (order.cl_ord_id);
	
	//获取执行回报
	DataArray<ExecRpt>* rpts = mt.get_execution_reports ();
	if (rpts->status () == 0)
	{
		//遍历执行回报
		// ...
		
		//释放内存
		rpts->release ();
	}
	
	//查委托
	DataArray<Order>* orders = mt.get_orders ();
	if (orders->status () == 0)
	{
		//遍历委托
		//	...
		//释放内存
		orders->release ();
	}
	
	//查资金
	Cash cash{};
	int res = mt.get_cash (cash);
	if (res == 0)
	{
		cout << "account_id: "<< cash.account_id;
		cout << "available: " << cash.available;
		cout << "balance: " << cash.balance;
		cout << "fpnl: " << cash.fpnl;
	}

	cout << "程序结束" << endl;
	getchar();
}

```

### 事件方式接收交易信息示例（更及时获取交易信息）

```cpp
#include <iostream>
#include "gmtrade.h"

//引入命名空间
using namespace gmtrade;
using namespace std;

//定义交易类, 继承Trade, 通过重写方法关注交易时间, 如 on_order_status, on_execution_report 等, 具体事件说明参考"交易事件类型"
class MyTrade:public Trade
{
public:
	MyTrade (const std::string& token)
		:Trade (token.c_str())
	{
		
	}

	//关注委托状态变化
	void on_order_status(Order* order) override
	{
		cout << "symbol: " << order->symbol << endl;
		cout << "cl_ord_id" << order->cl_ord_id << endl;
		cout << "status: " << order->status << endl;
		cout << "volume: " << order->volume << endl;
		cout << "filled_amount: " << order->filled_amount << endl;
		cout << "filled_commission: " << order->filled_commission << endl;
		cout << "filled_volume: " << order->filled_volume << endl;
		cout << "filled_vwap: " << order->filled_vwap << endl;
		cout << "created_at: " << order->created_at << endl;

	}

	//关注执行回报, 如成交, 撤单拒绝等
	void on_execution_report(ExecRpt* rpt) override
	{
		cout << "price: " << rpt->price << endl;
		cout << "volume: " << rpt->volume << endl;
		cout << "amount: " << rpt->amount << endl;
		cout << "commission: " << rpt->commission << endl;
		cout << "cost: " << rpt->cost << endl;
		cout << "created_at: " << rpt->created_at<< endl;
	}

	void on_trade_data_connected () override
	{
		cout << "连接上交易服务器 .........." << endl;
	}

	void on_trade_data_disconnected () override
	{
		cout << "断开交易服务器 ........." << endl;
	}

	void on_account_status (AccountStatus *account_status) override
	{
		cout << "账户状态变化 : " << account_status->state << endl;
	}
};

int main()
{
	//token身份认证
	MyTrade mt ("token"); 
	
	// 设置服务地址api.myquant.cn:9000
	mt.set_endpoint ("api.myquant.cn:9000");

	//登录账户id
	mt.login("account_id");


	//开始接收事件
	int status = mt.start ();

	//判断状态
	if (status == 0)
	{
		cout << "连接成功, 开始运行" << endl;
	}
	else
	{
		cout << "连接失败" << endl;
	}


	// 保持进程不退出，否则回调不再生效
	getchar();
}

```


## Trade类定义

```cpp

namespace gmtrade
{

	class GM_TRADE_API Trade
	{
	public:
		Trade(const char *token);
		Trade();
		virtual ~Trade();

	public: //基础函数

		//开始接收事件
		int start();

		//停止接收事件
		void stop();

		//设置用户token
		void set_token(const char *token);

		//设置服务地址
		void set_endpoint(const char *serv_addr);


	public: //交易函数

		int login(const char *account_ids);

		//按指定量委托
		Order order_volume(const char *symbol, int volume, int side, int order_type, int position_effect, double price = 0, const char *account = NULL);

		//按指定价值委托
		Order order_value(const char *symbol, double value, int side, int order_type, int position_effect, double price = 0, const char *account = NULL);

		//按总资产指定比例委托
		Order order_percent(const char *symbol, double percent, int side, int order_type, int position_effect, double price = 0, const char *account = NULL);

		//调仓到目标持仓量
		Order order_target_volume(const char *symbol, int volume, int position_side, int order_type, double price = 0, const char *account = NULL);

		//调仓到目标持仓额
		Order order_target_value(const char *symbol, double value, int position_side, int order_type, double price = 0, const char *account = NULL);

		//调仓到目标持仓比例（总资产的比例）
		Order order_target_percent(const char *symbol, double percent, int position_side, int order_type, double price = 0, const char *account = NULL);

		//平当前所有可平持仓
		DataArray<Order>* order_close_all();

		//委托撤单
		int order_cancel(const char *cl_ord_id, const char *account = NULL);

		//撤销所有委托
		int order_cancel_all();

		//委托下单
		Order place_order(const char *symbol, int volume, int side, int order_type, int position_effect, double price = 0, int order_duration = 0, int order_qualifier = 0, double stop_price = 0, const char *account = NULL);

		//查询资金
		int get_cash(Cash &cash, const char *accounts = NULL);

		//查询委托
		DataArray<Order>* get_orders(const char *account = NULL);

		//查询未结委托
		DataArray<Order>* get_unfinished_orders(const char *account = NULL);

		//查询成交
		DataArray<ExecRpt>* get_execution_reports(const char *account = NULL);

		//查询持仓
		DataArray<Position>* get_position(const char *account = NULL);


	public: //事件函数

		//委托变化
		virtual void on_order_status(Order *order);
		//执行回报
		virtual void on_execution_report(ExecRpt *rpt);
		//实盘账号状态变化
		virtual void on_account_status(AccountStatus *account_status);
		//错误产生
		virtual void on_error(int error_code, const char *error_msg);
		//数据已经连接上
		virtual void on_trade_data_connected();
		//交易连接断开了
		virtual void on_trade_data_disconnected();

	};

} //namespace gmtrade

```


##字段和参数
### Order - 委托对象

| 属性                   | 类型               | 说明                                                                       |
|:----------------------|:------------------|:--------------------------------------------------------------------------|
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

### OrderStatus - 委托状态

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

### OrderSide - 委托方向

```cpp
OrderSide_Unknown = 0     
OrderSide_Buy = 1             ## 买入
OrderSide_Sell = 2            ## 卖出
```

### OrderType - 委托类型

```cpp
OrderType_Unknown = 0  
OrderType_Limit = 1            ## 限价委托
OrderType_Market = 2           ## 市价委托
OrderType_Stop = 3             ## 止损止盈委托
```

### OrderDuration - 委托时间属性
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

### OrderQualifier - 委托成交属性
仅在实盘模式生效，具体执行模式请参考交易所给出的定义

```cpp
OrderQualifier_Unknown = 0          
OrderQualifier_BOC     = 1            ## 对方最优价格(best of counterparty)
OrderQualifier_BOP     = 2            ## 己方最优价格(best of party)
OrderQualifier_B5TC    = 3            ## 最优五档剩余撤销(best 5 then cancel)
OrderQualifier_B5TL    = 4            ## 最优五档剩余转限价(best 5 then limit)
```

### ExecType - 执行回报类型

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


### PositionEffect - 开平仓类型

```cpp
PositionEffect_Unknown = 0
PositionEffect_Open = 1                  ## 开仓
PositionEffect_Close = 2                 ## 平仓, 具体语义取决于对应的交易所
PositionEffect_CloseToday = 3            ## 平今仓
PositionEffect_CloseYesterday = 4        ## 平昨仓

```

### PositionSide - 持仓方向

```cpp
PositionSide_Unknown = 0
PositionSide_Long = 1            ## 多方向
PositionSide_Short = 2           ## 空方向
```

### OrderRejectReason - 订单拒绝原因

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

### CancelOrderRejectReason - 取消订单拒绝原因

```cpp
CancelOrderRejectReason_OrderFinalized = 101           ## 委托已完成
CancelOrderRejectReason_UnknownOrder = 102             ## 未知委托
CancelOrderRejectReason_BrokerOption = 103             ## 柜台设置
CancelOrderRejectReason_AlreadyInPendingCancel = 104   ## 委托撤销中
```

### OrderStyle - 订单类型

```cpp
OrderStyle_Unknown = 0
OrderStyle_Volume = 1                                  ## 按指定量委托
OrderStyle_Value = 2                                   ## 按指定价值委托
OrderStyle_Percent = 3                                 ## 按指定比例委托
OrderStyle_TargetVolume = 4                            ## 调仓到目标持仓量
OrderStyle_TargetValue = 5                             ## 调仓到目标持仓额
OrderStyle_TargetPercent = 6                           ## 调仓到目标持仓比例
```

### CashPositionChangeReason - 仓位变更原因

```cpp
CashPositionChangeReason_Unknown = 0
CashPositionChangeReason_Trade = 1            ## 交易
CashPositionChangeReason_Inout = 2            ## 出入金 / 出入持仓
```

### SecType - 标的类别
```cpp
SEC_TYPE_STOCK = 1                          ## 股票
SEC_TYPE_FUND = 2                           ## 基金
SEC_TYPE_INDEX = 3                          ## 指数
SEC_TYPE_FUTURE = 4                         ## 期货
SEC_TYPE_OPTION = 5                         ## 期权
SEC_TYPE_CONFUTURE = 10                     ## 虚拟合约
```


### AccountStatus - 交易账户状态

```cpp

State_UNKNOWN = 0;       //未知
State_CONNECTING = 1;    //连接中
State_CONNECTED = 2;     //已连接
State_LOGGEDIN = 3;      //已登录
State_DISCONNECTING = 4; //断开中
State_DISCONNECTED = 5;  //已断开
State_ERROR = 6;         //错误
```


## 返回数据类型

### DataArray

DataArray类模块是行情与交易数据查询的标准返回， 表示一个结构体数组。类声明如下:

#### 类定义
```cpp
template <typename T>
class DataArray
{
public:
	//获取api调用结果, 0: 成功， 非0: 错误码
	virtual int status() = 0;

	//返回结构数组的指针
	virtual T* data() = 0;

	//返回数据的长度
	virtual int count() = 0;

	//返回下标为i的结构引用，从0开始
	virtual T& at(int i) = 0;

	//释放数据集合
	virtual void release() = 0;
};

```

典型的使用场景如下：

1. 调用数据查询函数返回一个DataArray<T>对象指针 DataArray<T> *da;
2. 调用`da->status()` 判断函数调用是否成功，0表示成功，非0表示错误码，调用失败，数组长度为0
3. 如果`da->status()`返回成功，则可以遍历数组。
4. 调用`da->release()` 释放结果集。


#### 使用举例

```cpp

//查询一段tick行情
DataArray<Tick> *da = history_ticks("SHSE.600000", "2018-07-16 09:30:00", "2018-07-16 10:30:00");

if (da->status() == 0) //判断查询是否成功
{
	//遍历行情数组
	for (int i = 0; i < da->count(); i++)
	{
		cout << da->at(i).symbol << " " << da->at(i).price << endl;
	}
}

//释放数组
da->release();

```

#### 另一种遍历方式

```cpp

//查询一段tick行情
DataArray<Tick> *da = history_ticks("SHSE.600000", "2018-07-16 09:30:00", "2018-07-16 10:30:00");

if (da->status() == 0) //判断查询是否成功
{
	//获得原始数组指针
	Tick *ticks = da->data();

	//遍历行情数组
	for (int i = 0; i < da->count(); i++)
	{
		cout << ticks[i].symbol << " " << ticks[i].price << endl;
	}
}

//释放数组
da->release();

```

#### 成员函数

##### status 获取函数调用结果

获得结果集之后， 第一步都应该先调用本成员函数判断查询数据是否成功。

**函数原型:**

```cpp
int status()
```

**参数：**

| 参数名      | 类型 | 说明                   |
|:-----------|:----|:-----------------------|
| 返回值      | int | 0: 成功， 非0: 错误码 |


**注意：**

**1.** 如果status 断定为失败时， 不应该再遍历数组， 这时直接释放数组即可。



##### data 返回结构数组的指针

返回数组的原始指针， 可以用于遍历和拷贝数据。

**函数原型:**

```cpp
T* data()
```

**参数：**

| 参数名      | 类型 | 说明                   |
|:-----------|:----|:-----------------------|
| 返回值      | 结构指针 | 具体取决于实例化类时的模板参数 |


**注意：**

**1.** 如果status 断定为失败时， 不应该再遍历数组， 这时直接释放数组即可。


##### count 返回数组长度

指的是元素个数

**函数原型:**

```cpp
int count()
```

**参数：**

| 参数名      | 类型 | 说明                   |
|:-----------|:----|:-----------------------|
| 返回值      | int | 数据元素个数 |


**注意：**

**1.** 如果status 断定为失败时， 不应该再遍历数组， 这时直接释放数组即可。


##### at 返回元素值

返回下标为i的结构引用，从0开始

**函数原型:**

```cpp
T& at(int i)
```

**参数：**

| 参数名      | 类型 | 说明                   |
|:-----------|:----|:-----------------------|
| i      | int  | 数组下标，从0开始 |
| 返回值      | T& | 返回数据元素的引用，具体取决于实例化类时的模板参数 |


**注意：**

**1.** 如果status 断定为失败时， 不应该再遍历数组， 这时直接释放数组即可。


##### release 释放数组

获取DataArray指针之后，最后都应该释放(不管status是什么状态)， 不然会造成内存泄露。调用release之后，不能再调用任何成员函数。


**函数原型:**

```cpp
void release()
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
| 1022  | 执行超时                               |
| 1100  | 交易消息服务连接失败                        |
| 1101  | 交易消息服务断开                            |