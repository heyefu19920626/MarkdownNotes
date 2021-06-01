# Git 笔记

- [Git 笔记](#git-笔记)
  - [Git介绍](#git介绍)
  - [Git基础](#git基础)
    - [fork模式保持同步更新](#fork模式保持同步更新)
    - [Git使用代理](#git使用代理)
    - [git命令行中文乱码](#git命令行中文乱码)
    - [git回退](#git回退)
    - [Git配置](#git配置)
    - [查看修改内容](#查看修改内容)
    - [查看提交日志](#查看提交日志)
    - [查看命令历史](#查看命令历史)
    - [版本回退](#版本回退)
      - [回退指定版本号](#回退指定版本号)
    - [丢弃工作区和暂存区的修改](#丢弃工作区和暂存区的修改)
    - [Git忽略仓库中的文件](#git忽略仓库中的文件)
    - [删除文件](#删除文件)
      - [进一步的解释](#进一步的解释)
  - [远程仓库](#远程仓库)
    - [创建SSH Key](#创建ssh-key)
    - [关联远程仓库](#关联远程仓库)
    - [修改远程仓库地址](#修改远程仓库地址)
    - [推送到远程仓库](#推送到远程仓库)
  - [分支](#分支)
    - [创建分支](#创建分支)
    - [查看分支](#查看分支)
    - [切换分支](#切换分支)
    - [创建+切换分支](#创建切换分支)
    - [合并某分支到当前分支](#合并某分支到当前分支)
    - [合并某个分支上的单个commit](#合并某个分支上的单个commit)
    - [合并某个分支上的一系列commits](#合并某个分支上的一系列commits)
    - [合并单个文件到分支](#合并单个文件到分支)
    - [复制文件到分支](#复制文件到分支)
    - [删除分支](#删除分支)
    - [查看分支合并图](#查看分支合并图)
    - [普通模式合并分支](#普通模式合并分支)
    - [保存工作现场](#保存工作现场)
    - [查看工作现场](#查看工作现场)
    - [恢复工作现场](#恢复工作现场)
    - [丢弃一个没有合并过的分支](#丢弃一个没有合并过的分支)
    - [查看远程库信息](#查看远程库信息)
    - [在本地创建和远程分支对应的分支](#在本地创建和远程分支对应的分支)
    - [建立本地分支和远程分支的关联](#建立本地分支和远程分支的关联)
    - [从本地推送分支](#从本地推送分支)
    - [推送特定一次提交到远程分支](#推送特定一次提交到远程分支)
    - [从远程抓取分支](#从远程抓取分支)
  - [标签](#标签)
    - [新建一个标签](#新建一个标签)
    - [指定标签信息](#指定标签信息)
    - [PGP签名标签](#pgp签名标签)
    - [查看所有标签](#查看所有标签)
    - [推送一个本地标签](#推送一个本地标签)
    - [推送全部未推送过的本地标签](#推送全部未推送过的本地标签)
    - [删除一个本地标签](#删除一个本地标签)
    - [删除一个远程标签](#删除一个远程标签)
    - [rebase 合并多个commit](#rebase-合并多个commit)
  - [](#)
    - [git一直卡在Update file 100%](#git一直卡在update-file-100)

## Git介绍
- Git是分布式版本控制系统
- 集中式VS分布式，SVN VS Git
  1. SVN和Git主要的区别在于历史版本维护的位置
  2. Git本地仓库包含代码库还有历史库，在本地的环境开发就可以记录历史而SVN的历史库存在于中央仓库，每次对比与提交代码都必须连接到中央仓库才能进行。
  3. 这样的好处在于：
     - 自己可以在脱机环境查看开发的版本历史。
     - 多人开发时如果充当中央仓库的Git仓库挂了，可以随时创建一个新的中央仓库然后同步就立刻恢复了中央库。
- 特点
    + 分布式
    + 保存点
    + 离线操作
    + 基于快照
    + 分支与合并
    + 分支及时性与灵活性


## Git基础

### fork模式保持同步更新

1. 添加一个远程中心仓库`git remote add center https://github.com/...`
2. 查看是否配置成功`git remote -v`
3. 同步中心仓库的代码`git fetch center`
4. 切换到主分支`git checkout master`
5. 合并`git merge  center/master`
6. 推送到自己的仓库，提MR
   1. 这里推荐使用rebase，因为merge之后，会有记录，然后在提交PR，会很难看

### Git使用代理

1. 单独仓库使用代理
   1. git config http.proxy 'socks5://127.0.0.1:1080'
   2. git config https.proxy 'socks5://127.0.0.1:1080'
2. 全局代理
   1. git config --global http.proxy 'socks5://127.0.0.1:1080'
   2. git config --global https.proxy 'http://user:password@ip:port'
3. 注意替换特殊字符
```
如果密码中有@等特殊字符，会出错，此时要对其中的特殊符号进行处理，使用百分比编码(Percent-encoding)对特殊字符进行转换，转换列表如下：
! --> %21    # --> %23    $ --> %24    & --> %26    ' --> %27
( --> %28    ) --> %29    * --> %2A    + --> %2B    , --> %2C
/ --> %2F    : --> %3A    ; --> %3B    = --> %3D    ? --> %3F
@ --> %40    [ --> %5B    ] --> %5D
```
4. 永久单仓库代理:修改.git目录下的config文件
```
[http]
	proxy = http://username:password@domain:port/
[https]
	proxy = http://username:password@domain:port/
```

### git命令行中文乱码

```bash
git config --global i18n.commitencoding utf-8  --注释：该命令表示提交命令的时候使用utf-8编码集提交
git config --global i18n.logoutputencoding utf-8 --注释：该命令表示日志输出时使用utf-8编码集显示
export LESSCHARSET=utf-8  --注释：设置LESS字符集为utf-8
```

### git回退
1. `git rest --hard commitid`
2. `git push orign master --force` 或者 `git push orign HEAD -force`

### Git配置
```bash
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
``git config``命令的``--global``参数，表明这台机器上的所有Git仓库都会使用这个配置，也可以对某个仓库指定不同的用户名和邮箱地址。

### 查看修改内容

- `git diff` 可以查看工作区(work dict)和暂存区(stage)的区别
- `git diff --cached` 可以查看暂存区(stage)和分支(master)的区别
- `git diff HEAD --<file>` 可以查看工作区和版本库里面最新版本的区别

### 查看提交日志
- `git log`
- 简化日志输出信息
  - `git log --pretty=oneline`

### 查看命令历史
- `git reflog`

### 版本回退
- `git reset --hard HEAD^`
  - 以上命令是返回上一个版本，在Git中，用`HEAD`表示当前版本，上一个版本就是`HEAD^`，上上一个版本是`HEAD^^`，往上100个版本写成`HEAD~100`。

#### 回退指定版本号
- `git reset --hard commit_id`
  - commit_id是版本号，是一个用SHA1计算出的序列

### 丢弃工作区和暂存区的修改
- `git checkout -- <file>`
  - 该命令是指将文件在工作区的修改全部撤销，这里有两种情况：
    - 一种是file自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
    - 一种是file已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。

### Git忽略仓库中的文件

一般可以用.gitignore文件来忽略未加入版本控制的文件，单如果是加入版本控制的文件.gitignore对于这种文件是无能为力的

- 使用`git update-index --skip-worktree <file>`让git在搜索文件列表时，忽略某个文件
- 使用`git ls-files -v . | grep "^S"`查看忽略列表
- 使用`git update-index --no-skip-worktree <file>`不再忽略某个文件

### 删除文件
- `git rm <file>`
  - 相当于执行
    - `rm <file>`
    - `git add <file>`

#### 进一步的解释
Q：比如执行了`rm text.txt` 误删了怎么恢复？  
A：执行`git checkout -- text.txt` 把版本库的东西重新写回工作区就行了  
Q：如果执行了`git rm text.txt`我们会发现工作区的text.txt也删除了，怎么恢复？  
A：先撤销暂存区修改，重新放回工作区，然后再从版本库写回到工作区  
```bash
$ git reset head text.txt
$ git checkout -- text.txt
```
Q：如果真的想从版本库里面删除文件怎么做？  
A：执行`git commit -m "delete text.txt"`，提交后最新的版本库将不包含这个文件  

## 远程仓库
### 创建SSH Key
```bash
$ ssh-keygen -t rsa -C "youremail@example.com"
```
### 关联远程仓库
```bash
$ git remote add origin https://github.com/username/repositoryname.git
```

### 修改远程仓库地址
- 方式1
```
$ git remote set-url origin [url]
```
- 方式2
```
$ git remote rm origin
$ git remote add origin [url]
```
### 推送到远程仓库
```bash
$ git push -u origin master
```
`-u` 表示第一次推送master分支的所有内容，此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改。


## 分支
### 创建分支
```bash
$ git branch <branchname>
```
### 查看分支
```bash
$ git branch
```
`git branch`命令会列出所有分支，当前分支前面会标一个*号。

### 切换分支
```bash
$ git checkout <branchname>
```

### 创建+切换分支
```bash
$ git checkout -b <branchname>
```

### 合并某分支到当前分支
```bash
$ git merge <branchname>
```

### 合并某个分支上的单个commit

把82ecb31这次commit合并到master分支
1. 获取此次commit的id：82ecb31
2. 切换到master分支
3. 使用git cherry-pick命令
```bash
git checkout master  
git cherry-pick 82ecb31
```

### 合并某个分支上的一系列commits
```bash
# 首先需要基于feature创建一个新的分支，并指明新分支的最后一个commit
git checkout featuregit 
git checkout -b newbranch 62ecb3k
# 然后，rebase这个新分支的commit到master（--ontomaster）。76cada^ 指明你想从哪个特定的commit开始
git rebase --ontomaster 76cada^
# 得到的结果就是feature分支的commit 76cada ~62ecb3 都被合并到了master分支
```
### 合并单个文件到分支
```bash
# 只想将feature分支的某个文件f.txt合并到master分支上
git checkout feature 
git checkout --patch master f.txt
# 合并master分支上f文件到feature分支上，将master分支上 f 文件追加补丁到feature分支上 f文件。你可以接受或者拒绝补丁内容
```

### 复制文件到分支
```bash
git checkout master
git checkout feature f.txt
```

### 删除分支
```bash
$ git branch -d <branchname>
```
### 查看分支合并图
```bash
$ git log --graph
```
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。用`git log --graph`命令可以看到分支合并图。

### 普通模式合并分支
```bash
$ git merge --no-ff -m "description" <branchname>
```
因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。合并分支时，加上`--no-ff`参数就可以用普通模式合并，能看出来曾经做过合并，包含作者和时间戳等信息，而fast forward合并就看不出来曾经做过合并。

### 保存工作现场
```bash
$ git stash
```
### 查看工作现场
```bash
$ git stash list
```
### 恢复工作现场
```bash
$ git stash pop
```
### 丢弃一个没有合并过的分支
```bash
$ git branch -D <branchname>
```

### 查看远程库信息
```bash
$ git remote -v
```

### 在本地创建和远程分支对应的分支
```bash
$ git checkout -b branch-name origin/branch-name，
```
本地和远程分支的名称最好一致；

### 建立本地分支和远程分支的关联
```bash
$ git branch --set-upstream branch-name origin/branch-name；
```
### 从本地推送分支
```bash
$ git push origin branch-name
```
如果推送失败，先用git pull抓取远程的新提交；

### 推送特定一次提交到远程分支
> `git push <remotename> <commit SHA>:<remotebranchname>`  
1. `<remotename>` 远程仓库名，默认为origin
2. `<commit SHA>` 提交的唯一码
3. `<remotebranchname>` 远程分支名

如果想要通过上面推送某一个特定的提交，需要保证这个提交之前没有其他的提交了，如果不是，我们可以通过git rebase -i改变提交的位置，使其之前没有其他提交  
参考`git rebase -i`,将该提交移动到首行`:m 0`,rebase后唯一码会变
### 从远程抓取分支
```bash
$ git pull
```
如果有冲突，要先处理冲突。

## 标签
tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。
### 新建一个标签
```bash
$ git tag <tagname>
```
命令`git tag <tagname>`用于新建一个标签，默认为HEAD，也可以指定一个commit id。
### 指定标签信息
```bash
$ git tag -a <tagname> -m <description>
    <branchname> or commit_id
```
`git tag -a <tagname> -m "blablabla..."`可以指定标签信息。
### PGP签名标签
```bash
$ git tag -s <tagname> -m <description>
        <branchname> or commit_id
```
`git tag -s <tagname> -m "blablabla..."`可以用PGP签名标签。
### 查看所有标签
```bash
$ git tag
```
### 推送一个本地标签
```bash
$ git push origin <tagname>
```
### 推送全部未推送过的本地标签
```bash
$ git push origin --tags
```
### 删除一个本地标签
```bash
$ git tag -d <tagname>
```
### 删除一个远程标签
```bash
$ git push origin :refs/tags/<tagname>
```

### rebase 合并多个commit

1. git stash 暂存当前正在进行的工作
2. 开始合并
   1. `git rebase -i HEAD~3`合并从HEAD往上3个版本
   2. `git rebase -i 3a442`指明要合并的版本之前的版本号（3a442这个版本不参与合并） 
3. 执行合并
   1. 执行rebase命令后，会有一个窗口(rebae界面)，将除了第一行行首的pick，其余行首的pick都修改为s,:wq保存退出
   2. 之后是注释修改界面,修改后:wq退出
4. 强制推送到远端
   1. `git push origin master --force`
5. git stash pop 从Git栈中读取最近一次保存的内容
```
pick：保留该commit（缩写:p）
reword：保留该commit，但我需要修改该commit的注释（缩写:r）
edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
squash：将该commit和前一个commit合并（缩写:s）
fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
exec：执行shell命令（缩写:x）
drop：我要丢弃该commit（缩写:d）
```


## 

### git一直卡在Update file 100%

可能是由于git lfs的原因或者代理导致的，
使用`git config --global http.proxy ""`与`git config --global https.proxy ""`置空代理
