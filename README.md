### 一、 基本说明

1.0 区块确认完成

通过事务的哈希值查询确认区块数，并且确认是否已经完成，
我们认为往后确定2区块即可表示已经完成。
无论什么事务，都要等待至少2个区块确认才算完成

1.1 返回格式
```
{"message":"","data":[],"statusCode":int}
* message：描述
* data   ：数据
* statusCode：      
   
{
    2000 正确
    2100 已确认
    2200 未确认
    5000 错误
    6000 格式错误
    7000 校验错误
    8000 异常
}
```
1.2 事务类型



|编号             |交易类型               |说明型               |
|:-----------------------:|:-----------------------:|:-----------------------:|
|0                |0x00                   |coinbase事务         |
|1                |0x01                   |WDC转账              |
|2                |0x02                   |投票                 |
|3                |0x03                   |存证事务             |
|4                |0x04                   |多重签名事务-多签到多签         |
|5                |0x05                   |多重签名事务-多签到普通         |
|6                |0x06                   |多重签名事务-普通到多签         |
|7                |0x07                   |资产定义             |
|8                |0x08                   |原子交换             |
|9                |0x09                   |申请孵化             |
|10                |0x0a                   |获取利息收益        |
|11                |0x0b                   |获取分享收益        |
|12                |0x0c                   |提取本金            |

### 二、节点RPC

##### 1.0 获取Nonce
```
Function:sendNonce
POST/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/sendNonce
Parameter：pubkeyhash(String)
Demo:
    POST http://00.000.00.000:19585/sendNonce
    POST data:
        pubkeyhash=0000000000000000000000000000000000000000
Response Body:
    {"message":"","data":[],"statusCode":int}
	data:Nonce(Long)
```
##### 1.1 获取余额

```
Function:sendBalance
POST/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/sendBalance
Parameter：pubkeyhash(十六进制字符串)
Demo:
    POST http://00.000.00.000:19585/sendBalance
    POST data:
        pubkeyhash=0000000000000000000000000000000000000000
Response Body:
    {"message":"","data":[],"statusCode":int}
	data:balance(Long)
```

##### 1.2 广播事务

```
Function:sendTransaction
POST/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/sendTransaction
Parameter：traninfo(十六进制字符串)
Demo:
    POST http://00.000.00.000:19585/sendTransaction
    POST data:
    traninfo=000000000000000000000000000000000000000000000000000000000000000000
Response Body:
    {"message":"","data":[],"statusCode":int}
```

##### 1.3 查询当前区块高度

```
Function:height
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/height
Parameter:
Demo:
    GET http://00.000.00.000:19585/height
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:height(Long)
```

##### 1.4 根据事务哈希获得所在区块哈希以及高度

```
Function:blockHash
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/blockHash
Parameter：traninfo(十六进制字符串)
Demo:
    GET http://00.000.00.000:19585/blockHash?txHash=0000000000000000000000000000000000000000000000000000
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:
        {
        "blockHash":区块哈希(十六进制字符串), 
        "height":区块高度(Long)
        }
```
##### 1.5 根据事务哈希获得区块确认状态 
```
Function:transactionConfirmed
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/transactionConfirmed
Parameter：txHash(十六进制字符串)
Demo:
    GET http://00.000.00.000:19585/transactionConfirmed?txHash=0000000000000000000000000000000000000000
Response Body:
    {"message":"","data":[],"statusCode":int}
    data: status(int)
```
##### 1.6 根据区块高度获取事务列表

```
Function:getTransactionHeight
POST/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/getTransactionHeight
Parameter：height(十六进制字符串)
Demo:
    POST http://00.000.00.000:19585/getTransactionHeight
    POST data:
        height=100
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:
        String block_hash; 区块哈希16进制字符串
    	long height; 区块高度
    	int version; 版本号
    	String tx_hash; 事务哈希16进制字符串
    	int type;  事务类型
    	long nonce;nonce
    	String from;  发起者公钥16进制字符串
    	long gas_price; 事务手续费单价
    	long amount; 金额
    	String payload; payload数据(存证事务：UTF-8编码、其余事务：十六进制字符串) 
    	String signature; 签名16进制字符串
    	String to;  接受者公钥哈希16进制字符串
```
##### 1.7 通过事务哈希获取事务

```
Function:transaction
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/transaction
Parameter：路径变量,直接传入事务哈希
Demo:
        GET http://00.000.00.000:19585/transaction/0000000000000000000000000000000000000000000000000000000000000
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:
      "transactionHash": "e75d61e1b872f67cccc37c4a5b354d21dd90a20f04a41a8536b9b6a1b30ccf41", // 事务哈希
      "version": 1, // 事务版本 默认为 0
      "type": 0,  // 事务类型 0 是 coinbase 1 是 转账
      "nonce": 5916, // nonce 值，用于防止重放攻击
      "from": "0000000000000000000000000000000000000000000000000000000000000000", // 发送者的公钥， 用于验证签名
      "gasPrice": 0, // gasPrice 用于计算手续费
      "amount": 2000000000, // 交易数量，单位是 brain
      "payload": null, // payload 用于数据存证，一般填null(存证事务：UTF-8编码，其余事务：十六进制字符串) 
      "to": "08f74cb61f41f692011a5e66e3c038969eb0ec75", // 接收者的地址
      "signature": "00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000", // 签名
      "blockHash": "e2ccac56f58adb3f2f77edd96645931fac93dd058e7da21421d95f2ac9cc44ac", // 事务所在区块的哈希
      "fee": 0,  // 手续费
      "blockHeight": 13026 // 事务所在区块高度
```
##### 1.8 通过区块哈希获取事务列表

```
Function:getTransactionBlcok
POST/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/getTransactionBlcok
Parameter：blockhash(十六进制字符串)
Demo:
    POST http://00.000.0.000:19585/getTransactionBlcok
    POST data:
        blockhash=00000000000000000000000000000000000000000000000000000000000000000
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:
        String block_hash; 区块哈希16进制字符串
        long height; 区块高度
        int version; 版本号
        String tx_hash; 事务哈希16进制字符串
        int type;  事务类型
        long nonce;nonce
        String from;  发起者公钥16进制字符串
        long gas_price; 事务手续费单价
        long amount; 金额
        String payload; payload数据(存证事务：UTF-8编码，其余事务：十六进制字符串) 
        String signature; 签名16进制字符串
        String to;  接受者公钥哈希16进制字符串
```

##### 1.9 通过地址查询与地址相关的转入转出事务
```
Function:getTxrecordFromAddress
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/getTxrecordFromAddress
Parameter：address
Demo:
    GET http://000.000.00.000:19585/getTxrecordFromAddress?address=1xxxxxxxxxxxxxxxxxx
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:
        "tx_hash":"000000000000000000000000000000000000000000000000000000000000",//事务哈希
        "amount":100000,//金额
        "height":100,//区块高度
        "from":"1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",//发起者地址
        "to":"1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",//接收者者地址
        "block_hash":"f672350016ca08243c27b28824ec0b4eb2cc21014db9e7461a4bc7fe4f823e95",//区块哈希
        "datetime":"2019-07-24 00:29:15",//区块时间
        "type":"-"//"-" 是 转出 ，"+" 是 转入
```
1.10 获取节点的信息
```
Function:status
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/peers/status
Parameter：address
Demo:
    GET http://000.000.00.000:19585/peers/status
Response Body:
    {
    "message": "SUCCESS",
    "data": {
        "trusted": [],
        "bootstraps": [
            "wisdom://000000000000000000000000000000000000000000000000000000000000000@000.000.00.000:9585"
        ],//自己节点的信任节点
        "blockList": [],
        "peers": [
            {
                "wisdom://0000000000000000000000000000000000000000000000000000000000000000@000.00.00.000:9585": 64 //邻居节点1的私钥@节点：节点得分
            },
            {
                "wisdom://0000000000000000000000000000000000000000000000000000000000000000@000.00.00.000:9585": 63 ////邻居节点2的私钥@节点：节点得分
            },
            {
                "wisdom://0000000000000000000000000000000000000000000000000000000000000000@000.00.00.000:9585": 63 ////邻居节点3的私钥@节点：节点得分
            },
            ......
        ],
        "self": "wisdom://0000000000000000000000000000000000000000000000000000000000000000@000.000.0.00:9585",//自己的私钥@节点
        "p2pMode": "grpc",//p2p模式
        "maxBlocksPerTransfer": 2048,//最大一次性传输的区块数量
        "allowFork": true,//是否允许分叉
        "enableDiscovery": true //是否开启节点发现
    },
    "code": 2000
```

##### 2.0 通过地址查询事务内存池
```
Function:getPoolAddress
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/getPoolAddress
Parameter：address
Demo:
    GET http://00.000.0.000:19585/getPoolAddress?address=1xxxxxxxxxxxxxxxxxx
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:
       "pool":"PendingTransPool",//内存池名称
       "tranhaxh":"000000000000000000000000000000000000000000000000000000000000000",//事务哈希
       "type":1,//事务类型(详见基本说明1.2)
       "nonce":40,//当前事务nonce
       "from":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,,0,0,0,0,0,0,0],//发送者公钥字节数组
       "fromhash":"0000000000000000000000000000000000000",//发送者公钥哈希
       "amount":100000000,//金额
       "to":"0000000000000000000000000000000",//接收者公钥哈希
       "state":0,//状态 0 是 待确认，1 是 已引用，2 是 已确认
       "datatime":"2019-07-24 09:32:43",//区块时间
       "height":0//区块高度
```
##### 2.1 获取节点版本信息
```
Function:version
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/version
Parameter:
Demo:
    GET http://00.000.0.000:19585/version
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:
         "character" 1. default：默认 2. exchange：交易所
         "version" 版本
```

##### 2.2 获取用户信息
```
Function:version
GET/HTTP/1.1/Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Request URL: http://00.000.0.000:19585/account/{account}
Parameter:公钥哈希/地址
Demo:
    GET http://00.000.0.000:19585/account/1CRXnUJx9Tq4ZpNkkueeKFxCbYg1E4uTCt
Response Body:
    {"message":"","data":[],"statusCode":int}
    data:
         {
          "publicKeyHash": "7d4d105a3fc6db71d35ed654b1b7aab73d8fa50d",//公钥哈希
          "address": "1CRXnUJx9Tq4ZpNkkueeKFxCbYg1E4uTCt",//地址
          "nonce": 1,//序号
          "balance": "1.0E9 WDC",//余额
          "incubateCost": "0.0 WDC",//孵化本金
          "mortgage": "0.0 WDC",//抵押数
          "votes": "0.0 WDC"//投票数（未衰减）
        }
```
