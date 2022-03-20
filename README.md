# aimc_doc
AIMC 人工智能DAPP 接口文档

本文档三个合约下的接口

以:
```
	Main.function(...)
	Aitn.function(...)
	CryptoMachine.function(...)
```
表示

## 管理员操作
#### 创建矿机
Main.mintAitn(address account, uint amount) public onlyOwner
> 合约所有者铸造 AITN 币<br />
> 直接创建到 _account_ 地址下<br />
> 创建 _amount_ 个 AITN<br />
> 该函数会增加相应数量的 AITN 币<br />
> 该函数只能被 _Main_ 合约创所有者调用
```
参数
	account: 铸造到的钱包地址
	amount: 铸造数量
```

#### 燃烧AITN
Main.burnAitn(address account, uint amount) public onlyOwner
> 合约所有者燃烧 AITN 币<br />
> 从 _account_ 地址中燃烧 AITN 币<br />
> 燃烧 _amount_ 个 AITN<br />
> 该函数会减少相应数量的 AITN 币<br />
> 该函数只能被 _Main_ 合约所有者调用
```
参数
	account: 燃烧的钱包地址
	amount: 燃烧数量
```

#### 更新版本号
Main.versionImprove() public onlyOwner
> 暂定做法，将更改
> 版本号加1

#### 获取版本号
Main.getVersion() view public returns(uint64)
> 返回版本号
```
返回
	0: 版本号
```

## 用户调用
#### 铸造机器人
CryptoMachine.mint(uint16 \_efficiency) public onlyOwner
> 铸造一个效力为 _\_efficiency_ 的机器人
> 并且授权新机器人的转账权给 _Main_ 合约地址
> 效力在 [0, 65536) 区间范围内
```
参数
	_efficiency: 效力
```

#### 获取机器人（机器人效力）
CryptoMachine.getMachine(uint \_tokenId) view public returns(uint)
> 通过 \_tokenId 获取机器人信息
> 目前机器人的信息只有效力
```
参数
	_tokenId: 机器人唯一标识

返回
	0: 机器人效力
```

#### 购买机器人
Main.buy(uint \_tokenId) public
> 购买唯一标识为 _\_tokenId_ 的机器人
> 必须保证标识符在 _Main_ 合约所有者地址下 
> 必须保证调用者地址中有足够的 AITN 币
> 价格为每机器人效力 1050 个 AITN 币
> 购买成功后该标识符将关联到调用者地址
> 并且对应机器人的所有者被更新为调用者地址
> 并且调用者将转到 _Main_ 合约所有者地址中相应价格的 AITN 币
```
参数
	_tokenId: 机器人唯一标识符
```

#### 创建矿池
CryptoMachine.mintPool() public
> 用调用者自己的地址创建一个矿池
> 每个调用者只能同时拥有一个矿池
> 成功后新矿池属于调用者
> 全局矿池总数量 +1
> 必须保证创建者本来没有矿池

#### 销毁矿池
CryptoMachine.destoryPool() public
> 调用者销毁自己的矿池
> 成功后调用者将不再拥有任何矿池
> 全局矿池总数量 -1
> 必须保证用户已经有矿池
> 必须保证矿池下已经没有机器人
> 必须保证矿池下最后一次计算的余额都被提现

#### 机器人加入矿池
CryptoMachine.joinPool(address \_poolMinter, uint \_tokenId) public
> 更新当前矿池奖励
> 更新矿池结算时间为当前时间
> 将唯一标识为 _\_tokenId_ 的机器人加入到地址为 _\_poolMinter_ 的矿池
> 机器人加入矿池后开始产生效益
> 矿池效力上升，上升值为机器人的效力
> 全局效力上升，上升值为机器人的效力
> 必须保证矿池存在
> 必须保证机器人所有者为调用者地址
> 必须保证该机器人不再任何矿池
```
参数
	_poolMinter: 矿池所有者地址
	_tokenId: 机器人唯一标识
```

#### 机器人退出矿池
CryptoMachine.quitPool(uint \_tokenId) public
> 将 _\_tokenId_ 机器人移出所在矿池
> 矿池效力下降，下降值为机器人的效力
> 全局效力下降，下降值为机器人的效力
> 必须保证机器人所有者地址是调用者地址
> 必须保证机器人已经在一个矿池中
```
参数
	_tokenId: 机器人唯一标识
```

#### 按索引获取矿池中机器人
CryptoMachine.machineOfPoolByIndex(address \_poolMinter, uint index) view public returns(uint)
> 按索引获取矿池中机器人，并且返回唯一标识
> 必须保证索引值在范围内
> 前端可以循环传索引调用来做分页
> 当循环调用出现索引超出范围的时，可认为循环到了末尾
```
参数
	_poolMinter: 矿池地址
返回
	index: 索引
```

#### 获取矿池中机器人数量
CryptoMachine.getPool() view public returns(uint16)
> 如题
```
返回
	0: 数量
```

#### 按矿池所有者地址获取矿池中机器人数量
CryptoMachine.getPoolFrom(address \_poolMinter) view public returns(uint16)
> 如题
```
参数
	_poolMinter: 矿池地址
=======
返回
	0: 数量
```

#### 获取矿池余额
CryptoMachine.getPoolBalance() view public returns(uint)
> 每次获取余额时，都将计算一次余额及计算余额的条件变量
> 但是并不会更行余额及条件变量
> 所以用户获取余额后只是一个临时计算出来的数字，并为更新合约数据
> 但是合约中保证在提现时，能够大于调用这个方法时的余额
> 并且在提现后，用户回到余额所在的页面，经过自动刷新又重新调用了此函数
```
返回
	0: 可提现余额
```

#### 按矿池地址获取矿池余额
CryptoMachine.getPoolBalanceFrom(address \_poolMinter) view public returns(uint)
> 见 可获取矿池余额
```
参数
	_poolMinter: 矿池地址
返回
	0: 可提现余额
```

#### 获取矿池效力
CryptoMachine.getPoolEfficiencies() view public returns(uint64)
> 如题
```
返回
	0: 效力
```

#### 按矿池地址获取矿池效力
CryptoMachine.getPoolEfficienciesFrom(address \_poolMinter) view public returns(uint64)
> 如题
```
参数
	_poolMinter: 矿池地址
返回
	0: 效力
```

#### 机器所在矿池
CryptoMachine.whichPool(uint \_tokenId) view public returns(address)
> 返回机器人所在矿池地址
```
参数
	_tokenId
返回
	0: 矿池地址
```

#### 提现
Main.withDraw(uint amount) public
> 从调用者的矿池余额中提取 _amount_ 个 AITN 币
> 必须保证矿池所有者是调用者
> 必须保证矿池余额大于 _amount_
> 调用成功后矿池余额将减去 _amount_

## 目前缺少全局矿池数量接口
