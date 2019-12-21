# Gradle

- [Gradle](#gradle)
  - [设置gradle的本地仓库地址](#设置gradle的本地仓库地址)
  - [设置gradle的远程仓库为阿里云](#设置gradle的远程仓库为阿里云)
  - [gradle打包所有依赖和资源文件为可执行文件](#gradle打包所有依赖和资源文件为可执行文件)
- [错误处理](#错误处理)
  - [错误: 找不到或无法加载主类](#错误-找不到或无法加载主类)
  - [错误: 编码GBK的不可映射字符](#错误-编码gbk的不可映射字符)

## 设置gradle的本地仓库地址

配置全局环境变量GRADLE_USER_HOME即可

## 设置gradle的远程仓库为阿里云

在上面配置的全局GRADLE_USER_HOME文件夹下创建init.gradle文件，写入以下内容

```gradle
allprojects{
    repositories {
        def REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public/'
       
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                    remove repo
                }
            }
        }

         maven {
            url REPOSITORY_URL
        }
    }
}
```

## gradle打包所有依赖和资源文件为可执行文件

在build.gradle中加入
```gradle
jar {
    manifest {
        <!-- 此处Main-Class换成自己的主类 -->
        attributes "Main-Class": "com.game.blockchain.SDKTestMain"
    }
    from {
        configurations.compile.collect { it.isDirectory() ? it : zipTree(it) }
    }

    <!-- 下面的exclude排除很多情况下没有必要，但是如果遇到打包依赖后无法加载主类的情况下可以使用 -->
    exclude('LICENSE.txt', 'NOTICE.txt', 'rootdoc.txt')

    exclude 'META-INF/*.RSA', 'META-INF/*.SF', 'META-INF/*.DSA'

    exclude 'META-INF/NOTICE', 'META-INF/NOTICE.txt'

    exclude 'META-INF/LICENSE', 'META-INF/LICENSE.txt'

    exclude 'META-INF/DEPENDENCIES'
}
```

# 错误处理

## 错误: 找不到或无法加载主类

打开已打好的jar包看META-INF文件夹下的文件是否正常,其中MANIFEST.MF文件是否正常包含了主类信息,如果没有包含，则在build.gradle文件中加入
```gradle
jar {
    manifest {
        <!-- 此处Main-Class换成自己的主类 -->
        attributes "Main-Class": "com.game.blockchain.SDKTestMain"
    }
    from {
        configurations.compile.collect { it.isDirectory() ? it : zipTree(it) }
    }
}
```
如果已有，那么查看META-INF文件夹下是否有很多*.RSA文件等多余文件，如果有，在上面的jar{}使用exclude排除，是因为[gradle打包的jar有一种签名的机制，无法直接java -jar执行](https://www.cnblogs.com/Zhongzz/p/10215097.html)
```gradle
jar {

    ...

    <!-- 下面的exclude排除很多情况下没有必要，但是如果遇到打包依赖后无法加载主类的情况下可以使用 -->
    exclude('LICENSE.txt', 'NOTICE.txt', 'rootdoc.txt')

    exclude 'META-INF/*.RSA', 'META-INF/*.SF', 'META-INF/*.DSA'

    exclude 'META-INF/NOTICE', 'META-INF/NOTICE.txt'

    exclude 'META-INF/LICENSE', 'META-INF/LICENSE.txt'

    exclude 'META-INF/DEPENDENCIES'
}
```

## 错误: 编码GBK的不可映射字符

gradle编译代码的编码有问题，在build.gradle加入
```gradle
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}
```