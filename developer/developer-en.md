# 1 VAS Development Guide

VAS provides a simple, efficient, and rich API to help developers develop and invoke underlying rpc services in VAS.

# 2.JSON-RPC API instruction set

## 2.1 Available API directives

The way to invoke the command vaschain-cli (command), such as vaschain-cli help.

#### help
> Method description

Return a list of available commands

> Call command

```bash
	help (command)
```
> Method parameter

|	parameter|description	|
|--	|--	|
|	command|Command name or no argument	|

> e.g.

```bash
	help pause
```

> return value

If there is no parameter to display the command list, if there is a parameter to display the use of the command

#### stop
> Method description

Stop blockchain operation

> Call command

```bash
	stop
```
> Method parameter

none

> return value

VasChain server stopping

#### Local wallet related

#### getblock
> Method description

View the block information of the specified hash|height

> Call command

```bash
	getblock "hash"|height ( type )
```
> Method parameter

|parameter	|description	|
|--	|--	|
|hash/height	|Block hash or block height	|
|type	|0- encoded data, 1- json object, 2- with tx encoded data, 4- with tx json object	|



> e.g.

```bash
	getblock 10
```

> return value

```bash
	Outputs (for type = 1):
	{
	  (string)  "hash" : "hash",             
	  (string)  "miner" : "miner",           
	  (numeric) "confirmations" : n,         
	  (numeric) "size" : n,                  
	  (numeric) "height" : n,             
	  (numeric) "version" : n,              
	  (string)  "merkleroot" : "xxxx",      
				"tx" : [                     
	  (string)            "TXID"             
							,...
						],
	  (numeric) "time" : ttt,                 
	  (numeric) "nonce" : n,                 
	  (string)  "bits" : "1000ffff",         
	  (numeric) "difficulty" : x.xxx,       
	  (string)  "prevblockhash" : "hash"
	  (string)  "nextblockhash" : "hash" 
	}

	Outputs (for type=0):
	  (string)  "data"                      
```
#### listblocks
> Method description

Returns information about the specified block

> Call command

```bash
	listblocks block-set-identifier ( false|true )
```
> Method parameter
|parameter	|description	|
|--	|--	|
|block-set-identifier	|Block height or block hash or block hash/height set or time range{"starttime" : start-time         Start time."endtime" : end-time             End time.}	|
|false|true	|The default is false. If true, more detailed information will be displayed.	|


> e.g.

```bash
	listblocks "hash1,hash2"
	listblocks "hash"
```

> return value

```bash
	Block information collection
```
#### getblockhash
> Method description

Returns the HASH value of the specified height block

> Call command

```bash
	getblockhash index
```
> Method parameter
|parameter	|description	|
|--	|--	|
|index	|Block height	|


> e.g.

```bash
	getblockhash 20
```

> return value

```bash
	block hash
```
#### gettxdata

> Method description

Query additional data for a specified transaction

> Call command

```bash
	gettxdata "txid" vout ( count-bytes start-byte )
```

> Method parameter

|parameter		|description						|
|--			|--							|
|txid		|txid						|
|vout		|vout						|
|count-bytes|The default is INT_MAX, the number of bytes queried|
|start-byte	|The default is 0, the starting byte number		|


> e.g.

```bash
	gettxdata "TXID" 1
```

> return value

```bash
	data
```

#### gettransaction

> Method description

Returns the specified wallet transaction details. This command can only query related transactions at the address of this node.

> Call command

```bash
	gettransaction txid
```

> Method parameter
> |parameter	|description	|
> |--	|--	|
> |txid  |txid	|

> e.g.

```bash
	gettransaction  "txid"
```

> return value

```bash
{
    "amount" : "0.00",
    "fee" : "0.000069",
    "confirmations" : 10370,
    "blockhash" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "blockindex" : 4,
    "blocktime" : 1562300531,
    "txid" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "walletconflicts" : [
    ],
    "valid" : true,
    "time" : 1562300524,
    "timereceived" : 1562300524,
    "details" : [
        {
            "account" : "",
            "address" : "vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv",
            "category" : "send",
            "amount" : "4.00",
            "vout" : 0,
            "fee" : "0.000069"
        },
        {
            "account" : "",
            "address" : "vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv",
            "category" : "send",
            "amount" : "0.019771",
            "vout" : 1,
            "fee" : "0.000069"
        },
        {
            "account" : "",
            "address" : "vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv",
            "category" : "receive",
            "amount" : "4.00",
            "vout" : 0
        },
        {
            "account" : "",
            "address" : "vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv",
            "category" : "receive",
            "amount" : "0.019771",
            "vout" : 1
        }
    ],
    "hex" : "010vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv00000"
}	
```


#### getnewaddress

> Method description

Create a new address

> Call command

```bash
	getnewaddress
```
> Method parameter

none

> return value

```bash
	new address
```
#### addmultisigaddress
> Method description

Create a multi-tick address and add it to the node

> Call command

```bash
	addmultisigaddress nrequired keys
```
> Method parameter

|parameter	|description	|
|--	|--	|
|nrequired	|The number of signature addresses required for signing	|
|keys	|Public key set	|


> e.g.

```bash
	addmultisigaddress 2 "[\"HEXPUBKEYS\",\"HEXPUBKEYS\"]"
```

> return value

```bash
	Multi-sign address
```
#### createmultisig
> Method description

Create a multi-sign address but not join the wallet, it is not recommended

> Call command

```bash
	createmultisig nrequired keys
```
> Method parameter

|parameter	|description	|
|--	|--	|
|nrequired	|The number of signature addresses required for signing	|
|keys	|Public key set	|

> e.g.

```bash
	createmultisig 2 "[\"HEX-PUBKEYS\",\"HEX-PUBKEYS\"]"
```

> return value

```bash
	{
	  "address":"multisigaddress",         
	  "redeemScript":"script"             
	}
```
#### createaddresses
> Method description

Generate one or more public/private key pairs, default 1

> Call command

```bash
	createaddresses ( count )
```
> Method parameter

|	parameter|description	|
|--	|--	|
|	count|The number of public/private key pairs created. The default is 1.	|


> e.g.

```bash
	createaddresses 10
```

> return value

```bash
	[
	   {
		  "address" : "address",            
		  "pubkey"  : "public key",         
		  "privkey" : "private key",        
	  }
	]
```
#### listaddresses
> Method description

Returns the address details in this node wallet

> Call command

```bash
	listaddresses ( addr(es) count start )
```
> Method parameter
|方法	|parameter	|
|--	|--	|
|addr(es)	|*（Display all addresses) or addr (single address) or address array*	|
|count	|Display the number of addresses, the default is INT_MAX	|
|	start|Starting from the first few, the default is -count	|


> e.g.

```bash
	listaddresses
	listaddresses "*"
	listaddresses "ADDR"
```

> return value

```bash
Array of address objects
```
#### getbalances
> Method description

Returns the list of balances in this node wallet specified by addresses (including the native currency)

> Call command

```bash
	getbalances ( address(es) assets minconf false|true false|true )
```
> Method parameter

|parameter	|description	|
|--	|--	|
|address(es)	|Query the address or address set of the balance	|
|tokens | Queryed Assets or Asset Collections |
|minconf | Block minimum confirmation value |
|false/true | defaults to false, if true, includes read-only accounts |
|false/true | defaults to false, if true, includes locked assets |


> e.g.

```bash
	getbalances
	getbalances "address"
	getbalances "address" "AAA"
```

> return value

```bash
	返回余额
	
	{
    "Vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv" : [
        {
            "tokencode" : "",
            "qty" : "100.00",
            "raw" : 100000000
        }
    ],
    "total" : [
        {
            "tokencode" : "",
            "qty" : "100.00",
            "raw" : 100000000
        }
    ]
}
```
#### listtransactionsbyaddress
> Method description

Return transaction information for this account address

> Call command

```bash
	listtransactionsbyaddress "address" ( count skip false|true )
```
> Method parameter

|parameter		|description										|
|--			|--											|
|address |Address |
|count | defaults to 10, showing the number of records |
|skip | defaults to 0, records the start of the query, |
|false/true | defaults to false, if true, displays more detailed information |


> e.g.

```bash
	listtransactionsbyaddress "ADDR"
	listtransactionsbyaddress "ADDR" 20 100
```

> return value

```bash
	[
	  {
	(object)  "balance": {...},                  
			{
	(numeric) "amount": x.xxx,                   
	(object)  "tokens": {...},                    
			}
	(array)   "myaddresses": [...],              
	(array)   "addresses": [...],             
	(array)   "permissions": [...],               
	(object)  "sell": {...},                    
	(array)   "data" : "metadata",              
	 (numeric)"confirmations": n,                 
	(string)  "blockhash": "hashvalue",           
	(numeric) "blockindex": n,                    
	(string)  "txid": "TXID",                    
	(numeric) "time": xxx,                       
	(numeric) "timereceived": xxx,            
	(string) "comment": "...",                    
	(array)  "vin": [...],                        
	(array)  "vout": [...],                      
	(string) "hex" : "data"                   
	  }
	]
```
#### listtransactions

> Method description

Query the transaction history of the wallet

> Call command

```bash
	listtransactions ( count skip false|true)
```
> Method parameter

|parameter		|description								|
|--			|--									|
|count |Number of transaction records |
|skip | starting the table below, defaults to 0 |
|false/true | defaults to false, if true, includes read-only accounts |


> e.g.

```bash
	listtransactions
	listtransactions 20 100 true
```

> return value

```bash
	[
	  {
		"balance": {...},                 
		{
		  "amount": x.xxx,                
		  "tokens": {...},                
		}
		"myaddresses": [...],            
		"addresses": [...],              
		"permissions": [...],            
		"sell": {...},                    
		"data" : "metadata",            
		"confirmations": n,              
		"blockhash": "hashvalue",       
		"blockindex": n,                
		"txid": "TXID",                  
		"time": xxx,                      
		"timereceived": xxx,             
		"comment": "...",                
		"vin": [...],                    
		"vout": [...],                    
		"hex" : "data"                   
	  }
	]
```
#### listtokens

> Method description

Returns information about the assets that have been issued in the chain

> Call command

```bash
	listtokens ( token-id(s) detail count start )
```

> Method parameter

|parameter		|description														|
|--			|--															|
|token-id(s)|Asset name or asset id or asset name set or asset id set |
|detail |true or false, defaults to false, if true, displays more detailed information about the asset |
|count |display quantity, default is INT_MAX |
|start | defaults to -count |


> e.g.

```bash
	listtokens
	listtokens "AAA"
```

> return value

```bash
	An asset collection
```

#### listtransactionbytoken

> Method description

Query transaction information for a specific asset

> Call command

```bash
	listtransactionbytoken "token-id" ( count start local-ordering )
```
> Method parameter

|parameter	|description	|
|--	|--	|
|token-id | Asset id or asset name |
|count |display quantity, default is 10|
|start |Starting number, default is -count |
|local-ordering |The default is false.false means to query from the wallet, true means to query from the chain |


> e.g.

```bash
	listtransactionbytoken "token"
	listtransactionbytoken "token"  10 100 true
```

> return value

```bash
	List of transactions
```
#### send
> Method description

Send one or more assets

> Call command

```bash
	send "address" object ( "comment" "comment-to" )
```
> Method parameter

|parameter	|description	|
|--	|--	|
|address |Transfer to address |
|object |If you fill in the quantity directly, send the original asset, or fill in {"token-name" : token-count,"": nativecurrency-count Native currency use "",...} |
|comment |Additional information, not on the blockchain |
|comment-to |Additional information attribution, not on the blockchain |


> e.g.

```bash
	send "ADDRTO" "{\"assetname\":1000,\"\":2000}"
```

> return value

```bash
	txid
```
#### sendfrom
> Method description

Specify address to initiate a transfer

> Call command

```bash
	sendfrom "from-address" "to-address" quantities-object ( "comment" "comment-to" )
```
> Method parameter

|parameter				|description																																	|
|--					|--																																		|
|from-address |initiation address |
|to-address |Destination Address |
|quantities-object |If you fill in the quantity directly, send the original asset, or fill in {"token-name" : token-count,"": nativecurrency-count Native currency use "",...} |
|comment |Additional information, not on the blockchain |
|comment-to |Additional information attribution, not on the blockchain |


> e.g.

```bash
	sendfrom "ADDRFROM" "ADDRTO" "{\"assetname\":1000,\"\":2000}"
	sendfrom "ADDRFROM" "ADDRTO" 0.1 "desc" "desc out"
```

> return value

```bash
	txid
```
#### sendtoken
> Method description

Send a certain amount of assets to the specified address

> Call command

```bash
	sendtoken "address" "token-id" count ( native-amount "comment" "comment-to" )
```
> Method parameter

|parameter			|description						|
|--				|--							|
|address |Address |
|token-id | Asset id or asset name |
|count |Quantity |
|native-amount |Quantity of native assets |
|comment |Additional information, not on the blockchain |
|comment-to |Additional information attribution, not on the blockchain |


> e.g.

```bash
	sendtoken "ADDRTO" tokenname 1000
	sendtoken "ADDRTO" tokenname "ADDRTO" tokenname 1000
 1000 0.1 "desc" "desc out"
```

> return value

```bash
	txid
```
#### sendtokenfrom
> Method description

Specify address to initiate a transfer

> Call command

```bash
	sendtokenfrom "from-address" "to-address" "token-id" token-count ( native-amount "comment" "comment-to" )
```
> Method parameter

|parameter			|description						|
|--				|--							|
|from-address |Initiate a transfer address |
|to-address | destination address |
|token-id | Asset id or name |
|token-count |Number of assets |
|native-amount |Quantity of native assets |
|comment |Additional information, not on the blockchain |
|comment-to |Additional information attribution, not on the blockchain |


> e.g.

```bash
	sendtokenfrom "ADDRFROM" "ADDRTO" 0.1 tokenname 1000
	sendtokenfrom "ADDRFROM" "ADDRTO" 0.1 tokenname 1000 "desc" "desc out"
```

> return value

```bash
	txid
```
#### senddata
> Method description

Similar to send, additional data

> Call command

```bash
	senddata "address" amount|token-quantities data|send-new-item
```
> Method parameter

|parameter				|description														|
|--					|--															|
|address | destination address |
|token-quantities | Number of native assets or assets obj |
|data |data hex,text,json, can view the transferable data with help with-data |


> e.g.

```bash
	senddata "ADDR" "{\"token\":1000,\"token\":2000}" data-hex
	senddata "ADDR" 0.1 data-hex
```

> return value

```bash
	txid
```
#### senddatafrom
> Method description

The specified address initiates the transfer and carries the data.

> Call command

```bash
	senddatafrom "from-address" "to-address" amount|token-qty data|send-new-item
```
> Method parameter

|parameter				|description														|
|--					|--															|
|from-address |initiation address |
|to-address | destination address |
|token-quantities | Number of native assets or assets obj |
|data |data hex,text,json, can view the transferable data with help with-data |

> e.g.

```bash
	senddatafrom "ADDR" "ADDR" "{\"key\":100,\"key2\":200}" data-hex
	senddatafrom "ADDR" "ADDR" 0.1 data-hex
```

> return value

```bash
	txid
```
#### dumpprivkey

> Method description

Export private key

> Call command

```bash
	dumpprivkey "address"
```

> Method parameter

|parameter	|description	|
|--	|--	|
|address |To export the address of the private key |


> return value

```bash
	私钥
```

#### importprivkey

> Method description

Import private key

> Call command

```bash
	importprivkey privkey(s)
```

> Method parameter

|parameter		|description									|
|--			|--										|
|privkey(s) |Required, a single private key or private key array, |
|rescan | Whether to rescan the wallet transaction, the default is true |



> e.g.

```bash
	importprivkey "PRIKEY"
```

> return value

```bash
	none
```

#### importaddress

> Method description

Add address to the wallet (no private key), create one or more read-only accounts

> Call command

```bash
	importaddress address(es)
```

> Method parameter

|parameter		|description									|
|--			|--										|
|address(es)|Required, single address or address array |
|rescan | Whether to rescan the wallet transaction, the default is true |


> e.g.

```bash
	importaddress "ADDR"
```

> return value

```bash
	none
```

#### backupwallet

> Method description

Backup wallet.dat wallet file

> Call command

```bash
	backupwallet "destination"
```

> Method parameter

|parameter	|description	|
|--	|--	|
|destination |File Path |


> e.g.

```bash
	backupwallet "backup.dat"
```

> return value

```bash
	none
```

#### dumpwallet

> Method description

Dump all private keys in the wallet to a text file

> Call command

```bash
	dumpwallet "filename"
```

> Method parameter

|parameter	|description	|
|--	|--	|
| filename|File path to export to |


> e.g.

```bash
	dumpwallet "test"
```

> return value

```bash
	none
```

#### encryptwallet

> Method description

Encrypt the node's wallet for the first time, using password as the unlock password
Please note that after encryption, you cannot restore to the passwordless state unless you delete all the files of the node and resynchronize the blocks!

> Call command

```bash
	encryptwallet "password"
```

> Method parameter

|	parameter|description	|
|--	|--	|
|	password|password	|


> e.g.

```bash
	encryptwallet "password"
```

> return value

```bash
	none
```

Note that once the node is encrypted, whether it is to restart the node or change the password, you need to call the resume incoming,mining command.

#### walletpassphrasechange

> Method description

Change your wallet password

> Call command

```bash
	walletpassphrasechange password newpassword
```

> Method parameter

|	parameter|description	|
|--	|--	|
| password|old password |
| newpassword|New Password |


> return value

```bash
	none
```

#### walletpassphrase

> Method description

Use password to unlock the node's wallet, which is valid for the time.

> Call command

```bash
	walletpassphrase password time
```

> Method parameter

|parameter	|description	|
|--	|--	|
|password |Password |
| time|Unlock time in seconds |


> e.g.

```bash
	walletpassphrase password 60
```

> return value

```bash
	true|false
```

#### validateaddress

> Method description

Verify that the address is correct

> Call command

```bash
	validateaddress "address"|"pubkey"|"privkey"
```
> Method parameter

|parameter	|description	|
|--	|--	|
|address	|address		|


> e.g.

```bash
	validateaddress "ADDR"
```

> return value

```bash
	{
	  "isvalid" : true|false,          
	  "address" : "address",            
	  "ismine" : true|false,            
	  "isscript" : true|false,         
	  "pubkey" : "publickeyhex",        
	  "iscompressed" : true|false,     
	}
```
#### signmessage

> Method description

Sign the message based on the private key

> Call command

```bash
	signmessage "address|privkey" "message"
```

> Method parameter
> |parameter		|description			|
> |--			|--				|
> |address|privkey |address or private key, if it is an address, you must ensure that the private key of the address is also on the node |
> |message |Message to be signed |


> e.g.

```bash
	signmessage "addr" "my message"
```

> return value

```bash
  sign-str
```

#### verifymessage

> Method description

verification message

> Call command

```bash
	verifymessage "publickey" "signature" "message"
```

> Method parameter

|parameter	|description	|
|--	|--	|
|publickey |public key |
|signature |signed string |
|message | Messages before signing |


> return value

```bash
			true|false
```

#### combineunspent

> Method description

Combine the unused output (UTXO) into an unused output, returning a list of TXIDs that improve the performance of the wallet

> Call command

```bash
	combineunspent ( "address(es)" minconf maxcombines mininputs maxinputs maxtime )
```
> Method parameter

|parameter	|description	|
|--	|--	|
|address(es) | defaults to *, the address or address set to be aggregated |
|minconf | defaults to 1, the minimum confirmed quantity |
|maxcombines | defaults to 100, the maximum number of transactions in the collection |
|mininputs | default is 2, minimum input quantity |
|maxinputs | default is 100, the maximum number of inputs |
|maxtime | default is 15s, maximum broadcast time |


> e.g.

```bash
	combineunspent "*" 1 15 2 10 100
	combineunspent "addr"
```

> return value

```bash
	txids
```
#### listunspent

> Method description

Query address does not cost output

> Call command

```bash
	listunspent ( minconf maxconf addresses )
```

> Method parameter

|parameter		|description								|
|--			|--									|
|minconf | Block Minimum Confirmation Number |
|maxconf | Block maximum acknowledgment number |
|addresses |The address set to be queried |


> e.g.

```bash
	listunspent 6 9999999 "[\"address1\",\"address2\"]"
```

> return value

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

#### listlockunspent

> Method description

Returns a list of unused transaction output locks in the wallet

> Call command

```bash
	listlockunspent
```
> Method parameter

none


> return value

```bash
	[
	  {
	   (string) "txid" : "TXID",              
	   (numeric) "vout" : n                    
	  }
	  ,...
	]
```
#### syncstop

> Method description

Pause blockchain

> Call command

```bash
	syncstop (task(s))
```

> Method parameter

|parameter	|description												|
|--		|--													|
|task(s)|The service to be stopped, the possible values are incoming,mining|

> e.g.

```bash
	syncstop incoming,mining
```

> return value

none

#### syncstart

> Method description

Restore the blockchain and use it with pause

> Call command

```bash
	syncstart (task(s))
```

> Method parameter

|parameter	|description												|
|--		|--													|
|task(s)|The service to be restarted, the possible values are incoming,mining|

> e.g.

```bash
	syncstart incoming,mining
```

> return value

none

#### getmempoolinfo

> Method description

Return memory pool information

> Call command

```bash
	getmempoolinfo
```

> Method parameter

none

> return value

```bash
	{
		"size" : 0,
		"bytes" : 0
	}
```

#### addnode

> Method description

Manually add or remove peer-to-peer connections

> Call command

```bash
	addnode "node" "add"|"remove"|"onetry"
```

> Method parameter
> |parameter	|description	|
> |--	|--	|
> |node |node |
> |command |add joins a node to a connection collection; remove removes a node from a collection; onetry attempts to connect to a node test |
> e.g.

```bash
	addnode "ip:port" "add"
```

#### getaddednodeinfo

> Method description

Query connection node

> Call command

```bash
	getaddednodeinfo  ( "node" )
```

> Method parameter
> |parameter	|description	|
> |--	|--	|
> |(boolean)true/false|true=returns information about adding nodes using addnode, false=returns the list of nodes added using addnode |
> |node | Nodes that include or omit ip(:port) |

> e.g.

```bash
	getaddednodeinfo true
	getaddednodeinfo true "ip"
```

> return value

```bash
	[
		{
			"addednode": "xx.xxx.xxx.xx:xxxx", 
			"connected": true, 
			"addresses": 
				[
					{
						"address": "xx.xxx.xxx.xx:xxxx", 
						"connected": "outbound"
					}
				]
		}
	]
```

#### getnetworkinfo

> Method description

Return the connected network IP and port information

> Call command

```bash
	getnetworkinfo
```

> Method parameter

none


> return value

```bash
	{
  "version": xxxxx,                        
  "subversion": "/x.x.x/",           
  "ProVer": xxxxx,                         
  "localservices": "xxxxxxxxxxxxxxxx",     
  "timeoffset": xxxxx,                     
  "connections": xxxxx,                   
  "networks": [                           
  {
    "name": "xxx",                        
    "limited": true|false,              
    "reachable": true|false,               
    "proxy": "host:port"                 
  }
  ,...
  ],
  "relayfee": x.xxxxxxxx,                 
  "localaddresses": [                      
  {
    "address": "xxxx",                   
    "port": xxx,                           
    "score": xxx                          
  }
  ,...
  ]
}
```

#### getpeerinfo

> Method description

Return information about other nodes connected to

> Call command

```bash
	getpeerinfo
```

> Method parameter

none

> return value

```bash
	[
	  {
		"id": n,                           
		"addr":"host:port",              
		"addrlocal":"ip:port",            
		"services":"xxxxxxxxxxxxxxxx",    
		"lastsend": ttt,                   
		"lastrecv": ttt,                  
		"bytessent": n,                    
		"bytesrecv": n,                    
		"conntime": ttt,                 
		"pingtime": n,                    
		"pingwait": n,                    
		"version": v,                    
		"subver": "/x.x.x/",      
		"handlocal": n,               
		"handshake": n,                    
		"inbound": true|false,             
		"startingheight": n,               
		"banscore": n,                     
		"synced_headers": n,               
		"synced_blocks": n,                
		"inflight": [
		   n,                           
		   ...
		]
	  }
	  ,...
	]
```

#### getsyncinfo

> Method description

Display sync block information

> Call command

```bash
	getsyncinfo
```

> Method parameter

none

> return value

```bash
	{
		"blocks" : 12,
		"headers" : 12,
		"bestblockhash" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
	}
```

### Raw  transaction

#### lockunspent

> Method description

Lock or unlock unspent output

> Call command

```bash
	lockunspent unlock [{"txid":"txid","vout":n},...]
```

> Method parameter

|parameter	|description				|
|--		|--					|
|unlock	|False lock, true unlock|
|transactions	|The transaction to lock or unlock	|


> e.g.

```bash
	lockunspent false "[{\"txid\":\"txid\",\"vout\":1}]"
	lockunspent true "[{\"txid\":\"txid\",\"vout\":1}]"
```

> return value

```bash
	true or false
```

#### createrawtransaction

> Method description

Create a transaction that takes the specified input and send it to the given address

> Call command

```bash
	createrawtransaction [{"txid":"id","vout":n},...] {"address":amount,...} ( [data] "action" )
```

> Method parameter

|parameter	|description	|
|--	|--	|
|transactions	|Transaction collection	|
|addresses	|Address: Quantity	|
|data	|Additional data	|
|action	|The default is "", lock (locks the given input in the wallet), sign (signs the transaction using the wallet key), lock, sign, send (sign and send the transaction)	|



> e.g.

```bash
	createrawtransaction "[{\"txid\":\"myid\",\"vout\":0}]" "{\"address\":0.01}"
```

> return value

```bash
	
	hex
	or
	{                                                        
	  "hex": "value",                                      
	  "complete": true|false                                 
	}
```

#### getrawtransaction

> Method description

Query transaction information.

> Call command

```bash
	getrawtransaction "txid" 
```

> Method parameter

|parameter			|description								|
|--				|--									|
|txid			|txid								|

> e.g.

```bash
	getrawtransaction "txid"
```

> return value

```bash
	hex
```

#### createrawsendtransaction

> Method description

Create a transaction with the specified address

> Call command

```bash
	createrawsendtransaction "from-address" {"address":amount,...} ( [data] "action" )
```

> Method parameter

|parameter		|description																										|
|--			|--																											|
|from-addr	|address																									|
|addresses	|Address: Quantity																									|
|data		|data																						|
|action		|lock，sign，lock,sign，send	|

> e.g.

```bash
	createrawsendtransaction "FROMADDR" "{\"ADDR\":0.01}"
```

> return value

```bash
	hex
	or
	{                                                        
	  "hex": "value",                                       
	  "complete": true|false                                 
	}
```

#### decoderawtransaction

> Method description

Decoding transaction TX-HEX

> Call command

```bash
	decoderawtransaction "tx-hex"
```

> Method parameter

|parameter	|description	|
|--	|--	|
|tx-hex	|hex	|


> return value

```bash
	{
	  "txid" : "id",                  
	  "version" : n,                   
	  "locktime" : ttt,                
	  "vin" : [
		 {
		   "txid": "id",              
		   "vout": n,                
		   "scriptSig": {             
			 "asm": "asm",            
			 "hex": "hex"            
		   },
		   "sequence": n              
		 }
		 ,...
	  ],
	  "vout" : [
		 {
		   "value" : x.xxx,           
		   "n" : n,                    
		   "scriptPubKey" : {
			 "asm" : "asm",            
			 "hex" : "hex",            
			 "reqSigs" : n,            
			 "type" : "pubkeyhash",    
			 "addresses" : [
			   "address"  address
			   ,...
			 ]
		   }
		 }
		 ,...
	  ],
	}
```

#### addrawtransaction

> Method description

Add a given input and (regular or metadata) output to the specified original transaction TX-HEX instead of creating a new transaction

> Call command

```bash
	addrawtransaction "tx-hex" [{"txid":"id","vout":n},...] ( {"address":amount,...} [data] "action" )
```

> Method parameter

|parameter			|description																									|
|--				|--																										|
|tx-hex			|hex																						|
|transactions	|transactions		|
|addresses		|Asset: quantity												|
|data			|data																							|
|action			|The default is "", lock (locks the given input in the wallet), sign (signs the transaction using the wallet key), lock, sign, send (sign and send the transaction)	|

> e.g.

```bash
	addrawtransaction "hexstring" "[{\"txid\":\"myid\",\"vout\":0}]" "{\"address\":0.01}"
```

> return value

```bash
	hex
```

#### fundrawtransaction

> Method description

Accept the output of createrawtransaction. Specify change address and handling fee

> Call command

```bash
	fundrawtransaction "tx-hex" "address" ( fee )
```

> Method parameter

|parameter	|description		|
|--		|--			|
|tx-hex	|hex		|
|address|Change address	|
|fee	|fee		|

> e.g.

```bash
	fundrawtransaction "HEX""ADDR"
	fundrawtransaction "HEX""ADDR" 0.01
```

> return value

```bash
	hex
```

#### recordrawtransaction

> Method description

Add a data to the original transaction output TX-HEX of createrawtransaction.

> Call command

```bash
	recordrawtransaction tx-hex data
```

> Method parameter

|parameter	|description	|
|--	|--	|
|tx-hex	|hex	|
|data	|data |


> e.g.

```bash
	recordrawtransaction "TX-HEX" data-hex
```

> return value

```bash
	hex
```

#### signrawtransactionwithkey

> Method description

Sign the original transaction

> Call command

```bash
	signrawtransactionwithkey "tx-hex" ( [{"txid":"id","vout":n,"scriptPubKey":"hex","redeemScript":"hex"},...] ["privatekey1",...] sighashtype )
```

> Method parameter

|parameter	|description	|
|--	|--	|
|tx-hex	|hex	|
|prevtxs	|Trading array  |
|privatekeys	|Private key array	|
|sighashtype	|Signature type, default is ALL|

> e.g.

```bash
	signrawtransactionwithkey "HEX-STR"
```

> return value

```bash
	{
	  "hex": "value",                          
	  "complete": true|false                    
	}
```

#### sendrawtransaction

> Method description

Conduct a broadcast transaction.

> Call command

```bash
	sendrawtransaction "tx-hex" ( allowhighfees )
```

> Method parameter

|parameter			|description								|
|--				|--									|
|tx-hex			|tx-hex								|
|allowhighfees	|The default is false, whether higher fees are allowed	|


> e.g.

```bash
	sendrawtransaction "signedhex"
```

> return value

```bash
	txid
```

#### rollbacktransaction

> Method description

Disable TX-HEX

> Call command

```bash
	rollbacktransaction "tx-hex"
```

> Method parameter

|parameter	|description	|
|--		|--		|
|tx-hex	|hex	|


> return value

```bash
	txid
```

# 3.Example

## 3.1 Regular transaction example
VAS uses the UTXO model as the data structure for the underlying storage, which is called Unspent Transaction output, which is the unused transaction output. The balance in the account is not represented by a number, but by all UTXOs associated with the current account in the current blockchain network.
### 3.1.1Create multiple new addresses
```bash
	getnewaddress
	addr1
	getnewaddress
	addr2
	getnewaddress
	addr3
```
### 3.1.2 Send VAS
```bash
	send addr1 10
	txid1
	sendfrom addr1 addr2
	txid2
```
### 3.1.3 Send token
```bash
	send addr1 '{"BBB":10}'
	txid1
	sendfrom addr1 addr2 '{"BBB":10}'
	txid2
```
### 3.1.4 Send multiple assets
```bash
	send addr1 '{"BBB":10,"AAA":20,"":10}'
	txid1
	sendfrom addr1 addr2 '{"BBB":10,"AAA":20,"":10}'
	txid2
```
## 3.2 Multi-signed account transactions and samples

### 3.2.1 Multi-sign account introduction

VAS uses the "x/y" multi-signal address, defined as: Given y regular addresses, at least the private key corresponding to x addresses must sign the transaction at the same time to execute the transaction or transaction.

### 3.2.2 Create a multi-sign account address

* Find the address and find the address addr1, addr2
- Create a condominium account Nodes run the following command to create a 2/2 multiple address and add it to the node's wallet:
```bash
	addmultisigaddress 2 '["A1-publickey", "A2-publickey"]'
```
Get the multi-signal address MA1A2, and automatically turn on the tracking node two repeatable node one command
```bash
	addmultisigaddress 2 '["A1-publickey", "A2-publickey"]'
```
### 3.2.3 Trading through multiple signings

Send funds through a multi-signed account. Since this is a 2/2 co-management contract, this process will require a common signature of both addresses.
* Node 1: Create a transaction and partially sign it
```bash
	createrawsendtransaction MA1A2 '{"XXXXXXXXXXXXXXXXXXXX ":{"AAA":500}}' '[]' sign
  {
      "hex" : "XXXXXXXXXXXXXXXXXXXXXXXX",
      "complete" : false
  }
```
Response complete: false, hexadecimal hex field HEX1. Partially signed.
* Node 2 signature:
```bash
	  signrawtransactionwithkey HEX1
  
	  {
		  "hex" : "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
		  "complete" : true
	  }
```
Response complete: true, hexadecimal hex field HEX2. This means that the transaction signature is complete and can be broadcast to the blockchain.
* Broadcast transaction
```bash
	sendrawtransaction HEX2
```

## JSON RPC configuration

The RPC username and password are stored in the ~/.vaschain/vasc/vaschain.conf(linux/MAC) or %APPDATA%\vaschain\vasc\vaschain.conf(Windows) file. You can use the vashian-cli command line tool or the vaschain GUI. The built-in CLI interface of the tool (PC wallet), these tools will automatically read the RPC username and password and connect to the running blockchain, please do not open the rpc port to the external network.

> Command mode operation：

```bash
	[command] [params]
```
