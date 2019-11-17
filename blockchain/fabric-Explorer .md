# Fabric浏览器

## Requirement

1. nodejs 8.11.x (Note that v9.x is not yet supported)
2. PostgreSQL 9.5 or greater
3. Jq [https://stedolan.github.io/jq/]
4. docker-ce [https://www.docker.com/community-edition]
5. docker-compose [https://docs.docker.com/compose/]
6. GO [https://golang.org/dl/]

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
   1. ``postgresql-setup initdb``
3. 启动
   1. ``systemctl start postgresql``
4. 修改配置/var/lib/pgsql/data/pg_hba.conf
   1. 将ident修改为trust
   2. 开启远程访问修改#listen_addresses = 'localhost'  为  listen_addresses='*'
5. 命令行登录测试
   1. ``psql -h 127.0.0.1 -p 5432 -U postgres -d dbname``
6. 修改密码
   1. ``sudo -u postgres psql``
   2. ``ALTER USER postgres WITH PASSWORD 'admin'``
6. 重启
   
## Jq安装 
``apt install jq``


