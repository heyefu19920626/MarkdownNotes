# Fabric浏览器


- [Fabric浏览器](#fabric浏览器)
  - [Requirement](#requirement)
  - [node.js 8.11.4安装](#nodejs-8114安装)
  - [PostgreSql安装](#postgresql安装)
  - [Jq安装](#jq安装)
  - [启动first-network](#启动first-network)
  - [配置Fabric blockchain-explorer](#配置fabric-blockchain-explorer)
- [错误处理](#错误处理)
  - [查看错误](#查看错误)
  - [module.js:549 throw err;](#modulejs549-throw-err)
  - [psql: 致命错误: 用户 "postgres" Ident 认证失败](#psql-致命错误-用户-postgres-ident-认证失败)
  - [locate: 未找到命令](#locate-未找到命令)
  - [执行locate时报错“locate: can not stat () `/var/lib/mlocate/mlocate.db':](#执行locate时报错locate-can-not-stat--varlibmlocatemlocatedb)



本教程基于Hyperledger Fabric1.4.3与Hyperledger Explorer release-4.0,参考了
1. [官方教程](https://github.com/hyperledger/blockchain-explorer/tree/release-4.0)
2. [基于 fabric1.4.2 的区块链浏览器搭建](https://learnblockchain.cn/2019/09/29/fabric-explorer/),主要是前置的安装
3. [Hyperledger Explorer 部署步骤与踩坑记录](https://www.jianshu.com/p/5725ab2ded89),主要是前置的安装与一些错误处理

## Requirement

1. nodejs 8.11.x (Note that v9.x is not yet supported)
2. PostgreSQL 9.5 or greater
3. Jq [https://stedolan.github.io/jq/]
4. docker-ce [https://www.docker.com/community-edition]
5. docker-compose [https://docs.docker.com/compose/]
6. GO [https://golang.org/dl/]
7. python2.5+,python3.0-

## node.js 8.11.4安装

1. 安装 nvm
   1. ``curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash``
   2. 查看当前支持的版本 ``nvm ls-remote``
   3. 安装（同时安装 npm）``nvm install 8.11.4``
2. 检查版本
   1. ``node -v``
   2. ``npm -v``
3. 切换源
   1. ``npm install -g nrm``
   2. ``nrm ls``
   3. ``nrm use taobao``

## PostgreSql安装

1. 安装
   1. ubuntu ``apt install postgresql postgresql-contrib``
   2. centos
      1.  ``rpm -Uvh https://download.postgresql.org/pub/repos/yum/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm``
      2.  ``yum install -y postgresql10-server postgresql10``
2. 初始化
   1. ubuntu 
      1. ``sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'postgres';"``
      2. ``sudo /etc/init.d/postgresql restart``
   2. ``postgresql-setup initdb``
3. 启动
   1. ``systemctl start postgresql`` 或者 ``service postgresql start``
4. 修改配置/var/lib/pgsql/data/pg_hba.conf
   1. 将ident修改为trust
   2. 开启远程访问修改#listen_addresses = 'localhost'  为  listen_addresses='*'
5. 命令行登录测试
   1. ``psql -h 127.0.0.1 -p 5432 -U postgres -d dbname(fabricexplorer)``
6. 修改密码
   1. ``sudo -u postgres psql``
   2. ``ALTER USER postgres WITH PASSWORD 'admin'``
7. 重启

   
## Jq安装 

``apt install jq``

## 启动first-network

1. 进入``fabric-sample/first-network/``目录下
2. ``./byfn.sh -m generate``
3. ``./byfn.sh -m up``

## 配置Fabric blockchain-explorer

1. 检出blockchain-explorer项目：``git clone github.com/hyperledger/blockchain-explorer``
2. 切换至分支release-4.0：``git checkout -b release-4.0 origin/release-4.0``
3. 修改数据库配置
   1. ``vim blockchain-explorer/app/explorerconfig.json``
   2. 修改其中的username与passwd,将其设为自己的用户名和密码，同时此用户名与密码还是将来web页面登录的账户与密码
   3. 执行数据库脚本`blockchain-explorer/app/persistence/fabric/postgreSQL/db`,
   4. `./createdb.sh`
4. 修改应用的配置，修改其中的证书路径，端口等,主要参照同目录下的config-balance-transfer.json文件 
   1. ``cd blockchain-explorer/app/platform/fabric/``
   2. ``cp config-balance-transfer.json config.json``
   3. ``vim config.json``
   4. 删除network-configs.network-1.organizations.Org*.certificateAuthorities的属性
   5. 修改network-configs.network-1.organizations.Org*.(adminPrivateKey|signedCert).path中的/crypto-config/之前的路径，改为自己的first-network的路径
   6. 同理修改其他所有与自己不同的路径
   7. 将所有节点的grpcs端口修改为正确端口，如果first-work没做任何修改的话，应该是7051,7053;8051,8053;9051,9053;10051,10053
   8. 删除network-configs.network-1.certificateAuthorities属性
5. 编译
   1. ``cd blockchain-explorer``
   2. ``npm install``
   3. ``cd blockchain-explorer/app/test``
   4. ``npm install``
   5. ``npm run test``
   6. ``cd client/``
   7. ``npm install``
   8. ``npm test -- -u --coverage``
   9. ``npm run build``
6. 运行
   1. ``cd blockchain-explorer``
   2. ``./start.sh``
7. 访问：该项目部署在8080端口，通过ip+8080端口访问即可,访问后使用3.1中的用户名与密码登录
7. 停止
   1. ``./stop.sh``

# 错误处理

## 查看错误
1. 主要在blockchain-explorer/logs/文件夹下看相关日志，app，db，console，都看

## module.js:549 throw err;
> 先删除``node_modules``整个文件夹，然后``npm cache clean``或者``npm cache clean --force``,然后``npm install``


## psql: 致命错误: 用户 "postgres" Ident 认证失败
> vim /var/lib/pgsql/(此处可能不为空)/data/pg_hba.conf  
> 把这个配置文件中的认证 METHOD的ident修改为trust，可以实现用账户和密码来访问数据库

## locate: 未找到命令
> yum install mlocate

## 执行locate时报错“locate: can not stat () `/var/lib/mlocate/mlocate.db':
> 更新数据库  
> updatedb


