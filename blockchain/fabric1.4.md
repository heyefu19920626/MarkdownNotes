# Fabric 1.4

- [Fabric 1.4](#fabric-14)
- [Requirment](#requirment)
  - [crul 安装](#crul-安装)
  - [docker-ce安装](#docker-ce安装)
    - [centos 安装](#centos-安装)
    - [ubuntu安装](#ubuntu安装)
  - [docker-compose 安装](#docker-compose-安装)
  - [git 安装](#git-安装)
  - [go 安装与配置](#go-安装与配置)
- [Fabric单机启动first-network](#fabric单机启动first-network)
  - [下载fabric源码](#下载fabric源码)
  - [使用脚本下载可执行文件和docker镜像等](#使用脚本下载可执行文件和docker镜像等)
  - [使用脚本启动first-network](#使用脚本启动first-network)
  - [停止](#停止)
- [Fabric集群启动first-network](#fabric集群启动first-network)
  - [生成创世区块和组织证书，并同步给所有机器](#生成创世区块和组织证书并同步给所有机器)
  - [修改docker所有节点基础配置文件](#修改docker所有节点基础配置文件)
  - [修改order节点的docker配置文件](#修改order节点的docker配置文件)
  - [修改组织org1的peer0节点的docker配置文件](#修改组织org1的peer0节点的docker配置文件)
  - [修改组织org1的peer1节点的docker配置文件](#修改组织org1的peer1节点的docker配置文件)
  - [修改组织org2的节点docker配置文件](#修改组织org2的节点docker配置文件)
  - [启动](#启动)
  - [停止](#停止-1)
- [Fabric智能合约部署](#fabric智能合约部署)
  - [在开发模式编译智能合约](#在开发模式编译智能合约)
  - [安装智能合约game](#安装智能合约game)
  - [实例化智能合约game](#实例化智能合约game)
  - [调用智能合约game](#调用智能合约game)
  - [查询智能合约game](#查询智能合约game)
- [错误汇总](#错误汇总)
  - [Windows进入docker容器命令行bash出错](#windows进入docker容器命令行bash出错)

# Requirment
1. linux内核升级，最好4.12+
2. curl
3. docker-ce 安装
4. docker-compose
5. git
6. go

##  crul 安装
```sh
yum install curl
```


## docker-ce安装
### centos 安装
```sh
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager  --add-repo  https://download.docker.com/linux/centos/docker-ce.repo
sudo yum-config-manager --enable docker-ce-edge
sudo yum-config-manager --enable docker-ce-test
sudo yum-config-manager --disable docker-ce-edge
sudo yum makecache fast
sudo yum install docker-ce
docker 启动
service docker start
开机自启
chkconfig docker on
```
### ubuntu安装
```sh
安装能够让apt通过https使用仓库的包
sudo apt-get install  apt-transport-https  ca-certificates  curl gnupg-agent software-properties-common
添加docker官方GPG密钥
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
添加docker仓库地址
sudo add-apt-repository  "deb [arch=amd64] https://download.docker.com/linux/ubuntu  $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce 
```
## docker-compose 安装
1. ``curl -L https://github.com/docker/compose/releases/download/1.20.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose``  
2. ``chmod +x /usr/local/bin/docker-compose``
3. 如果有问题，去github仓库找对应的release命令

## git 安装
```sh
yum install git
```

## go 安装与配置
1. 下载go安装包 ``wget https://studygolang.com/dl/golang/go1.13.4.linux-amd64.tar.gz``
2. 解压并放入/usr/loacl/目录下``tar -C /usr/local -xzf go1.13.4.linux-amd64.tar.gz``
3. 配置环境变量，并使其生效
```sh
修改/etc/profile文件
# go目录
export PATH=$PATH:/usr/local/go/bin
# go源码目录
export GOPATH=/opt/gopath
使其生效
source profile
```

# Fabric单机启动first-network

## 下载fabric源码
```
go get github.com/hyperledger/fabric
进入目录切换分支
git checkout -b release-1.4 origin/release-1.4
```

## 使用脚本下载可执行文件和docker镜像等
1. 进入fabric/script/目录  
2. 执行bootstrap.sh脚本``./bootstrap.sh``,该脚本会自动下载安装fabric-sample仓库和fabric的二进制文件,最后会自动下载相关的docker镜像并重新命名tag为latest   
3. 如果遇到网络问题没有一次性下载成功,重新执行脚本即可，为避免重复已成功的步骤，也可编辑脚本，在脚本最后注释掉已经完成的步骤

## 使用脚本启动first-network
1. 进入fabric-sample/first-network文件夹
2. 启动first-network：``./byfn.sh up``

## 停止
1. ``./byfn.sh down``  
2. 该命令会停止并删除已启动的容器与智能合约的镜像

# Fabric集群启动first-network

first-network集群需要5台独立的机器，一台排序节点order，四台peer节点，先确保每台机器都能单机运行first-network成功,然后停止所有已启动的fabric网络,本次所有操作基本默认都在first-network目录下

##  生成创世区块和组织证书，并同步给所有机器
1. 先删除所有旧的证书文件,即crypto-config文件夹和channel-artifacts目录下的所有文件（不要删除channel-artifacts目录）： ``rm -rf channel-artifacts/* crypto-config``  
2. 现在order节点的机器下生成证书等文件: ``./byfn.sh generate``  
3. 将生成的crypto-configh和channel-artifacts目录同步到所有机器的first-network文件夹下

## 修改docker所有节点基础配置文件  

该配置文件为first-network/base/docker-compose-base.yaml,因为分布到多台机器上，所以所有节点的端口都应该为7051-7054之间，order节点还是7050

1. 将文件中除order节点所有的ports都修改为7051  
2. 将所有节点的environment中不正确的端口都修改为正确的，比如8052应为7052，10051应为7051等
3. 将修改完成的该文件同步到所有机器的first-network/bash文件夹下

## 修改order节点的docker配置文件

单机模式下脚本是利用docker-compose-cli.yaml创建docker容器的，集群模式下可以根据该文件做对应修改

1. 将cli文件复制一份出来做修改:``cp docker-compose-cli.yaml docker-compose-orderer.yaml``
2. 修改docker-compose-oederer.yaml:``vim docker-compose-oederer.yaml``
3. 删除volumns下的除 orderer.example.com:的其他行
4. 删除services下的除了 orderer.example.com的的其他如peer0.org1.example.com等的配置

## 修改组织org1的peer0节点的docker配置文件

1. 将cli文件复制一份出来做修改:``cp docker-compose-cli.yaml docker-compose-peer.yaml``
2. 修改docker-compose-peer.yaml:``vim docker-compose-peer.yaml``
3. 删除volumns下的除 peer0.org1.example.com:的其他行
4. 删除services下的除了 peer0.org1.example.com与cli的的其他如orderer,peer1.org1.example.com等的配置
5. 在peer0.org1.example.com配置中增加order的extra_hosts配置
```yaml
  peer0.org1.example.com:
    container_name: peer0.org1.example.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.org1.example.com
    networks:
      - byfn
    # 这里增加host配置，如果这里不配也可以修改主机的hosts映射
    extra_hosts:
    # 主节点只需要识别order节点即可
     - "orderer.example.com:192.168.11.105"
```
6. 删除cli的配置中depends_on中除了本节点外的其他节点
```yaml
depends_on:
      - peer0.org1.example.com
```
7. 在cli的配置最后同样增加host配置，不过此处要配置所有节点的host
```yaml
    extra_hosts:
     - "orderer.example.com:192.168.11.105"
     - "peer0.org1.example.com:192.168.11.101"
     - "peer1.org1.example.com:192.168.11.102"
     - "peer0.org2.example.com:192.168.11.103"
     - "peer1.org2.example.com:192.168.11.104"
```

## 修改组织org1的peer1节点的docker配置文件

1. 将cli文件复制一份出来做修改:``cp docker-compose-cli.yaml docker-compose-peer.yaml``
2. 修改docker-compose-peer.yaml:``vim docker-compose-peer.yaml``
3. 删除volumns下的除 peer1.org1.example.com:的其他行
4. 删除services下的除了 peer1.org1.example.com与cli的的其他如orderer,peer1.org1.example.com等的配置
5. 在peer1.org1.example.com配置中增加order与本组织内的peer0的extra_hosts配置
```yaml
  peer1.org1.example.com:
    container_name: peer1.org1.example.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.org1.example.com
    networks:
      - byfn
    extra_hosts:
     - "orderer.example.com:192.168.11.105"
     # 如果是组织org2的peer1节点，只需要在此处增加org2的peer0节点的host即可
     - "peer0.org1.example.com:192.168.11.101"
```
6. 删除cli的配置中depends_on中除了本节点外的其他节点
7. 在cli的配置最后同样增加host配置，不过此处要配置所有节点的host
8. 修改cli中environment中的证书路径与节点配置
```yaml
    environment:
      - SYS_CHANNEL=$SYS_CHANNEL
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      #- FABRIC_LOGGING_SPEC=DEBUG
      - FABRIC_LOGGING_SPEC=INFO
      - CORE_PEER_ID=cli
      # 此处应该修改为对应的节点
      - CORE_PEER_ADDRESS=peer1.org1.example.com:7051
      # 此处的组织也需要修改，如果是组织org2,需要修改为Org2MSP
      - CORE_PEER_LOCALMSPID=Org1MSP
      - CORE_PEER_TLS_ENABLED=true
      # 下面的几行证书路径需要修改为与本组织和本节点匹配
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
```

## 修改组织org2的节点docker配置文件

主要步骤与上面相同

## 启动

1. 启动order节点``docker-compose -f docker-compose-orderer.yaml -up -d``
2. 启动所有peer节点，在每台peer节点执行``docker-compose -f docker-compose-peer.yaml -up -d``
3. 选择Org1的peer0节点,进入客户端cli容器``docker exec -it cli bash``,如果``docker ps``查看发现cli容器已退出，则继续修改peer的docker容器，删除cli的command属性
4. 执行cli容器的启动脚本``./script/script.sh``

## 停止

1. 在peer节点执行``docker-compose -f docker-compose-peer.yaml down``即可停止并删除容器,如果没有完全删除，则手动删除智能合约容器与镜像
2. 在order节点执行``docker-compose -f docker-compose-orderer.yaml down``

# Fabric智能合约部署

## 在开发模式编译智能合约
1. 将代码文件夹拷贝至``$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode``目录下,我这为game文件夹  
2. 进入``$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode-docker-devmode``目录下  
3. ``docker-compose -f docker-compose-simple.yaml up -d``  
4. ``进入链码容器docker exec -it chaincode bash``  
5. ``cd game``
6. ``go build``

##  安装智能合约game
> ``peer chaincode install -n game -v 1.0 -l golang -p github.com/chaincode/game``
> ``peer chaincode install -n game -v 1.0 -l golang -p github.com/hyperledger/fabric-samples/chaincode/game``

## 实例化智能合约game
> ``peer chaincode instantiate -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game -l golang -v 1.0 -c '{"Args":["init"]}' -P 'AND ('\''Org1MSP.peer'\'','\''Org2MSP.peer'\'')'``

## 调用智能合约game
> 不带背书  
> ``peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game -c '{"Args":["set","1111111111111111111111111111111111111111111111111111111111111111111111111573803744"]}'``  
> 带背书策略，如果还用上面那个，不同同步到其他节点  
> ``peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["set","1111111111111111111111111111111111111111111111111111111111111111111111111573803744"]}'``  


## 查询智能合约game
> ``peer chaincode query -n game -c '{"Args":["query",""]}' -C mychannel``


# 错误汇总

## Windows进入docker容器命令行bash出错
问题： windows 运行``docker exec -it cli bash``出错  
解决：在前面添加参数winpty, ``winpty docker exec -it cli bash``
