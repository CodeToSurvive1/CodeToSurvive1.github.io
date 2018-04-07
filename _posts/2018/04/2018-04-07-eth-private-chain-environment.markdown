---
layout: "post"
title: "ethereum私有链环境测试环境搭建"
date: "2018-04-07 12:00"
category: "j2ee"
tags: [blockchain,ethereum]

---

#### ethereum私有链环境测试环境搭建


1.新建目录及创世文件

```
#私有链数据存储目录

mkdir -p /Users/mac/software/blockchain/ethereum/private_chain/PrivateChain

cd /Users/mac/software/blockchain/ethereum/private_chain
touch genesis.json
vi genesis.json
```     
加入下面内容
```

{
   "coinbase": "0x0000000000000000000000000000000000000000",
   "config": {
          "homesteadBlock": 5
    },
   "difficulty": "0x20000",
   "extraData": "0x",
   "gasLimit": "0x2FEFD8",
   "mixhash": "0x00000000000000000000000000000000000000647572616c65787365646c6578",
   "nonce": "0x0",
   "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
   "timestamp": "0x00",
   "alloc": {
          "dbdbdb2cbd23b783741e8d7fcf51e459b497e4a6":
          {
              "balance":"100000000000000000000000000000"
          }
   }
}

```

2.启动geth，初始化创世文件

```

geth --datadir /Users/mac/software/blockchain/ethereum/private_chain/PrivateChain init /Users/mac/software/blockchain/ethereum/private_chain/genesis.json

```

3.启动eth控制台   

```
geth --datadir  /Users/mac/software/blockchain/ethereum/private_chain/PrivateChain console

```

4.创建账户，挖矿

```

personal.newAccount()
Passphrase:
Repeat passphrase:
"0x63c2d7bcff151c9f8821602425c99aeb55827c49"

my = eth.accounts[0]
eth.getBalance(my)

```

```
miner.start()

```

过一段时间后停止挖矿，并查看账户余额

```

miner.stop()
eth.getBalance(my)

```


5.转账

```

personal.newAccount('123')
"0xc32fb24dc8c62ddde9e9389b4d9abb0415faf73b"

eth.getBalance(eth.accounts[1])
other=eth.accounts[1]

首先解锁被转移账号
personal.unlockAccount(my)
Unlock account 0x63c2d7bcff151c9f8821602425c99aeb55827c49
Passphrase:
true

创建交易

eth.sendTransaction({from:my,to:other,value:1})
INFO [04-07|10:43:00] Submitted transaction                    fullhash=0x1c74e91304324b721a906a67ed337cd90850ddcba13bfd00bdda6a86999b8539 recipient=0xc32FB24dC8c62dDDE9e9389B4D9AbB0415faf73b
"0x1c74e91304324b721a906a67ed337cd90850ddcba13bfd00bdda6a86999b8539"


> eth.getBalance(my)
245000000000000000000
> eth.getBalance(other)
0

此时还是0，因为需要挖矿,查看余额

miner.start()
miner.stop()
eth.getBalance(other)
1

```
