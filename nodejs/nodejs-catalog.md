# NodeJs

- [NodeJs](#nodejs)
  - [npm仓库设置](#npm仓库设置)
    - [单项目仓库设置](#单项目仓库设置)
    - [全局配置](#全局配置)

## npm仓库设置

### 单项目仓库设置

1. 在项目目录下创建`.npmrc`文件
2. 在该文件中写入仓库地址
```
registry=http://npm.cloudartifact.szv.dragon.tools.huawei.com/artifactory/api/npm/sz-npm-public 
```

### 全局配置

```
//设置淘宝源
npm config set registry https://registry.npm.taobao.org
//查看源，可以看到设置过的所有的源
npm config get registry
```