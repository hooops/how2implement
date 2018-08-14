# 开发以太坊DAPP--水果机

## 原理

* 使用 truffle 框架，solidity语言进行智能合约的开发和部署
* 使用 web3.js 来进行页面与智能合约的交互
* 使用 ganache-cli 作为测试链进行测试
* 使用metamask作为钱包

## 机器环境

* Win10
* Node.js

## 编写智能合约

```javascript
npm isntall -g ganache-cli
npm install -g solc
npm install -g truffle

truffle init

npm install --save truffle-contract
npm install --save web3

cd a dir
touch ./contracts/SlotMachine.sol
```

```typescript
// 声明编译器版本号
pragma solidity ^0.4.4;

// 智能合约类
contract SlotMachine {
    
    address public slotMachineFunds;
    // 每个硬币的价格
    uint256 public coinPrice = 0.1 ether;
    // 合约所有者的地址
    address owner;
    // 游戏开始的事件
    event Rolled(address sender, uint rand1, uint rand2, uint rand3);
    // 存放地址到余额的映射
    mapping (address => uint) pendingWithdrawals;
    // 权限装饰器，只有合约所有者才有权限
    modifier onlyOwner() {
        require(owner == msg.sender);
        _;
    }

    function SlotMachine() {
        owner = msg.sender;
    }

    //游戏逻辑，payable表示每次游戏都要给coinPrice的钱
    function oneRoll() payable {
        require(msg.value >= coinPrice);

        uint rand1 = randomGen(msg.value);
        uint rand2 = randomGen(msg.value + 10);
        uint rand3 = randomGen(msg.value + 20);

        uint result = calculatePrize(rand1, rand2, rand3);

        Rolled(msg.sender, rand1, rand2, rand3);

        pendingWithdrawals[msg.sender] += result;

    }
    // 查看合约的余额
    function contractBalance() constant returns(uint) {
        return this.balance;
    }
    // 计算游戏结果
    function calculatePrize(uint rand1, uint rand2, uint rand3) constant returns(uint) {
        if(rand1 == 5 && rand2 == 5 && rand3 == 5) {
            return coinPrice * 30;
        } else if (rand1 == 6 && rand2 == 5 && rand3 == 6) {
            return coinPrice * 20;
        } else if (rand1 == 4 && rand2 == 4 && rand3 == 4) {
            return coinPrice * 15;
        } else if (rand1 == 3 && rand2 == 3 && rand3 == 3) {
            return coinPrice * 12;
        } else if (rand1 == 2 && rand2 == 2 && rand3 == 2) {
            return coinPrice * 10;
        } else if (rand1 == 1 && rand2 == 1 && rand3 == 1) {
            return coinPrice * 5;
        } else if ((rand1 == rand2) || (rand1 == rand3) || (rand2 == rand3)) {
            return coinPrice;
        } else {
            return 0;
        }
    }
    // 退票出钱
    function withdraw() {
        uint amount = pendingWithdrawals[msg.sender];

        pendingWithdrawals[msg.sender] = 0;

        msg.sender.transfer(amount);
    }
    // 查看用户余额
    function balanceOf(address user) constant returns(uint) {
        return pendingWithdrawals[user];
    }
    // 设置游戏硬币的价格
    function setCoinPrice(uint _coinPrice) onlyOwner {
        coinPrice = _coinPrice;
    }

    function() onlyOwner payable {
    }

    function cashout(uint _amount) onlyOwner {
        msg.sender.transfer(_amount);
    }
    // 生成随机数
    function randomGen(uint seed) private constant returns (uint randomNumber) {
        return (uint(sha3(block.blockhash(block.number-1), seed )) % 6) + 1;
    }
}
```

## 编写迁移脚本 {#编写迁移脚本}

```typescript
var SlotMachine = artifacts.require('./SlotMachine');

module.exports = (deployer) => {
    deployer.deploy(SlotMachine);
}
```

## 配置truffle.js文件 {#配置trufflejs文件}

```typescript
module.exports = {
  networks: {  
    development: {  
      host: "localhost",  
      port: 8545,  
      network_id: "*"  
    }  
  }  
};
```

## 启动ganache-cli测试链 {#启动ganache-cli}

* ganache-cli
* 记录账号信息

## 迁移部署合约 {#迁移合约}

* `truffle migrate`

## 启动前端

* npm install  &&  npm run dev

## GitHub地址

* [https://github.com/ns2250225/ethereum-slot-machine](https://github.com/ns2250225/ethereum-slot-machine)

## 效果展示

![](../.gitbook/assets/demo.gif)

