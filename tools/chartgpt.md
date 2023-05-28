# ChatGpt

- [ChatGpt](#chatgpt)
  - [本地搭建Gpt](#本地搭建gpt)


## 本地搭建Gpt

使用Docker运行本地Gpt项目，可以不通过翻墙使用Gpt,开源项目为[Pandora](https://github.com/pengzhile/pandora), 运行教程参考[本地搭建ChatGPT](https://www.freedidi.com/9544.html)

1. 下载并安装Docker, Ubuntu可以参考[Docker](docker.md)
2. 下载Pandora镜像`docker pull pengzhile/pandora`
3. 启动Pandora镜像`docker run  -e PANDORA_CLOUD=cloud -e PANDORA_SERVER=0.0.0.0:8899 -p 8899:8899 -d pengzhile/pandora`
4. 浏览器访问: `http://ip:8899/`
