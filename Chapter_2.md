# 完成您的第一个智能合约编写

## 项目结构说明

>contracts 目录为您自己编写的智能合约
>
>migrations 为需要编译链接的智能合约描述
>
>其他结构为标准的react 项目结构

## 编写第一个智能合约

>在contracts 文件夹下面新建文件MetaCoin.sol与ConvertLib.sol
>
> MetaCoin.sol 代码如下:
```pragma solidity ^0.4.0;
import "./ConvertLib.sol";

// This is just a simple example of a coin-like contract.
// It is not standards compatible and cannot be expected to talk to other
// coin/token contracts. If you want to create a standards-compliant
// token, see: https://github.com/ConsenSys/Tokens. Cheers!

contract MetaCoin {
	mapping (address => uint) balances;

	function MetaCoin() public {
		balances[tx.origin] = 10000;
	}

	function sendCoin(address receiver, uint amount) public returns(bool sufficient) {
		if (balances[msg.sender] < amount) return false;
		balances[msg.sender] -= amount;
		balances[receiver] += amount;
		return true;
	}

	function getBalanceInEth(address addr) public returns(uint){
		return ConvertLib.convert(getBalance(addr),2);
	}

	function getBalance(address addr) public returns(uint){
  	return balances[addr];
	}
}
```
> ConvertLib.sol 代码如下:
```
pragma solidity ^0.4.0;

library ConvertLib{
	function convert(uint amount,uint conversionRate) public returns (uint convertedAmount)
	{
		return amount * conversionRate;
	}
}
```
> 在migrations 下面 2_deploy_contracts.js文件里新增如下代码:
```
var SimpleStorage = artifacts.require("./SimpleStorage.sol");
var MetaCoin = artifacts.require("./MetaCoin.sol");
var ConvertLib = artifacts.require("./ConvertLib.sol");

module.exports = function(deployer) {
  deployer.deploy(ConvertLib)
  deployer.link(ConvertLib,MetaCoin)
  deployer.deploy(MetaCoin)
  deployer.deploy(SimpleStorage);
};

```
> 运行命令 truffle complie
>
> 运行命令 truffle migrate