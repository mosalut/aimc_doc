# aimc_doc
AIMC 人工智能DAPP 接口文档
本文档三个合约下的接口
以:
-Main.function(...)
-Aitn.function(...)
-CryptoMachine.function(...)
表示

## 管理员操作
#### 创建矿机
Main.mintAitn(address account, uint amount) public onlyOwner
> 合约所有者铸造 AITN 币<br />
> 直接创建到 _account_ 地址下<br />
> 创建 _amount_ 个 AITN<br />
> 改函数只能被 _Main_ 合约创所有者调用
```
参数
	account: 铸造到的钱包地址
	amount: 铸造数量
```
