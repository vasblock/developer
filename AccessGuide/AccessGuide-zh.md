# 交易所接入指南

本文的目的主要是协助交易所上线VAS交易。

github: https://github.com/vasblock 

## 基本概念

VAS的账户体系采用了UTXO模型。



# 热节点搭建部署


## Linux(ubuntu) 节点部署

### 安装

 下载对应linux版本的可执行程序: https://github.com/vasblock/VASChain
 安装运行依赖：

```bash
curl -sL https://github.com/vasblock/VASChain/raw/master/env.sh | sudo -E bash -
```

 解压ubuntu-14/16/18.zip，使用terminal命令窗口使用vaschaind/vaschain-cli执行程序
 如若您想更方便的运行程序，可将可执行程序拷贝到/usr/lib下，并对其使用chmod赋权，即可在任何目录使用vaschaind/vaschain-cli程序

#### 启动

` ./vaschaind` 

#### 执行命令

` ./vaschain-cli command `

#### 停止节点

` ./vaschain-cli stop `

#### 启动时连接指定节点

```bash
./vaschaind -addnode=ip:port
```

## mac 节点部署

### 安装

 下载 https://github.com/vasblock/VASChain
 解压 mac.zip并进入
 使用terminal命令窗口使用vaschaind/vaschain-cli执行程序

#### 启动

` ./vaschaind` 

#### 执行命令

` ./vaschain-cli command `

#### 停止节点

` ./vaschain-cli stop `

#### 启动时连接指定节点

```
./vaschaind -addnode=ip:port
```


## window 节点部署

### 安装

 下载 https://github.com/vasblock/VASChain
 解压 win.zip并进入
 使用cmd命令窗口使用vaschaind.exe/vaschain-cli.exe执行程序

#### 启动

` vaschaind.exe ` 

#### 执行命令

` vaschain-cli.exe command `

#### 停止节点

` vaschain-cli.exe stop `

#### 启动时连接指定节点

```
vaschaind.exe -addnode=ip:port
```

启动后将开始同步区块，同时启动RPC服务（如何使用命令可查看开发者指南文档）。

## 节点启动重要参数
参数在vaschaind 后增加 以-开头，后面跟参数名=参数值

存储目录

```bash
-datadir=/data/.vas
```

区块链端口

```bash
-port=6513
```

rpc命令端口

```bash
-rpcport=6512
```

允许调用rpc的ip

```bash
-rpcallowip=127.0.0.1
```

每次生成新区块触发指定shell命令，其中%s是新区块的HASH

```bash
-blocknotify='curl http://xxx.xx.x.xxx:8080/vas?blockHash=%s'
```

例如:

```bash
./vaschaind  -datadir=/data/.vas -port=6513 -rpcport=6512 -blocknotify='curl http://xxx.xx.x.xxx:8080/vas?blockHash=%s'
```

链启动后可以将启动参数写到conf配置文件中，配置文件位于/data/.vaschain/vas/vaschain.conf

例如：

```bash
rpcuser=vaschainrpc
rpcpassword=xxxxxxxxxxxxxxxxxxxxxxxxxxx
port=6513
rpcport=6512
rpcallowip=127.0.0.1
rpcallowip=xxx.xx.x.xxx
rpcallowip=xxx.xx.x.xxx
rpcallowip=xxx.xx.x.xxx
```

设置后需重启节点。

通过以下命令可查看节点基本信息

```bash
getblockchaininfo
```

返回结果为

```json
{
    "paytxfee" : "0.0001",
    "rpcport" : 6513,
    "relayfee" : "0.0001",
    "maxout" : 1000000000000000000
}
```

其中paytxfee为当前节点的基础手续费设置，rpcport为可调用的rpc端口

## 如何通过热节点rpc调用命令

1. 确认已经将调用方IP配入rpcallowip中

2. 查看区块链目录下vaschain.conf中账号密码.

   如果未指定区块链目录Linux、Mac默认目录为~/.vaschain/vas

   ​									Windows默认目录为C:\users\\{username}\AppData\Romaming\VasChain

3. 向节点RPC发起HTTP请求

  如下：调用createaddresses命令创建两个新地址
  
  ```bash
   $ curl --user vaschainrpc:xxxxxxxxxxxxxxxxxxxxx --data-binary '{"jsonrpc": "2.0", "id":"rpccall", "method": "createaddresses", "params": [2] }' -H 'content-type: text/plain;' http://127.0.0.1:6513
   
   {"result":[{"address":"address1","pubkey":"pubkey1","privkey":"pribkey1"},{"address":"address2","pubkey":"pubkey2","privkey":"privkey2"}],"error":null,"id":"rpccall"}
  ```
  

## 生成充值地址

充值地址有如下两种生成方式：

- 方式一：动态创建vas地址

  调用如下命令：

  ```bash
  ./vaschain-cli getnewaddress
  ```

  创建好的地址会直接导入到钱包文件中。

- 方式二：提前批量创建钱包地址

  调用如下命令：

  ```bash
  ./vaschain-cli createaddresses (count)
  ```

  其中参数count为公钥/私钥对的数量，默认为1，注意这种方式创建的地址需要通过importaddr或者importprivkey的命令将地址导入钱包，才能直接调用转账的命令或者查询未花费输出。

  例如：

  - createaddresses100 
  - importaddress '["ADDR1","ADDR2","ADDR3"]' false

  ## 检测地址的有效性

  通过调用如下命令检测账户地址的有效性：

  ```bash
./vaschain-cli validateaddress (address)
  ```

  返回结果如下：
  
  ```json
{
    "isvalid" : true|false,         
  "address" : "address",           
    "ismine" : true|false,            
    "isscript" : true|false,          
    "pubkey" : "publickeyhex",       
    "iscompressed" : true|false,     
  }
  ```
  

若isvalid字段为false，则为无效地址。

## 监控用户充值

监控用户充值首先要查看区块同步情况，解析区块信息，从而确认用户是否进行充值。

### 查看区块同步情况

  调用命令如下：

```bash
  ./vaschain-cli getsyncinfo
```

  返回结果如下：

```json
  {
    "blocks" : 12,
      "headers" : 2324,
      "bestblockhash" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
  }
```

  其中headers是区块头的数量，blocks是已同步的区块数量。

  ### 解析区块信息

  调用命令如下：

  ```bash
 ./vaschain-cli getblock hash|height 4
  ```

  我们采用了UTXO模型，交易所需要解析每个TX中的VOUT部分，其中value为本地资产即VAS,token为创建的资产。有两种方式比对充值信息：

  1. 交易所记录下所有和自己相关的交易，作为用户充值提现的记录。如果发现在VOUT中有属于交易所的地址，则修改数据库中该充值地址的用户余额（当然也可能包含发行资产）.
  2. 如果在VOUT中有属于交易所的地址，先在数据库中记录下充值记录，等待几个区块确认后再修改用户余额，注意此处需要检查vin和vout中的重复地址，判断vout中是转账行为、合并unspent行为还是找零行为。

  ```bash
 ./vaschain-cli getblock 10 4
  ```

  返回结果：

  ```json
 {
    "hash" : "0020d76cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx993f6a754756f0e",
    "miner" : "VxxxxxxxxxxxxxxxxxxxxxxxxxiVSd",
    "confirmations" : 10501,
    "size" : 298,
    "height" : 10,
    "version" : 3,
    "merkleroot" : "7eed1a68e6efe4axxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx7f0cd61f7726a59",
    "tx" : [
        {
            "txid" : "7eed1a68e6efexxxxxxxxxxxxxxxxxxxxxxxxxxaf7f0cd61f7726a59",
            "version" : 1,
            "locktime" : 0,
            "vin" : [
                {
                    "coinbase" : "5a0101062f503253482f",
                    "sequence" : 4294967295
                }
            ],
            "vout" : [
                {
                    "value" : "100.00",
                    "n" : 0,
                    "scriptPubKey" : {
                        "asm" : "OP_DUP OP_HASH160 55xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx47ef3875831 OP_EQUALVERIFY OP_CHECKSIG",
                        "hex" : "76axxxxxxxxxxxxxxxxxxxxxxxxxxxxx87583188ac",
                        "reqSigs" : 1,
                        "type" : "pubkeyhash",
                        "addresses" : [
                            "VxxxxxxxxxxxxxxxxxxxxxxxxxiVSd"
                        ]
                    }
                },
                {
                    "value" : "0.00",
                    "n" : 1,
                    "scriptPubKey" : {
                        "asm" : "OP_RETURN 564153644630440220762d1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxbc72deb5a396d17ac27",
                        "hex" : "6a4c6e564153644630440220762d1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxbc72deb5a396d17ac27",
                        "type" : "nulldata"
                    },
                    "data" : [
                        "564153644630440220762d1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx72deb5a396d17ac27"
                    ]
                }
            ],
            "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0a5a0101062f503253482fffffffff0200e40bxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx72deb5a396d17ac2700000000"
        }
    ],
    "time" : 1572266966,
    "nonce" : 261,
    "bits" : "2000ffff",
    "difficulty" : 5.96046447753906e-8,
    "chainwork" : "0000000000000000000000000000000000000000000000000000000000000b00",
    "prevblockhash" : "00956a6039xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx5afdb7343893",
    "nextblockhash" : "00c313b56xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx72d6d4de714fb9"
}
  ```

  ## 处理提现请求

    1. 记录用户提现请求，更改余额；
    2. 调用send或者sendfrom命令，向用户提现地址发送交易，具体使用方法请参考《developer-zh.md》；
    3. 调用命令成功后将返回txid，记录在数据库中；
    4. 等待区块确认后，即为提现成功。
    注意，通过send或者sendfrom命令进行转账时，默认会找零会发起转账的地址，如需指定找零地址，请采用离线交易的方式。

  ### 设置节点手续费

  通过settxfee的方式可设置节点转账时的基础手续费,节点手续费默认为0.0001，具体手续费还是根据字节收取手续费。

#### 注意：

在热节点中，如果节点的地址数量过多，并且地址的uspent数量过多会造成节点性能下降，可通过以下两种方式将地址未使用的unspent合并成一个unspent，

1.用地址自己给自己转账

2.启动节点的时候，使用 -minconsolidation=50 -maxconsolidation=100  配置地址的unspent自动合并，节点默认minconsolidation=-1不进行unspent合并

这两种方式都会生成一笔地址自己给自己转账的交易，请注意区分这种交易与充值交易，防止将合并unspent交易作为充值交易处理！



### 节点加密

​	使用vaschain-cli  encryptwallet "password" 对钱包进行加密或更改钱包密码后，节点会自行停止服务，请使用vaschaind重启加密节点

### 设置keypool大小

​	节点加密之后keypool容量会减小，可以使用

```
 ./vaschain-cli keypoolrefill [size]
```

 对进行keypool设置大小



# 冷节点搭建

请参照《cold-node-zh.md》进行操作。



# 冷/热节点—冷签处理

## 离线交易

### 查询未花费输出

调用如下命令：

```bash
listunspent ( minconfirm maxconfirm addresses )
```

> 方法参数

| 参数       | 描述             |
| ---------- | ---------------- |
| minconfirm | 区块最小确认数   |
| maxconfirm | 区块最大确认数   |
| addresses  | 要查询的地址集合 |


> e.g.

```bash
	listunspent 6 9999999 "[\"address1\",\"address2\"]"
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

### 组装交易
  1.交易所通过解析区块获得地址未花费交易(unspent)指定转入对象,调用命令如下：

#### 注意：请将用于支付手续费的vin也拼入，手续费的计算请参考文档最后的手续费计算公式

  ```bash
	createrawtransaction [{"txid":"id","vout":n},...] {"address":amount,...} ( [data] "action" )
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

	hex
	or
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
	hex
```

建议交易所的找零地址单独设置，防止解析vout找零重复。



3.通过signrawtransactionwithkey进行离线私钥签名交易

> 调用命令

```bash
	signrawtransactionwithkey "tx-hex" ( [{"txid":"id","vout":n,"scriptPubKey":"hex","redeemScript":"hex"},...] ["privatekey1",...] sighashtype )
```

> 方法参数

| 参数        | 描述                                           |
| ----------- | ---------------------------------------------- |
| Tx-hex      | Hex                                            |
| prevtxs     | 交易数组，可设置为[]                           |
| privatekeys | 私钥数组，如果是在有私钥的节点签名，可设置为[] |
| sighashtype | 签署类型，默认为all                            |

> 返回值

```bash
	{
	  "hex": "value",                         
	  "complete": true|false                  
	}
```

若complete为true，则签名成功，否则失败

4.通过sendrawtransaction广播交易

> 调用命令

```bash
	sendrawtransaction "tx-hex" ( allowhighfees )
```
> 方法参数

| 参数          | 描述                             |
| ------------- | -------------------------------- |
| Tx-hex        | 签名后要广播的hex                |
| allowhighfees | 默认为false,是否允许更高的手续费 |


> e.g.

```bash
	sendrawtransaction "signedhex"
```

> 返回值

```bash
	txid
```

例：

  ```shell
  #向VxxxxxxxxxxxxxxxxxxxxxxxxxxxFSSi地址50个原生币(VAS)
	createrawtransaction '[{"txid":"526be56xxxxxxxxxxxxxxxxxx573fada996c1e08b4c89","vout":0},{"txid":"526be56xxxxxxxxxxxxxxxxxxxxxxxxxxx7cc2573fada996c1e08b4c89","vout":1}]'  '{"VxxxxxxxxxxxxxxxxxxxxxxxxxxxFSSi":{"":50}}'
	#返回 hex
01000000023935dfgg455tf57cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx00000e64ad8c6ebffc4d749dbd3e1f93090f002671000000007500000000
  ```

 ### 设置上一步Hex的找零地址VxxxxxxxxxxxxxxxxxxxxxxxxxxxFSSi 与 手续费 0.001
```shell
fundrawtransaction 01000000023935dfgg455tf57cxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx00000e64ad8c6ebffc4d749dbd3e1f93090f002671000000007500000000 VxxxxxxxxxxxxxxxxxxxxxxxxxxxFSSi 0.001
# 返回带找零地址的hex2
```
 ### 为交易进行签名并广播交易

```shell
# 使用私钥privatekey1、privatekey2对hex进行签名
signrawtransactionwithkey hex2 '[]' '["privatekey1","privatekey2"]'
# 若在含有私钥的节点进行签名广播
sendrawtransaction hex2 
返回签名后的hex与complete字段。当complete字段为true时 代表所有unspent均被签名可以进行广播
```


```json
{
    "hex" : "xxxxxxx",
    "complete" : true
}
```



  # 相关查询命令

  ### 查询余额

  使用getbalances可查询用户余额,资产最小单位为0.00000001。

### 查询交易信息

#### gettransaction

> 方法说明

返回指定的钱包交易明细,此命令只可查询在本节点的地址的相关交易

> 调用命令

```bash
	gettransaction txid
```

> 方法参数
> |参数	|描述	|
> |--	|--	|
> |txid  |txid	|

> e.g.

```bash
	gettransaction  "txid"
```

> 返回值

```bash
{
    "amount" : "100.00",
    "confirmations" : 10565,
    "generated" : true,
    "blockhash" : "0020xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx93f6a754756f0e",
    "blockindex" : 0,
    "blocktime" : 1572266966,
    "txid" : "7eed1a68exxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxcd61f7726a59",
    "walletconflicts" : [
    ],
    "valid" : true,
    "time" : 1572266966,
    "timereceived" : 1572266966,
    "details" : [
        {
            "account" : "",
            "address" : "Vxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxy",
            "category" : "generate",
            "amount" : "100.00",
            "vout" : 0
        }
    ],
    "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0a5a0101062f503253482fffffxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxa5c0544adf1410683959178877b24a6bc72deb5a396d17ac2700000000"
}	
```

####  getrawtransaction

> 方法说明

查询交易信息。

> 调用命令

```bash
	getrawtransaction "txid" 
```

> 方法参数

|参数			|描述								|
|--				|--									|
|txid			|txid								|

> e.g.

```bash
	getrawtransaction "txid"
```

> 返回值

```bash
	hex 交易的完整hex信息
```

### 手续费计算

如果您想通过根据交易大小计算手续费的方式来设置交易手续费，可参考以下公式进行计算:

fee = transactionsSize ( bytes ) / 10000000

其中交易的大小可参照一下计算方式计算：

普通交易大小：

```js
	transactionsSize  = vinNum*200+voutNum*34+10
```

携带数据的普通交易大小计算：

```js
	transactionsSize  = vinNum*200+voutNum*34+21+数据的16进制字节大小
```

vin/vout包含其他资产（例如BTC）的交易大小：

```js
	transactionsSize  = vinNum*234+voutNum*58+20
```



