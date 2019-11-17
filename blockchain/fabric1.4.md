# fabric1.4集群部署，及智能合约部署

## 在开发模式编译智能合约
> 将代码文件夹拷贝至``$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode``目录下,我这为game文件夹  
> 进入$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode-docker-devmode目录下  
> docker-compose -f docker-compose-simple.yaml up -d  
> 进入链码容器docker exec -it chaincode bash  

##  安装智能合约game
> peer chaincode install -n game -v 1.0 -l golang -p github.com/chaincode/game

## 实例化智能合约game
> peer chaincode instantiate -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game -l golang -v 1.0 -c '{"Args":["init"]}' -P 'AND ('\''Org1MSP.peer'\'','\''Org2MSP.peer'\'')'

## 调用智能合约game
> 不带背书  
> peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game -c '{"Args":["set","1111111111111111111111111111111111111111111111111111111111111111111111111573803744"]}'  
> 带背书策略，如果还用上面那个，不同同步到其他节点  
> peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n game --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["set","1111111111111111111111111111111111111111111111111111111111111111111111111573803744"]}'  


## 查询智能合约game
> peer chaincode query -n game -c '{"Args":["query",""]}' -C mychannel


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