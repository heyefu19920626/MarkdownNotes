# Gradle

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