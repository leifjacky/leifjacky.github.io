---
title: "Ethereum Contract Notes"
date: 2023-04-08T00:23:00+08:00
draft: false
tags: []
card: false
weight: 0
---

Sodility IDE: https://remix.ethereum.org

first.sol

```js
pragma solidity ^0.4.0;

contract SimpleStorage {
    uint storedData;

    function set(uint x) public {
        storedData = x;
    }

    function get() public constant returns (uint) {
        return storedData;
    }
}
```

coin.sol

```js
pragma solidity ^0.4.21;

contract Coin {
    address public minter;
    mapping (address => uint) public balances;

    event Sent(address from, address to, uint amount);

    constructor() public {
        minter= msg.sender;
    }

    function mint(address receiver, uint amount) public {
        if (msg.sender != minter) return;
        balances[receiver] += amount;
    }

    function send(address receiver, uint amount) public {
        if (balances[msg.sender] < amount) return;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        emit Sent(msg.sender, receiver, amount);
    }
}
```

P17

合约可以通过消息调用的方式来调用其他合约，或者发送 ETH 到非合约账户。

P20

委托调用（delegatecall)和库

P34

很多合约就是因为溢出问题导致漏洞，比如美链的漏洞

https://learnblockchain.cn/2018/04/25/bec-overflow

P39

函数定义：

```js
function name(<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]
```
