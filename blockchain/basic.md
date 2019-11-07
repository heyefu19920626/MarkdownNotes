#### 基本原理

如果把区块链作为一个状态机，则每次交易就是试图改变一次状态，而每次共识生成
的区块，就是参与者对于区块中交易导致状态改变的结果进行确认。

- 基本概念
    + 交易（transaction ）：一次对账本的操作，导致账本状态的 次改变，如添加一条转账记录
    + 区块（ block ）：记录一段时间内发生的所有交易和状态结果，是对当前账本状态的一次共识
    + 链（chain ）：由区块按照发生顺序串联而成，是整个账本状态变化的日志记录

#### peer0.org1.example.com节点

- cli 与 orderer 之间的通讯使用 tls 加密,设置环境变量 ORDERER_CA 以作建立握手的凭证

```
ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
```

- 创建名为 mychannel 的 channel

```
peer channel create -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/channel.tx --tls true --cafile $ORDERER_CA 

channel 创建成功后，会在当前目录下生成mychannel.block文件。
每个peer 在向 orderer 发送 join channel 交易的时候，
需要提供这个文件才能加入到 mychannel 中
```

- 把 peer0.org1.example.com 加入到 mychannel 中

```
peer channel join -b mychannel.block
```

- 更新锚节点,对于Org1来说，peer0.org1是锚节点，我们需要连接上它并更新锚节点, mychannel 中 org1 的 anchor peer 的信息

```
peer channel update -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile $ORDERER_CA
或者
peer channel update -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org1MSPanchors.tx --tls true --cafile $ORDERER_CA
```

- 链上代码的安装与运行:安装 chaincode 示例 chaincode_example02 到 peer0.org1.example.com 中

```
peer chaincode install -nmycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```

#### peer1.org1.example.com 节点

- 把 peer1.org1.example.com 加入到 mychannel 中

```
peer channel join -b mychannel.block
```

- 安装 chaincode_example02 到 peer1.org1.example.com 中

```
peer chaincode install -nmycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```

#### org2 的第一个节点 peer2,peer0.org2.example.com节点

- 把peer0.org2.example.com加入到mychannel中

```
peer channel join -b mychannel.block
```

- 更新 mychannel 中 org2 的 anchor peer 的信息

```
# 注意组织的不同
peer channel update -oorderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org2MSPanchors.tx --tls --cafile $ORDERER_CA
```

- 安装 chaincode_example02 到 peer0.org2.example.com

```
peer chaincode install -nmycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```

#### 启动org2的第二个节点 peer3 ，即启动 peer1.org2.example.com

- 把 peer1.org2.example.com 加入到 mychannel 中

```
peer channel join -b mychannel.block
```

- 安装 chaincode_example02 到 peer1.org2.example.com

```
peer chaincode install -n mycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```


#### 运行chaincode

- 实例化chaincode

```
peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile $ORDERER_CA -C mychannel-nmycc -v 1.0 -c '{"Args":["init","a","100","b","200"]}' -P "OR ('Org1MSP.member','Org2MSP.member')"
或者
peer chaincode instantiate -o orderer.example.com:7050 --tls true --cafile $ORDERER_CA -C mychannel -n mycc -v 1.0 -c '{"Args":["init","a","100","b","200"]}' -P "OR      ('Org1MSP.member','Org2MSP.member')"
```

- 在peer0.org1.example.com上查看chaincode的状态，登录到VM1上并进入cli容器内部执行：

```
peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
```

- 转账,把a账户的10元转给b
```
ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

peer chaincode invoke -o orderer.example.com:7050  --tls true --cafile $ORDERER_CA -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'
```
