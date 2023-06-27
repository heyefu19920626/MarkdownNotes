# Docker

- [Docker](#docker)
  - [Ubuntu 22.04安装docker](#ubuntu-2204安装docker)
    - [安装证书相关的](#安装证书相关的)
    - [添加docker源](#添加docker源)
    - [安装](#安装)
    - [启动](#启动)
    - [测试](#测试)
    - [卸载](#卸载)
  - [配置代理](#配置代理)
    - [配置Client代理](#配置client代理)
    - [配置容器代理](#配置容器代理)
    - [WSL2中配置代理](#wsl2中配置代理)
  - [配置镜像加速](#配置镜像加速)
  - [拉取镜像](#拉取镜像)
  - [运行镜像](#运行镜像)
  - [进入容器](#进入容器)
  - [镜像管理](#镜像管理)
  - [镜像删除](#镜像删除)
  - [容器管理](#容器管理)
  - [文件传输](#文件传输)
  - [推送镜像至https://hub.docker.com](#推送镜像至httpshubdockercom)
  - [镜像与文件](#镜像与文件)
  - [volumn卷的挂载](#volumn卷的挂载)
  - [查看docker容器日志](#查看docker容器日志)
  - [设置时区](#设置时区)
  - [Dockerfile](#dockerfile)
    - [设置时区](#设置时区-1)
    - [添加文件夹](#添加文件夹)


## Ubuntu 22.04安装docker

[参考](https://yeasy.gitbook.io/docker_practice/install/ubuntu)


### 安装证书相关的

```bash
$ curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg


# 官方源
# $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### 添加docker源

向 sources.list 中添加 Docker 软件源

```bash
$ echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


# 官方源
# $ echo \
#   "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
#   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 安装

```bash
$ sudo apt-get update

$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

### 启动

```bash
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

### 测试

```bash
docker run --rm hello-world
```

### 卸载
```bash
$ sudo apt-get remove docker docker-engine  docker.io
```

## 配置代理

配置代理可以参考[这里](https://kingdo.club/2022/01/06/docker%E5%8C%85%E6%8B%ACwsl2%E5%A6%82%E4%BD%95%E9%85%8D%E7%BD%AEproxy/)

docker使用proxy分两种情况：
1. docker client希望使用代理，也就是在执行docker pull、docker push等操作时通过代理来访问镜像仓库
2. 容器实例希望使用代理，也就是在容器内部希望通过代理来访问网络

### 配置Client代理

1. 创建配置文件
```bash
sudo vim /etc/systemd/system/docker.service.d/http-proxy.conf
```
2. 添加配置
```bash
[Service]
Environment="HTTP_PROXY=http://211.69.198.232:8118"
Environment="HTTPS_PROXY=http://211.69.198.232:8118"
Environment="NO_PROXY=localhost,127.0.0.1,docker-registry.example.com,.corp,211.69.198.232"
```
3. 重启容器
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 配置容器代理

1. 配置文件方式
```bash
创建配置文件
vim  ~/.docker/config.json

写入
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "http://211.69.198.232:8118",
     "httpsProxy": "http://211.69.198.232:8118",
     "noProxy": "*.test.example.com,.example2.com,127.0.0.0/8,211.69.198.232"
   }
 }
}
```
2. 环境变量方式

| Variable  | Dockerfile example  | docker run example |
|---|---|
| HTTP_PROXY  |  ENV HTTP_PROXY=”http://192.168.1.12:3128“ | –env HTTP_PROXY=”http://192.168.1.12:3128“ |
| HTTP_PROXY  |  ENV HTTPS_PROXY=”https://192.168.1.12:3128“ | 	–env HTTPS_PROXY=”https://192.168.1.12:3128“ |
| HTTP_PROXY  |  ENV FTP_PROXY=”ftp://192.168.1.12:3128″ | –env FTP_PROXY=”ftp://192.168.1.12:3128″ | 
| HTTP_PROXY  |  ENV NO_PROXY=”*.test.example.com,.http://example2.com“ | –env NO_PROXY=”*.test.example.com,.http://example2.com“ |


### WSL2中配置代理

对于容器实例的代理, 配置方式同上所述, 问题在于Docker CLient的代理配置, 因为WSL2并不是采用Systemd方式管理的!

```bash
sudo vim /etc/default/docker

输入:
export HTTP_PROXY="http://211.69.198.232:8118"
export HTTPS_PROXY="http://211.69.198.232:8118"
export NO_PROXY="localhost,127.0.0.0/8,172.16.0.0/12,192.168.0.0/16,10.0.0.0/8"
```
配置完成可以通过docker info查看配置

## 配置镜像加速
> /etc/docker/daemon.json

## 拉取镜像
docker pull how2j/tmall

## 运行镜像
> docker run -dit --privileged -p21:21 -p80:80 -p8080:8080 -p30000-30010:30000-30010 -v /root/webapps/:/opt/tomcat/webapps/ --name how2jtmall how2j/tmall:latest /usr/sbin/init

- docker run 表示运行一个镜像
- -dit 是 -d -i -t 的缩写。 -d ，表示 detach，即在后台运行。 -i 表示提供交互接口，这样才可以通过 docker 和 跑起来的操作系统交互。 -t 表示提供一个 tty (伪终端)，与 -i 配合就可以通过 ssh 工具连接到 这个容器里面去了
- --privileged 启动容器的时候，把权限带进去。 这样才可以在容器里进行完整的操作
- -p21:21 第一个21，表示在CentOS 上开放21端口。 第二个21 表示在容器里开放21端口。 这样当访问CentOS 的21端口的时候，就会间接地访问到容器里了
- -p80:80 和 21一个道理
- -p8080:8080 和21 一个道理，在本例里，访问的地址是 http://192.168.84.128:8080/tmall/， 这个 192.168.84.128 是CentOS 的ip地址，8080是 CentOS 的端口，但是通过-p8080:8080 这么一映射，就访问到容器里的8080端口上的 tomcat了
- -p30000-30010 和21也是一个道理，这个是ftp用来传输数据的
- -v：表示需要将本地哪个目录挂载到容器中，格式：-v <宿主机目录>:<容器目录>
- --name how2jtmall 给容器取了个名字，叫做 how2jtmall，方便后续管理
- how2j/tmall:latest how2j/tmall就是镜像的名称， latest是版本号，即最新版本
- /usr/sbin/init: 表示启动后运行的程序，即通过这个命令做初始化

## 进入容器
- docker exec -it how2jtmall /bin/bash

## 镜像管理
- search 查看仓库里有些什么镜像
- pull 拉取镜像
- images 查看本地有些什么镜像
- rmi 删除本地镜像 -f 强制删除
- 修改本地镜像名称
- push , 把镜像提交到仓库

## 镜像删除
- service docker stop
- rm -rf /var/lib/docker 删除所有镜像
- service docker start

## 容器管理
- 运行 run
- 进入 exec
- 生命周期管理， 暂停，恢复，停止，启动 pause, unpause, stop, start
- ps 查看所有的容器
- 检查某个具体的容器
- rm 删除容器
- inspect 容器名称 查看容器信息
- commit，对容器做了修改后，把改动后的容器，再次转换为镜像
    - > docker commit -m "state" 容器id 仓库名:tag

## 文件传输
- 根据短id或容器名称获取id全称
>  docker inspect -f '{{.Id}}' 容器名称/短id
- 本机与容器传文件,或相反
> docker cp 本地文件路径 id全称:容器路径

## 推送镜像至https://hub.docker.com
- 登录账号
> docker login
- 规范命名镜像
> docker tag 镜像id 新id(docker.io/用户名/镜像名)
- 推送
> docker push 镜像

## 镜像与文件
- 镜像导出为文件
> docker save -o 要保存的文件名 要保存的镜像
- 文件载入为镜像
> docker load --input 文件名
> docker load < 文件名

## volumn卷的挂载


## 查看docker容器日志
docker logs -f peer0.org1.example.com --tail=300

## 设置时区

1. 已运行容器里的时区修改
   1. `ln -sf /usr/share/zoneinfo/Asia/Shanghai    /etc/localtime`
   2. 或者`cp /usr/share/zoneinfo/Asia/Shanghai    /etc/localtime`  ,重启容器
2. 创建并运行容器
   1. `docker run -e TZ="Asia/Shanghai" -d -p 80:80 --name nginx nginx`

## Dockerfile

### 设置时区
```dockerfile
FROM anapsix/alpine-java
ADD *.jar test.jar
# 下面两句设置时区
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo 'Asia/Shanghai' >/etc/timezone
ENTRYPOINT java -Xms1g -Xmx1g -jar test.jar
```

### 添加文件夹

Dockerfile添加文件夹，则必须镜像中存在和当前文件夹同名的文件夹才行,例如，我希望将当前目录下的views文件夹添加到docker镜像中的app文件夹下。也许你会采用这样的方式
> ADD views /app  
这样其实并不能实现，应该通过下面的方式:
> ADD views /app/views  
也就是说：镜像中存在和当前需要拷贝或添加的文件夹同名的文件夹时，才能够拷贝或添加成功
