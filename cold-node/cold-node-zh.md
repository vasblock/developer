### 1.搭建并启动冷节点

可以从以下两个方式中选择一种进行冷节点搭建：

1. 热节点脱机模式：

用脱机模式启动热节点：

```bash
vaschaind -coldcoldpattern
```

2. （**推荐**）在热节点目录（VasChain/.vaschain）的同级目录下，建立VasChainCold(mac/linux 为 .vaschain-cold)目录，并在VasChainCold（或.vaschain-cold）下建立vas目录，并将热节点目录中的chain.inf以及vaschain.conf文件一同拷贝到新建立的VasChainCold（或.vaschain-cold）/vas目录下。

然后启动冷节点时，请注意使用环境。运行命令：

```bash
vaschaind-cold 
```

#### 下面为方式2搭建的冷节点进行冷签交易示例:

​	若使用方式一进行操作，下述示例中的vaschaind-cli 方法执行不需要使用-cold参数，使用vaschaind-cli [command] 即可。

### 2.在冷节点创建地址

​	在冷节点调用命令，注意使用-cold参数

```bash
vaschaind-cli -cold getnewaddress
```

​	返回地址newAddress

### 3.导入地址

​	在热节点中导入地址：

```bash
vaschaind-cli  importaddress newAddress false
```

### 5.创建交易

​	在热节点创建交易，创建交易之前，请确定新创建的地址中是否有资产：

```bash
vaschaind-cli  createrawsendtransaction newAddress '{"toAddr":100}'
```

​	返回交易的hex1

#### 如若不在热节点上创建交易，可选择用离线组装的方式进行创建交易：

##### 查询未花费输出

调用如下命令：

```bash
listunspent ( minconf maxconf addresses )
```

> 方法参数

| 参数      | 描述             |
| --------- | ---------------- |
| minconf   | 区块最小确认数   |
| maxconf   | 区块最大确认数   |
| addresses | 要查询的地址集合 |

> e.g.

```bash
	listunspent 6 9999999 '["address1","address2"]'
```

> 返回值

```bash
	[                                        
    {
      "txid" : "txid",                      
      "vout" : n,                          
      "address" : "address",                
      "account" : "account",               
      "scriptPubKey" : "key",              
      "amount" : x.xxx,                   
      "confirmations" : n                 
    }
    ,...
  ]
```

##### 组装交易：

  1.通过解析区块获得地址未花费交易(unspent)指定转入对象,调用命令如下：

```bash
	createrawtransaction '[{"txid":"id","vout":n},...]' '{"address":amount,...}' ( [data] "action" )
```

> 方法参数

| 参数         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| transactions | 交易集合                                                     |
| addresses    | 地址：数量                                                   |
| data         | 附加数据                                                     |
| action       | 默认为“”,lock（锁定钱包中的给定输入），sign（使用钱包密钥签署交易），lock,sign，send（签署并发送交易 |

> 返回值

```bash
	#若action为""或lock或send
	hex
	#若action为其他几种
	{                                                        
	  "hex": "value",                                      
	  "complete": true|false                               
	}
```

2.通过fundrawtransaction设置找零地址以及指定交易手续费

> 调用命令

```bash
	fundrawtransaction "tx-hex" "address" ( fee )
```

> 方法参数

| 参数    | 描述     |
| ------- | -------- |
| tx-hex  | Hex      |
| address | 找零地址 |
| fee     | 手续费   |

> e.g.

```bash
	fundrawtransaction "HEX""ADDR"
	fundrawtransaction "HEX""ADDR" 0.01
```

> 返回值

```bash
	hex1
```

建议找零地址单独设置，防止解析vout找零重复。

### 5.解析交易

```bash
vaschain-cli  decoderawtransaction hex1
```

​	返回值示例:

```bash
{
    "txid" : "23vr***********************************34g523",
    "version" : 1,
    "locktime" : 0,
    "vin" : [
        {
            "txid" : "34f4**********************************bvre432",
            "vout" : 0,
            "scriptSig" : {
                "asm" : "",
                "hex" : ""
            },
            "sequence" : 4256984756
        }
    ],
    "vout" : [
        {
            "value" : "100.00",
            "n" : 0,
            "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [
                    "Vcxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxy"
                ]
            }
        },
        {
            "value" : "100.44",
            "n" : 1,
            "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [
                    "Vxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxs"
                ]
            }
        }
    ]
}
```

### 6.获取vin信息

​	使用上一步返回的交易明细中的vin的txid以及n，获取地址scriptPubKey信息：

```bash
vaschain-cli  gettxout 34f4**********************************bvre432 0
```

​	返回值示例：

```bash
{
    "bestblock" : "00xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx73",
    "confirmations" : 104,
    "value" : "200.4401",
    "scriptPubKey" : {
        "asm" : "OP_DUP OP_HASH160 xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx OP_EQUALVERIFY OP_CHECKSIG",
        "hex" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "reqSigs" : 1,
        "type" : "pubkeyhash",
        "addresses" : [
            "Vxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxs"
        ]
    },
    "version" : 1,
    "coinbase" : false
}
```

### 7.签名交易

​	将第5步中的hex1，第6步中的vin的txid以及n，第7步中返回的scriptPubKey，以安全的方式转移到冷节点中，调用签名命令：

```bash
vaschain-cli  -cold signrawtransactionwithkey hex1 '[{"txid":"txid","vout":n,"scriptPubKey":"scriptPubKey-hex"}]'
```

​	返回值签名成功的hex2。

​	如果地址不是在冷节点创建的，请执行以下命令：

```bash
vaschain-cli  -cold signrawtransactionwithkey hex1 '[{"txid":"txid","vout":n,"scriptPubKey":"scriptPubKey-hex"}]' '["privateKey"]'
```

### 8.广播交易

​	将上一步签名成功的hex2，转移到热节点进行广播：

```bash
vaschain-cli  sendrawtransaction hex2
```

​	广播成功返回交易txid

​	