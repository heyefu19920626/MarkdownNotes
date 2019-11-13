# fabric的多机部署

- [集群内部手动启动](#shoudong)
- [运行chaincode](#shilihua)

#### 环境准备

- linux内核升级，最好4.12+

- docker-ce 安装

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

- crul 安装

```sh
yum install curl
```

- docker-compose 安装

```sh
如果有问题，去github仓库找对应的release
curl -L https://github.com/docker/compose/releases/download/1.20.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose chmod +x /usr/local/bin/docker-compose
```

- go 安装与配置

```sh
curl -O https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.8.3.linux-amd64.tar.gz
修改/etc/profile文件
export PATH=$PATH:/usr/local/go/bin
export GOPATH=/opt/gopath
使其生效
source profile
```

- git 安装

```sh
yum install git
```

- 下载fabric源码
```
go get github.com/hyperledger/fabric
切换分支
git checkout -b release-1.0 origin/release-1.0
```

- 下载fabric相关镜像并修改tag为latest

```sh
docker pull hyperledger/fabric-peer:x86_64-1.0.0
docker pull hyperledger/fabric-tools:x86_64-1.0.0
docker pull hyperledger/fabric-orderer:x86_64-1.0.0
docker pull hyperledger/fabric-couchdb:x86_64-1.0.0
docker pull hyperledger/fabric-kafka:x86_64-1.0.0
docker pull hyperledger/fabric-ca:x86_64-1.0.0
docker pull hyperledger/fabric-zookeepe:x86_64-1.0.0r
docker pull hyperledger/fabric-baseos:x86_64-1.0.0
修改tag
docker tag IMAGEID(镜像id) REPOSITORY:TAG（仓库：标签）
将镜像到处为文件
docker save IMAGEID > 文件路径+名称
导入文件为镜像
docker load < 文件路径+名称
```

- 运行e2e_cli项目测试

```
进入/opt/gopath/src/github.com/hyperledger/fabric/examples/e2e_cli
bash network_setup.sh up
bash network_setup.sh down
```

## 多节点集群配置

#### 生成公私钥、证书、创世区块等

```
进入/opt/gopath/src/github.com/hyperledger/fabric/examples/e2e_cli/
bash generateArtifacts.sh mychannel
将生成channel-artifacts和crypto-config目录拷贝到其他服务器e2e_cli目录下
目录里有orderer和peer的证书、私钥和用于通信加密的tls证书等文件，它通过configex.yaml配置文件生成
scp -r channel-artifacts root@ip:/opt/gopath/src/github.com/hyperledger/fabric/examples/e2e_cli/
```

#### peer0.org1.example.com节点的docker-compose文件

- 在原cli基础上修改
```
cp docker-compose-cli.yaml docker-compose-peer.yaml
```

- 修改docker-compose-peer.yaml,去掉orderer的配置，只保留一个peer和cli,给peer增加extra_hosts设置
```
peer0.org1.example.com:
    container_name: peer0.org1.example.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.org1.example.com
    # 此处域名与地址映射，所有peer节点必须有order节点的地址，组织内子节点除初始化节点外还要添加对应的初始化节点
    extra_hosts:
     - "orderer.example.com:10.130.116.8"
```

- 给cli增加extra_hosts设置,去掉command
```
cli:
    container_name: cli
    image: hyperledger/fabric-tools
    tty: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_LOGGING_LEVEL=DEBUG
      - CORE_PEER_ID=cli
      # 其余节点时此处ADDRESS需要修改
      - CORE_PEER_ADDRESS=peer0.org1.example.com:7051
      # 如果不是组织1需要修改为Org2MSP
      - CORE_PEER_LOCALMSPID=Org1MSP
      - CORE_PEER_TLS_ENABLED=true
      # 下面的几行org1.example.com文件夹与peer0.org1.example.com也需要做相应修改
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    volumes:
        - /var/run/:/host/var/run/
        - ../chaincode/go/:/opt/gopath/src/github.com/hyperledger/fabric/examples/chaincode/go
        - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/
        - ./scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
    # 此处依赖只需要保留本节点即可
    depends_on:
      - peer0.org1.example.com
    # 此处域名与地址映射需要有所有节点地址,如果不使用此处的映射，也可以修改服务器的host解决
    extra_hosts:
     - "orderer.example.com:10.130.116.8"
     - "peer0.org1.example.com:10.130.116.9"
     - "peer1.org1.example.com:10.130.116.10"
     - "peer0.org2.example.com:10.130.116.25"
     - "peer1.org2.example.com:10.130.116.27"
```

- 修改base/docker-compose-base.yaml文件，将所有peer的端口映射改为相同的
```
ports:
  - 7051:7051
  - 7052:7052
  - 7053:7053
```

#### 设置peer1.org2.excmple.com节点的docker-compose文件

```
peer1.org2.example.com:
    container_name: peer1.org2.example.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.org2.example.com
    extra_hosts:
     - "orderer.example.com:10.130.116.8"
     - "peer0.org2.example.com:10.130.116.9"

  cli:
    container_name: cli
    image: hyperledger/fabric-tools
    tty: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_LOGGING_LEVEL=DEBUG
      - CORE_PEER_ID=cli
      - CORE_PEER_ADDRESS=peer1.org2.example.com:7051
      - CORE_PEER_LOCALMSPID=Org2MSP
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    volumes:
        - /var/run/:/host/var/run/
        - ../chaincode/go/:/opt/gopath/src/github.com/hyperledger/fabric/examples/chaincode/go
        - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/
        - ./scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
    depends_on:
      - peer1.org2.example.com
    extra_hosts:
     - "orderer.example.com:10.130.116.8"
     - "peer0.org1.example.com:10.130.116.9"
     - "peer1.org1.example.com:10.130.116.10"
     - "peer0.org2.example.com:10.130.116.25"
     - "peer1.org2.example.com:10.130.116.27"
```

#### 设置order节点的docker-compose文件

- cp docker-compose-cli.yaml docker-compose-orderer.yaml
- 保留order设置，其他peer和cli设置都删除。orderer可以不设置extra_hosts
```
orderer.example.com:
     extends:
       file:   base/docker-compose-base.yaml
       service: orderer.example.com
     container_name: orderer.example.com
```


## 启动Fabric多节点集群

#### 启动orderer节点服务
> docker-compose -f docker-compose-orderer.yaml up -d

#### 启动peer节点服务
> docker-compose -f docker-compose-peer.yaml up -d

#### 使用脚本创建channel和运行chaincode
> docker exec -it cli bash  
> ./scripts/script.sh mychannel

<div id='shoudong'></div>

## 集群内部手动启动

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

- docker cp cli:/opt/gopath/src/github.com/hyperledger/fabric/peer/mychannel.block .
- scp root@192.168.11.101:/opt/gopath/src/github.com/hyperledger/fabric/examples/e2e_cli/mychannel.block .
- docker cp mychannel.block cli:/opt/gopath/src/github.com/hyperledger/fabric/peer/

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
peer chaincode install -nmycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```


<div id="shilihua"></div>

#### 运行chaincode

- 实例化chaincode

```
peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile $ORDERER_CA -C mychannel -nmycc -v 1.0 -c '{"Args":["init","a","100","b","200"]}' -P "OR ('Org1MSP.member','Org2MSP.member')"
或者
peer chaincode instantiate -o orderer.example.com:7050 --tls true --cafile $ORDERER_CA -C mychannel -nmycc -v 1.0 -c '{"Args":["init","a","100","b","200"]}' -P "OR ('Org1MSP.member','Org2MSP.member')"
```


- 如果实例化出错有可能有以下问题
  + 链码的旧镜像没有删除

- 在peer0.org1.example.com上查看chaincode的状态，登录到VM1上并进入cli容器内部执行：

```
peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
```

- 转账,把a账户的10元转给b
```
ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

peer chaincode invoke -o orderer.example.com:7050  --tls true --cafile $ORDERER_CA -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'
```
