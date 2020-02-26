# Fabric

- [Fabric](#fabric)
    - [打包](#打包)
    - [安装，部署合约到节点](#安装部署合约到节点)
    - [当前组织同意合约定义](#当前组织同意合约定义)
    - [操作合约](#操作合约)
    - [合约升级](#合约升级)
  - [开发者模式](#开发者模式)
    - [查看docker容器日志](#查看docker容器日志)
  - [钱包](#钱包)
    - [一些变量名称](#一些变量名称)
    - [查询](#查询)
    - [节点环境变量](#节点环境变量)
    - [调用](#调用)
- [org3 docker compose file](#org3-docker-compose-file)
- [two additional etcd/raft orderers](#two-additional-etcdraft-orderers)
- [certificate authorities compose file](#certificate-authorities-compose-file)
  - [问题](#问题)


[证书配置](https://blog.csdn.net/qq_28540443/article/details/104282768)  
generateCerts ：生成证书
> cryptogen generate --config=./crypto-config.yaml  实现根据crypto-config.yaml 生成证书文件  
> ./ccp-generate.sh 生成调用nodejs SDK的相关区块链配置文件  

crypto-config.yaml主要包含的fabric排序节点证书配置以及fabric组织证书配置

generateChannelArtifacts ：生成创始区块与通道文件, 二选一, 在外部创建通道*文件*
>  configtxgen -profile SampleMultiNodeEtcdRaft -channelID mychannel -outputBlock ./channel-artifacts/genesis.block  
>  configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel2.tx -channelID channel2

- `-outputCreateChannelTx`： 输出tx文件路径
- `-channelID`： 通道ID

在cli中创建通道
>  peer channel create -o orderer.example.com:7050 -c channel2 -f ./channel-artifacts/channel2.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

- -o 参数排序节点服务域名端口
- -c 通道ID
- -f 通道文件所在路径
- -tls 是否启用tls
- -cafile ca路径

节点加入通道
> peer channel join -b channel2.block  

- -b 通道文件路径

查看当前节点所加入的通道
> peer channel list  

智能合约操作合集
```
#智能合约操作合集
Perform chaincode operations: package|install|queryinstalled|getinstalledpackage|approveformyorg|checkcommitreadiness|commit|querycommitted

Usage:
  peer lifecycle chaincode [command]

Available Commands:
  approveformyorg      同意智能合约定义
  checkcommitreadiness  检查合约是否在通道上已经定义
  commit               提交合约定义到指定通道
  getinstalledpackage  从节点中获取已经安装的链码包
  install              部署链码
  package           打包链码
  querycommitted       查询节点上已提交的链码定义
  queryinstalled       查询节点已经安装的链码
```

### 打包
> peer lifecycle chaincode package game.tar.gz --path github.com/hyperledger/fabric-samples/chaincode/game/ --lang golang --label game_1
- game.tar.gz ：打包合约包文件名 
- --path 智能合约路径
- --lang 智能合约语言 支持golang、node、java
- --label  智能合约标签，描述作用

打包后的文件主要为
1. chaincode-Package-Metadata.json文件，主要是定义合约的代码路径、合约语言以及合约标签`{"path":"github.com/hyperledger/fabric-samples/chaincode/game/","type":"golang","label":"game_1"}`
2. 压缩的合约代码文件code.tar.gz,包含合约代码以及依赖包

### 安装，部署合约到节点
> peer lifecycle chaincode install game.tar.gz
- game.tar.gz： 智能合约压缩包路径

1. 查询该节点已安装的合约
> peer lifecycle chaincode queryinstalled

1. Name	合约名称	mycc
2. Version	合约版本	1
3. Sequence	合约序列号(升级1次，加1)	1
4. Endorsement Plugin	背书插件	escc
5. Validation Plugin	校验插件	vsc

### 当前组织同意合约定义

2.0智能合约由合约定义控制。当通道成员批准合约定义时，该批准作为组织对其接受的合约参数的投票。这些经过批准的组织定义允许通道成员在链码在通道上使用之前对链码达成一致。链码定义包括以下参数，这些参数需要在组织之间保持一致:

名称: 智能合约名称
版本: 与给定的合约包相关联的版本号或值。如果您升级了合约二进制文件，那么您也需要更改合约版本。
序列: 定义链码的次数。此值为整数，用于跟踪合约升级。例如，当您第一次安装并批准一个chaincode定义时，序列号将是1。下次升级合约时，序列号将增加到2。
背书策略: 哪些组织需要执行和验证事务输出。背书策略可以表示为传递给CLI或SDK的字符串，也可以在通道配置中引用策略。默认情况下，背书策略被设置为通道/应用程序/背书，这默认要求通道中的大多数组织对交易进行背书。
集合配置: 指向与您的chaincode关联的私有数据集合定义文件的路径。有关私有数据收集的更多信息，请参见私有数据体系结构参考。
初始化: 所有的链码需要包含一个初始化函数来初始化链码。默认情况下，这个函数永远不会执行。但是，您可以使用chaincode定义来请求Init函数是可调用的。如果请求执行Init，则fabric将确保在调用任何其他函数之前调用Init，并且只调用一次。
ESCC/VSCC插件: 自定义认证或验证插件的名称

1. 查询已投票的背书组织/检查合约定义是否满足策略
> peer lifecycle chaincode checkcommitreadiness --channelID mychannel --name game --version 1 --sequence 1 --output json --init-required

2. 投票,修改合约定义
> peer lifecycle chaincode approveformyorg --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --channelID mychannel --name game --version 1 --init-required --package-id game_1:d637af903cdf60aa0ba77a025e4c4660f5ab94a183b98b4cbf20152d10acd8a9 --sequence 1 --waitForEvent

- --tls 是否启动tls
- --ca ca证书路径
- --channelID 智能合约安装通道
- --name 合约名
- --version 合约版本
- --package-id queryinstalled查询的合约ID
- --sequence 序列号
- --waitForEvent 等待peer提交交易返回
- --init-required 合约是否必须执行init

3. 提交合约定义
> peer lifecycle chaincode commit -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --channelID mychannel --name game --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt --version 1 --sequence 1 --init-required

- --tls 是否启动tls
- --ca ca证书路径
- --channelID 智能合约安装通道
- --name 合约名
- --version 合约版本
- --package-id queryinstalled查询的合约ID
- --sequence 序列号
- --waitForEvent 等待peer提交交易返回
- --init-required 合约是否必须执行init
- --peerAddresses 节点路径 
- --tlsRootCertFiles 节点ca根证书路径(--peerAddresses --tlsRootCertFiles  连用，可多个节点，多个节点即将合约部署到对应节点集合上)

4. 查看节点已提交合约
>  peer lifecycle chaincode querycommitted --channelID mychannel --name game

### 操作合约

1. 初始化
> peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt --isInit -c '{"Args":["Init"]}'

2. 调用
> peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["addDuration","1","3600","1582002171"]}'

3. 查询
> peer chaincode query -C mychannel -n game -c '{"Args":["get","1"]}'

### 合约升级

需要重新打包，安装，定义，提交

## 开发者模式
docker exec -it chaincode sh
cd game
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
go build
CORE_CHAINCODE_ID_NAME=mycc:0 CORE_PEER_TLS_ENABLED=false ./game -peer.address peer:7052


docker exec -it cli bash
peer chaincode install -p chaincodedev/chaincode/game -n mycc -v 0
peer chaincode instantiate -n mycc -v 0 -c '{"Args":["a","10"]}' -C myc
peer chaincode invoke -n mycc -c '{"Args":["addMoney", "1", "2000", "1582012809"]}' -C myc
peer chaincode invoke -n mycc -c '{"Args":["addDuration", "1", "2000", "1582012809"]}' -C myc
peer chaincode query -n mycc -c '{"Args":["get","1"]}' -C myc

### 查看docker容器日志
docker logs -f peer0.org1.example.com --tail=300


## 钱包

### 一些变量名称

```
CONFIG_ROOT=/opt/gopath/src/github.com/hyperledger/fabric/peer
ORDERER_TLS_ROOTCERT_FILE=${CONFIG_ROOT}/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
ORG1_TLS_ROOTCERT_FILE=${CONFIG_ROOT}/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

ORDERER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
ORG1_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
```

### 查询

```
peer chaincode query --tls=true --cafile=${ORDERER_TLS_ROOTCERT_FILE} --orderer=orderer.example.com:7050 -C mychannel -n game  -c '{"function":"QueryPlayer","Args":["1"]}' --peerAddresses peer1.org1.example.com:8051 --tlsRootCertFiles ${ORG1_TLS_ROOTCERT_FILE} 

peer chaincode query --tls=true --cafile=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --orderer=orderer.example.com:7050 -C mychannel  --peerAddresses peer1.org1.example.com:8051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt -n game  -c '{"function":"QueryPlayer","Args":["1"]}'
```

### 节点环境变量
```
CORE_PEER_LOCALMSPID=Org1MSP
CORE_PEER_ADDRESS=peer0.org1.example.com:7051
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt cli 
```

### 调用

1. 增加已消费时长
```
peer --tls=true --cafile=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --orderer=orderer.example.com:7050 chaincode invoke -C mychannel -n game --waitForEvent --waitForEventTimeout 300s --peerAddresses peer0.org1.example.com:7051 --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"function":"addPlayTime","Args":["1","3600","1582002171"]}'
```

COMPOSE_FILES = -f docker-compose-cli.yaml -f docker-compose-etcdraft2.yaml -f docker-compose-ca.yaml -f docker-compose-couch.yaml 

COMPOSE_FILE=docker-compose-cli.yaml
#
COMPOSE_FILE_COUCH=docker-compose-couch.yaml
# org3 docker compose file
COMPOSE_FILE_ORG3=docker-compose-org3.yaml
# two additional etcd/raft orderers
COMPOSE_FILE_RAFT2=docker-compose-etcdraft2.yaml
# certificate authorities compose file
COMPOSE_FILE_CA=docker-compose-ca.yaml

fi
  COMPOSE_FILES="-f ${COMPOSE_FILE} -f ${COMPOSE_FILE_RAFT2}"
  if [ "${CERTIFICATE_AUTHORITIES}" == "true" ]; then
    COMPOSE_FILES="${COMPOSE_FILES} -f ${COMPOSE_FILE_CA}"
    export BYFN_CA1_PRIVATE_KEY=$(cd crypto-config/peerOrganizations/org1.example.com/ca && ls *_sk)
    export BYFN_CA2_PRIVATE_KEY=$(cd crypto-config/peerOrganizations/org2.example.com/ca && ls *_sk)
  fi
  if [ "${IF_COUCHDB}" == "couchdb" ]; then
    COMPOSE_FILES="${COMPOSE_FILES} -f ${COMPOSE_FILE_COUCH}"
  fi
  IMAGE_TAG=$IMAGETAG docker-compose ${COMPOSE_FILES} up -d 2>&1


peer lifecycle chaincode approveformyorg --orderer=orderer.example.com:7050 --package-id fabcarv1:0b7e2b81902727c3eb9bda027b5c635934ff4676eaf65e9f23287cb6aea8f33f --channelID mychannel --name fabcar --version 1.0 --signature-policy "AND('Org1MSP.member','Org2MSP.member')" --sequence 1 --waitForEvent

## 问题

1. go下载需翻墙问题  
```
Error: failed to normalize chaincode path: 'go list' failed with: go: github.com/hyperledger/fabric-contract-api-go@v1.0.0: Get https://proxy.golang.org/github.com/hyperledger/fabric-contract-api-go/@v/v1.0.0.mod: dial tcp 216.58.200.241:443: i/o timeout: exit status 1
!!!!!!!!!!!!!!! Chaincode packaging on peer0.org1 has failed !!!!!!!!!!!!!!!!
```
   
   使用七牛云 go module 镜像
```
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```
