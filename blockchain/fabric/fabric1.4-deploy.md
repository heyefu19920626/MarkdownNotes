# Fabric 1.4 CA+CouchDb

- [Fabric 1.4 CA+CouchDb](#fabric-14-cacouchdb)
  - [基础及单机部署](#基础及单机部署)
  - [多机部署](#多机部署)
  - [修改所有服务器的host](#修改所有服务器的host)
  - [编写修改各个节点的docker-compose文件](#编写修改各个节点的docker-compose文件)
    - [Order节点与Peer节点的配置](#order节点与peer节点的配置)
    - [docker-compose-base的修改](#docker-compose-base的修改)
    - [couchDb的配置](#couchdb的配置)
  - [生成证书并同步到所有节点](#生成证书并同步到所有节点)
  - [CA的配置](#ca的配置)
  - [拷贝智能合约](#拷贝智能合约)
  - [启动脚本的修改](#启动脚本的修改)
  - [启动所有节点](#启动所有节点)
  - [执行scripts/script.sh初始化脚本](#执行scriptsscriptsh初始化脚本)
  - [安装智能合约](#安装智能合约)
  - [实例化智能合约](#实例化智能合约)
  - [智能合约的调用](#智能合约的调用)


由于防沉迷项目中采用的是couchDb，并使用CA认证机构，故编写此文档  
CA+couchDb的部署与1.4的基础多机部署并无太大区别


## 基础及单机部署

这次部署是基于Fabric1.4的多机部署与Fabric的fabcar示例，因此最好有Fabric1.4的多机部署经验与fabcar的部署经验

1. [Fabric1.4多机部署](http://192.168.3.10/hyperleger/doc/commit/8a705cfdc318b14c9bf87822a00d685561ca06e7)
2. fabcar单机部署
   1. fabcar是fabric-sample给出的一个示例，其中包含了一个排序(Order)节点，两个ca组织认证机构，四个普通节点(peer)，4couchdb数据库,并且包含了java，node的客户端，可以用来测试
   2. 启动fabcar
      1. 进入fabric-sample的fabcar目录
      2. 使用启动脚本即可启用`./startFabric.sh`

## 多机部署

本次多机部署仍然使用五台服务器，排序节点一台，普通节点四台(每个普通节点配一个couchDb数据库),ca认证机构与普通节点放在一起（ca1与组织1的0节点放在一起，ca2与组织2的0节点放在一起）
实验室环境为
```
192.168.11.101 peer0.org1.example.com #组织1的0节点
192.168.11.102 peer1.org1.example.com #组织1的1节点
192.168.11.103 peer0.org2.example.com #组织2的0节点
192.168.11.104 peer1.org2.example.com #组织2的1节点
192.168.11.105 orderer.example.com #排序节点
```
本文档所有操作都在first-network目录下操作，无特殊说明，均默认该目录  
在进行所有操作前，最好运行脚本`./byfn.sh down`来暂停移除所有旧容器，配置,智能合约镜像等文件，
并确认`docker ps -a`为空，`docker images`没有智能合约的镜像


## 修改所有服务器的host

1. `vim /etc/hosts`添加或修改以下内容,使各个节点的域名与ip对应，防止很多不必要的错误
```host
192.168.11.101 peer0.org1.example.com
192.168.11.102 peer1.org1.example.com
192.168.11.103 peer0.org2.example.com
192.168.11.104 peer1.org2.example.com
192.168.11.105 orderer.example.com
```
## 编写修改各个节点的docker-compose文件 

要分别编写Order节点,两个CA机构，四个普通节点，四个couchDb的docker-compose文件，其中Order节点与四个普通节点的编写修改方式与Fabric1.4的多机部署一样

### Order节点与Peer节点的配置

具体可以参照Fabric1.4的多机部署,这里只提几点注意事项,也可以参考[本项目的script/docker目录下的配置](http://192.168.3.10/system/product/FangChenMi/fcm-trial/fcm-block-client/tree/master/script/docker)
1. 每个peer几点都需要添加order节点的host属性
2. 每个组织的非锚节点，都需要添加锚节点的hsot属性
3. cli客户端需要添加order节点与四个peer节点的host属性
4. cli客户端需要注释掉commad命令，避免刚启动就关闭
5. cli客户端需要根据节点与组织修改对应组织，证书等属性

### docker-compose-base的修改

因为peer节点与order节点都继承自base/docker-compose-base.yaml文件，而base/docker-compose-base.yaml中的配置全部为单机部署的，故order节点与所有peer节点都需要修改该文件(也可以修改一次，拷贝四次),该文件的修改步骤与Fabric1.4的多机部署中相同，这里也只提几点注意事项
1. 修改该文件主要是修改该文件中的ports配置

### couchDb的配置

couchDb的配置与peer节点的配置大同小异,下面以修改peer1.org1.example.com对应的couchDb为例

1. 将docker-compose-couch.yaml复制一份，`cp docker-compose-couch.yaml docker-compose-couch1.yaml`
2. 删除docker-compose-couch1.yaml中的service下除了couchdb1与peer1.org1.example.com的所有配置
3. 修改couchdb1的ports,都修改为5984(本步骤应该并不必须)

其余节点的couchdb配置参照以上步骤，只保留与本店相对应的couchdb与peer节点

## 生成证书并同步到所有节点

通过以上的步骤，除了CA认证机构，所有配置已经完毕，先在order节点生成证书

1. 运行脚本`./byfn.sh generate`,该脚本会根据configtx.yaml文件生成证书(存储在crypto-config)与创世区块(在channel-artifacts目录)
2. 将生成的channel-artifacts与crypto-config目录及其下所有文件同步到其他所有节点的first-network目录下

## CA的配置

CA只需要在组织1和组织2的锚节点部署即可，即peer0.org1.example.com与peer0.org2.example.com节点

1. 在peer0.org1.example.com节点复制一份docker-compose-ca.yaml，`cp docker-compose-ca.yaml docker-compose-ca1.yaml`
2. 删除docker-compose-ca1.yaml中service下的除ca0以外的配置
3. 运行`echo $(cd crypto-config/peerOrganizations/org1.example.com/ca && ls *_sk)`命令，并将输出复制,准备第4步使用
4. 将docker-compose-ca1.yaml中的所有${BYFN_CA1_PRIVATE_KEY}替换为第3步的输出,并保存
5. 在peer0.org2.example.com节点复制一份docker-compose-ca.yaml，`cp docker-compose-ca.yaml docker-compose-ca2.yaml`
6. 删除docker-compose-ca2.yaml中service下的除ca1以外的配置
7. 修改docker-compose-ca2.yaml中的ports映射为7054:7054
8. 运行`echo $(cd crypto-config/peerOrganizations/org2.example.com/ca && ls *_sk)`命令，并将输出复制,准备第9步使用
9. 将docker-compose-ca2.yaml中的所有${BYFN_CA2_PRIVATE_KEY}替换为第8步的输出

*重要* 

这里的步骤3,4,8,9在每次更新证书等认证文件时也要同步修改

## 拷贝智能合约

将本项目的contract目录下的[game文件夹](http://192.168.3.10/system/product/FangChenMi/fcm-trial/fcm-block-client/tree/master/contract)拷贝到四个普通节点的fabric-sample/chaincode/目录下，如果该目录存在game文件夹，最好删除再拷贝

## 启动脚本的修改 

Fabric1.4中如果使用byfn.sh脚本启动，会自动执行scripts/script.sh脚本,  
该脚本会自动创建通道，并将所有节点加入通道，更新锚节点，安装智能合约并初始化等，  
不过不是我们项目的智能合约，而是fabric提供的示例，  
因此，我们修改scripts/script.sh脚本，让它更新完锚节点就结束，  
我们手动安装智能合约(也可以修改脚本，自动安装智能合约并初始化等，但稍微麻烦些，这里就不提了)

1. 在peer0.org1.example.com中注释掉scripts/script.sh本中更新锚节点以后的代码
2. 从updateAnchorPeers 0 2行以下开始注释到echo "All G...之上, 如下示例
```sh
updateAnchorPeers 0 2
:'
if [ "${NO_CHAINCODE}" != "true" ]; then

	## Install chaincode on peer0.org1 and peer0.org2
	echo "Installing chaincode on peer0.org1..."
	installChaincode 0 1
	echo "Install chaincode on peer0.org2..."
	installChaincode 0 2

	# Instantiate chaincode on peer0.org2
	echo "Instantiating chaincode on peer0.org2..."
	instantiateChaincode 0 2

	# Query chaincode on peer0.org1
	echo "Querying chaincode on peer0.org1..."
	chaincodeQuery 0 1 100

	# Invoke chaincode on peer0.org1 and peer0.org2
	echo "Sending invoke transaction on peer0.org1 peer0.org2..."
	chaincodeInvoke 0 1 0 2
	
	## Install chaincode on peer1.org2
	echo "Installing chaincode on peer1.org2..."
	installChaincode 1 2

	# Query on chaincode on peer1.org2, check if the result is 90
	echo "Querying chaincode on peer1.org2..."
	chaincodeQuery 1 2 90
	
fi
'

echo
echo "========= All GOOD, BYFN execution completed =========== "
echo

echo
echo " _____   _   _   ____   "
echo "| ____| | \ | | |  _ \  "
echo "|  _|   |  \| | | | | | "
echo "| |___  | |\  | | |_| | "
echo "|_____| |_| \_| |____/  "
echo

exit 0
```

至此，所有的前置配置都准备好了

## 启动所有节点

启动order节点与所有peer节点及其couchdb数据库，cli客户端，ca客户端

1. 在orderer.example.com启动order节点
   1. `docker-compose -f docker-compose-orderer.yaml up -d`
2. 在peer0.org1.example.com启动peer0.org1.example.com，ca1，couchdb0，cli
   1. `docker-compose -f docker-compose-ca1.yaml -f docker-compose-peer0.yaml -f docker-compose-couch0.yaml up -d`
3. 在peer1.org1.example.com启动peer1.org1.example.com，couchdb1，cli
   1. `docker-compose -f docker-compose-peer1.yaml -f docker-compose-couch1.yaml up -d`
4. 在peer0.org2.example.com启动peer0.org2.example.com，ca2，couchdb2，cli
   1. `docker-compose -f docker-compose-ca2.yaml -f docker-compose-peer2.yaml -f docker-compose-couch2.yaml up -d`
5. 在peer1.org2.example.com启动peer1.org2.example.com，couchdb3，cli
   1. `docker-compose -f docker-compose-peer3.yaml -f docker-compose-couch3.yaml up -d`

*注意*  

此处在每个节点启动该节点的容器时必须使用以上命令一次性启动，启动后使用docker ps查看是否都在运行，如果没有，关闭移除重新启动(删除最后的-d参数可以实时查看日志，看是否有报错等)

## 执行scripts/script.sh初始化脚本

在peer0.org1.example.com执行scripts/script.sh脚本，初始化通道，所有节点加入通道，更新锚节点  
由于只在peer0.org1.example.com修改了scripts/script.sh脚本，因此在peer0.org1.example.com操作即可

1. 在peer0.org1.example.com执行`docker exec -it cli bash`进入cli容器
2. 使用命令`./scripts/script.sh`运行脚本
   1. 该脚本必须在容器中运行
   2. 该脚本默认初始化了一个ID为mychannel的通道

## 安装智能合约

智能合约需要在所有节点安装

1. 在所有peer节点执行`docker exec -it cli bash`进入cli容器
2. 在所有节点安装智能合约
   1. `peer chaincode install -n game -v 1.0 -l golang -p github.com/hyperledger/fabric-samples/chaincode/game`
   2. -p 后跟的参数为智能合约的包地址，有时候可能不在该路径，需要在cli容器中去查看具体路径
   3. -n 为安装的智能合约名称，之后所有调用都需要哦谈过这个名称
   4. -v 为智能合约版本
   5. -l 为智能合约语言
3. 可以使用`peer chaincode list --instantiated -C mychannel`查看该节点该通道安装的智能合约
   1. -C 后跟的为通道名称

## 实例化智能合约

智能合约安装后必须实例化才能有效

1. `peer chaincode instantiate  -o orderer.example.com:7050  -C mychannel  -n game -v 1.0 -c '{"Args":[]}' -P "AND('Org1MSP.member','Org2MSP.member')" --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt`
   1. 注意修改其中的参数(一般不用修改)
   2. -P 跟的参数为背书策略，此处为必须有组织1和组织2的成员背书(更复杂的背书策略可以自己扩展)
   3. 大部分参数都是证书路径

## 智能合约的调用

智能合约的调用主要分为写入和查询

1. 写入:`peer chaincode invoke -o orderer.example.com:7050 -C mychannel -n game -c '{"function":"addPlayTime","Args":["2", "200", "1583804862117"]}' --waitForEvent --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --peerAddresses peer0.org1.example.com:7051  --peerAddresses peer1.org1.example.com:7051 --peerAddresses peer0.org2.example.com:7051 --peerAddresses peer1.org2.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt`
   1. 该命令绝大多数都不需要修改，只需要修改-c后的参数即可，且只需要该json串的function和Args的属性
   2. -c 后跟的为传递给智能合约的参数,实际上为json串，不同的智能合约可能不同，function的属性为智能合约的函数名，Args的属性为该函数的参数
   3. 具体修改为什么值合法请查看智能合约的代码
3. 查询: `peer chaincode query -n game -c '{"Args":["queryPlayer","1"]}' -C mychannel`
   1. 该命令绝大多数都不需要修改，只需要修改-c后的参数即可，且只需要该json串的Args的属性,其中Args[0]为查看函数名称，Args[1]为玩家id


实际上智能合约中都是invoke函数，在invoke函数根据第一个参数的不同映射到不同的函数