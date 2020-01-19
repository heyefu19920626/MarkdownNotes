# Docker

- [Docker](#docker)
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


- 仓库： 别人做好的现成的镜像，都放在仓库里
- 镜像： 自己要用哪个镜像，就先拉到本地来。镜像就相当于还没激活的容器。
- 容器： 容器就是跑起来的镜像，就是一个完整的工作环境

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