# Docker

- [Docker](#docker)
  - [Ubuntu 22.04安装docker](#ubuntu-2204安装docker)
    - [安装证书相关的](#安装证书相关的)
    - [添加docker源](#添加docker源)
    - [安装](#安装)
    - [启动](#启动)
    - [测试](#测试)
    - [卸载](#卸载)


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