# fabric1.4集群部署，及智能合约部署


- [fabric1.4集群部署，及智能合约部署](#fabric14%e9%9b%86%e7%be%a4%e9%83%a8%e7%bd%b2%e5%8f%8a%e6%99%ba%e8%83%bd%e5%90%88%e7%ba%a6%e9%83%a8%e7%bd%b2)
  - [生成组织证件之类等（待完善）](#%e7%94%9f%e6%88%90%e7%bb%84%e7%bb%87%e8%af%81%e4%bb%b6%e4%b9%8b%e7%b1%bb%e7%ad%89%e5%be%85%e5%ae%8c%e5%96%84)
  - [在开发模式编译智能合约](#%e5%9c%a8%e5%bc%80%e5%8f%91%e6%a8%a1%e5%bc%8f%e7%bc%96%e8%af%91%e6%99%ba%e8%83%bd%e5%90%88%e7%ba%a6)
  - [安装智能合约game](#%e5%ae%89%e8%a3%85%e6%99%ba%e8%83%bd%e5%90%88%e7%ba%a6game)
  - [实例化智能合约game](#%e5%ae%9e%e4%be%8b%e5%8c%96%e6%99%ba%e8%83%bd%e5%90%88%e7%ba%a6game)
  - [调用智能合约game](#%e8%b0%83%e7%94%a8%e6%99%ba%e8%83%bd%e5%90%88%e7%ba%a6game)
  - [查询智能合约game](#%e6%9f%a5%e8%af%a2%e6%99%ba%e8%83%bd%e5%90%88%e7%ba%a6game)
- [PostgreSQL](#postgresql)
  - [登录pg](#%e7%99%bb%e5%bd%95pg)
  - [修改密码](#%e4%bf%ae%e6%94%b9%e5%af%86%e7%a0%81)
  - [psql: 致命错误: 用户 "postgres" Ident 认证失败](#psql-%e8%87%b4%e5%91%bd%e9%94%99%e8%af%af-%e7%94%a8%e6%88%b7-%22postgres%22-ident-%e8%ae%a4%e8%af%81%e5%a4%b1%e8%b4%a5)
  - [locate: 未找到命令](#locate-%e6%9c%aa%e6%89%be%e5%88%b0%e5%91%bd%e4%bb%a4)
  - [执行locate时报错“locate: can not stat () `/var/lib/mlocate/mlocate.db':](#%e6%89%a7%e8%a1%8clocate%e6%97%b6%e6%8a%a5%e9%94%99locate-can-not-stat--varlibmlocatemlocatedb)

## 生成组织证件之类等（待完善）
> ``cryptogen generate --config=./crypto-config.yaml``



## 在开发模式编译智能合约
> 将代码文件夹拷贝至``$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode``目录下,我这为game文件夹  
> 进入``$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode-docker-devmode``目录下  
> ``docker-compose -f docker-compose-simple.yaml up -d``  
> ``进入链码容器docker exec -it chaincode bash``  

##  安装智能合约game
> ``peer chaincode install -n game -v 1.0 -l golang -p github.com/chaincode/game``

## 实例化智能合约game
> ``peer chaincode instantiate -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game -l golang -v 1.0 -c '{"Args":["init"]}' -P 'AND ('\''Org1MSP.peer'\'','\''Org2MSP.peer'\'')'``

## 调用智能合约game
> 不带背书  
> ``peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game -c '{"Args":["set","1111111111111111111111111111111111111111111111111111111111111111111111111573803744"]}'``  
> 带背书策略，如果还用上面那个，不同同步到其他节点  
> ``peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["set","1111111111111111111111111111111111111111111111111111111111111111111111111573803744"]}'``  


## 查询智能合约game
> ``peer chaincode query -n game -c '{"Args":["query",""]}' -C mychannel``


# PostgreSQL

## 登录pg
> psql -h 127.0.0.1 -p 5432 -U postgres -d fabricexplorer

## 修改密码
> sudo -u postgres psql  
> ALTER USER postgres WITH PASSWORD '930912'

## psql: 致命错误: 用户 "postgres" Ident 认证失败
> vim /var/lib/pgsql/(此处可能不为空)/data/pg_hba.conf  
> 把这个配置文件中的认证 METHOD的ident修改为trust，可以实现用账户和密码来访问数据库

## locate: 未找到命令
> yum install mlocate

## 执行locate时报错“locate: can not stat () `/var/lib/mlocate/mlocate.db':
> 更新数据库  
> updatedb
