### 1.Build and start a cold node

You can choose one of the following two methods for cold node setup:

1. Hot node offline mode:

Start the hot node in offline mode:

```bash
vaschaind -coldcoldpattern
```

2. (**Recommended**) In the same directory of the hot node directory (VasChain/.vaschain), create a VasChainCold (macs/.vchain-cold) directory and create a vas directory under VasChainCold (or .vaschain-cold). Copy the chain.inf and vaschain.conf files in the hot node directory to the newly created VasChainCold (or .vaschain-cold)/vas directory.

Then when you start the cold node, please pay attention to the environment. Run the command:

```bash
vaschaind-cold 
```

#### The following is an example of a cold-selling transaction for a cold node built in Mode 2:

If you use mode one, the vaschaind-cli method in the following example does not need to use the -cold parameter, use vaschaind-cli [command].

### 2.Create an address at the cold node

Execute call command on the cold node, and pay attention to the -cold parameter 

```bash
vaschaind-cli  -cold getnewaddress
```

​	Return to newAddress 

### 3.Import address 

Import the address to the hot node: 

```bash
vaschaind-cli   importaddress newAddress false
```

### 4.Create a transaction 

Create a transaction at the hot node. Before doing it, determine if there are assets in the newly created address: 

```bash
vaschaind-cli   createrawsendtransaction newAddress '{“toAddr“:5}'
```

​	Return to hex1 of the transaction 

**In addition to creating a transaction on a hot node, you can choose to create a transaction using offline assembly:** 

**Query unspent output** 

Call command as follows:

```bash
listunspent ( minconf maxconf addresses )
```

> Method parameter

| parameter | Description                              |
| --------- | ---------------------------------------- |
| minconf   | Minimum confirmation number of the block |
| maxconf   | Maximum confirmation number of the block |
| addresses | The set of addresses to query            |

> e.g.

```bash
	listunspent 6 9999999 '[“address1“,“address2“]'
```

> Return value 

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

**Assembly transaction:** 

1. Obtain the address by parsing the block, with unspent specifying the transfer object. Call command as follows: 

```bash
	createrawtransaction '[{"txid":"id","vout":n},...]' '{"address":amount,...}' ( [data] "action" )
```

> Method parameter

| parameter    | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| transactions | Transaction collection                                       |
| addresses    | Address: Quantity                                            |
| data         | Additional data                                              |
| action       | The default is "", lock (locking the given input in the wallet), sign (signing the transaction using the wallet key), lock, sign, send (signing and sending the transaction) |

> Return value 

```bash
	#If the action is "" or lock or send#  
	hex
	#if the action is "" in other ways
	{                                                        
	  "hex": "value",                                      
	  "complete": true|false                               
	}
```

2.Set the change address and specify the transaction fee through addrawchange 

Call command 

> 调用命令

```bash
	fundrawtransaction "tx-hex" "address" ( fee )
```

> Method parameter

| parameter | Description     |
| --------- | --------------- |
| tx-hex    | Hex             |
| address   | Change address  |
| fee       | Handling charge |

> e.g.

```bash
	fundrawtransaction "HEX""ADDR"
	fundrawtransaction "HEX""ADDR" 0.01
```

> Return value 

```bash
	hex1
```

It is recommended to set the change address separately to prevent the repeated change due to vout parsing. 

### 5.Analyze the transaction

```bash
vaschain-cli   decoderawtransaction hex1
```

Return value example:

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

### 6. Get vin information

Get the address scriptPubKey information using the txid and n of vin in the transaction details returned in the previous step: 

```bash
vaschain-cli   gettxout 34f4**********************************bvre432 0
```

​	Return value example:

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

### 7.Signature transaction

The hex1 in Step 5, the txid and n of vin in Step 6, and the scriptPubKey returned in Step 7, are safely transferred to the cold node, and the signature command is called: 

```bash
vaschain-cli   -cold signrawtransactionwithkey hex1 '[{“txid“:“txid“,“vout“:n,“scriptPubKey“:“scriptPubKey-hex“}]'
```

Sign return value for hex2. 

If the address is not created on a cold node, execute the following command: 

```bash
vaschain-cli   -cold signrawtransactionwithkey hex1 '[{“txid“:“txid“,“vout“:n,“scriptPubKey“:“scriptPubKey-hex“}]' '[“privateKey“]'
```

### 8.Broadcasting transactions

Transfer the hex2 successfully signed in the previous step to the hot node for broadcast: 

```bash
vaschain-cli   sendrawtransaction hex2
```

Return to transaction txid after successful broadcast 

​	