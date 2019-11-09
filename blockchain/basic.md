#### 基本原理

如果把区块链作为一个状态机，则每次交易就是试图改变一次状态，而每次共识生成
的区块，就是参与者对于区块中交易导致状态改变的结果进行确认。

- 基本概念
    + 交易（transaction ）：一次对账本的操作，导致账本状态的 次改变，如添加一条转账记录
    + 区块（ block ）：记录一段时间内发生的所有交易和状态结果，是对当前账本状态的一次共识
    + 链（chain ）：由区块按照发生顺序串联而成，是整个账本状态变化的日志记录

#### 核心

- 一致性(consistency)
    + ，是指对于分布式系统中的多个服务节点，给定一系列操作，在约定协议的保障下，试图使得它们对处理结果达成“某种程度” 的认同
    + 一致性并不代表结果正确与否，而是系统对外呈现的状态一致与否；例如，所有节点都达成失败状态也是一种一致
    + 需要满足
        * 可终止性（termination ）：一致的结果在有限时间内能完成
        * 约同性（ agreement ）：不同节点最终完成决策的结果是相同的
        * 合法性（validity ）：决策的结果必须是某个节点提出的提案
    + <font color='red'>把多件事情进行排序，而且这个顺序还得是大家都认可的</font>
    + 强一致性（严格一致性）,主要包括顺序一致性，线性一致性
    + 弱一致性，最终一致性（ eventual consistency ），即总会存在一个时刻（而不是立刻），让系统达到一致的状态
- 共识(consensus)
    + 描述了分布式系统中多个节点之间，彼此对某个状态达成一致结果的过程,共识算法解决的是对某个提案（ proposal ）大家达成一致意见的过程
    + 对于分布式系统来讲，各个节点通常都是相同的确定性状态机模型（又称为状态机复制问题， state-machine replication ），从相同初始状态开始接收相同顺序的指令，则可以保证相同的结果状态
    + 系统中多个节点最关键的是对多个事件的顺序进行共识，即排序
    + 把出现故障（ crash或fail-stop ，即不响应）但不会伪造信息的情况称为“非拜占庭错误”（ non-byzantine fault ）或“故障错误”（ Crash Fault ）；伪造信息恶 响应的情况称为“拜占庭错误”（ Byzantine Fault ），对应节点为拜占庭节点
    + FLP 不可能原理
        * 在网络可靠，但允许节点失效（即便只有一个）的最小化异步模型系统中，不存在一个可以解决一致性问题的确定性共识算法（ No completely asynchronous consensus protocol can tolerate even a single unannounced process death ）
        * 不要浪费时间，去为异步分布式系统设计在任意场景下都能实现共识的算法
        * 同步是指系统中的各个节点的时钟误差存在上限；并且消息传递必须在一定时间 内完成， 则认为失败 ；同时各个节点完成处理消息的时间是一定的对于同步系统，可以很容易地判断消息是否丢失
        * 异步指系统中各个节点可能存在较大的时钟差异，同时消息传输时间是任意长的，各节点对消息进行处理的时间也可能是任意长的，这就造成无法判断某个消息迟迟没有被响应是哪里出了问题（节点故障还是传输故障？）
    + CAP 原理
        * ：分布式计算系统不可能同时确保以下三个特性： 致性（ Consistency ）、可用性（ Availability ）和分区容忍性（ Partition ），设计中往往需要弱化对某个特性的保证
        * 一致性：任何操作应该都是原子的，发生在后面的事件能看到前面事件发生导致的结果，注意这里指的是强一致性
        * 可用性：在有限时间内，任何非失败节点都能应答请求
        * 分区容忍性：网络可能发生分区，即节点之间的通信不可保障
    + ACID 原则
        * Atomicity （原子性）、 Consistency （一致性）、 Isolation （隔离性）、Durability （持久性）
        * Atomicity ：每次操作是原子的，要么成功，要么不执行
        * Consistency ：数据库的状态是一致的，无中间状态
        * Isolation ：各种操作彼此之间互相不影响
        * Durability ：状态的改变是持久的，不会失效
    + Paxos 算法
        * Proposer （提案者）：提出一个提案，等待大家批准（ chosen ）为结案（ value 系统中提案都拥有一个自增的唯一提案号 往往由客户端担任该角色
        * Acceptor （接受者）：负责对提案进行技票，接受（ accept ）提案 往往由服务端担任该角色
        * Learner （学习者）：获取批准结果，并可以帮忙传播，不参与投 过程 可能为客户端或服务端
        * Safety 约束：保证决议（value ）结果是对的，无歧义的，不会出现错误情况
            - 只有是被 Proposers 提出的提案才可能被最终批准
            - 在一次执行中，只批准（ chosen ）一个最终决议 被多数接受（ accept ）的结果成为决议；
        * Liveness 约束：保证决议过程能在有限时间内完成
            - 决议总会产生，并且学习者能获得被批准的决议
        * 基本过程是多个提案者先争取到提案的权利（得到大多数接受者的支持）；得到提案权利的提案者发送提案给所有人进行确认，得到大部分人确认的提案成为批准的结案
        * 准备阶段
            - 提案者发送自己计划提交的提案的编号到多个接收者，试探是否可以锁定多数接收 者的支持；
            - 接受者时刻保留收到过提案的最大编号和接受的最大提案 如果收到提案号比目前保留的最大提案号还大，则返回自己已接受的提案值（如果还未接受过任何提案，则为空）给提案者，更新当前最大提案号，并说明不再接受小于最大提案号的提案
        * 提交阶段
            - 提案者如果收到大多数的回复（表示大部分人昕到它的请求），则可准备发出带有刚 才提案号的接受消息 如果收到的回复中不带有新的提案，说明锁定成功 则使用 自己的提案内容；如果返回中有提案内容，则替换提案值为返回中编号最大的提案值。 如果没收到足够多的回复，则需要再次发出请求
            - 接受者收到“接受消息”后，如果发现提案号不小于已接受的最大提案号，则接受该提案，并更新接受的最大提案
        * 一旦多数接受者接受了共同的提案值，则形成决议，成为最终确认
    + Raft 算法
        * Raft 算法包括三种角色： Leader（领导者）、Candidate（候选领导者）和 Follower（跟随者），决策前通过选举一个全局的leader来简化后续的决策过程。Leader 角色十分关键，决定日志 (log ）的提交。日志只能由 Leader Follower 单向复制
        * Leader 选举：开始所有节点都是 Follower ，在随机超时发生后未收到来自 Leader或Candidate 消息，则转变角色为 Candidate ，提出选举请求。 最近选举阶段（ Term ）中 得票超过一半者被选为 Leader ；如果未选出，随机超时后进入新的阶段重试。Leader负责从客户端接收log，并分发到其他节点；
        * 同步日志： Leader 会找到系统中日志最新的记录，并强制所有的 Follower 来刷新到这个记录，数据的同步是单向的(此处日志并非是指输出消息，而是各种事件的发生记录)
    + 拜占庭问题与算法
        * 拜占庭容错（ Byzantine Fault Tolerant, BFT ）算法讨论的是在拜占庭情况下对系统如何达成共识
- 基本原理
- 算法
- 分布式系统可靠性指标

#### peer0.org1.example.com节点

- cli 与 orderer 之间的通讯使用 tls 加密,设置环境变量 ORDERER_CA 以作建立握手的凭证

```
ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
```

- 创建名为 mychannel 的 channel

```
peer channel create -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/channel.tx --tls true --cafile $ORDERER_CA 

channel 创建成功后，会在当前目录下生成mychannel.block文件。
每个peer 在向 orderer 发送 join channel 交易的时候，
需要提供这个文件才能加入到 mychannel 中
```

- 把 peer0.org1.example.com 加入到 mychannel 中

```
peer channel join -b mychannel.block
```

- 更新锚节点,对于Org1来说，peer0.org1是锚节点，我们需要连接上它并更新锚节点, mychannel 中 org1 的 anchor peer 的信息

```
peer channel update -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile $ORDERER_CA
或者
peer channel update -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org1MSPanchors.tx --tls true --cafile $ORDERER_CA
```

- 链上代码的安装与运行:安装 chaincode 示例 chaincode_example02 到 peer0.org1.example.com 中

```
peer chaincode install -nmycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```

#### peer1.org1.example.com 节点

- 把 peer1.org1.example.com 加入到 mychannel 中

```
peer channel join -b mychannel.block
```

- 安装 chaincode_example02 到 peer1.org1.example.com 中

```
peer chaincode install -nmycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```

#### org2 的第一个节点 peer2,peer0.org2.example.com节点

- 把peer0.org2.example.com加入到mychannel中

```
peer channel join -b mychannel.block
```

- 更新 mychannel 中 org2 的 anchor peer 的信息

```
# 注意组织的不同
peer channel update -oorderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org2MSPanchors.tx --tls --cafile $ORDERER_CA
```

- 安装 chaincode_example02 到 peer0.org2.example.com

```
peer chaincode install -nmycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```

#### 启动org2的第二个节点 peer3 ，即启动 peer1.org2.example.com

- 把 peer1.org2.example.com 加入到 mychannel 中

```
peer channel join -b mychannel.block
```

- 安装 chaincode_example02 到 peer1.org2.example.com

```
peer chaincode install -n mycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```


#### 运行chaincode

- 实例化chaincode

```
peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile $ORDERER_CA -C mychannel-nmycc -v 1.0 -c '{"Args":["init","a","100","b","200"]}' -P "OR ('Org1MSP.member','Org2MSP.member')"
或者
peer chaincode instantiate -o orderer.example.com:7050 --tls true --cafile $ORDERER_CA -C mychannel -n mycc -v 1.0 -c '{"Args":["init","a","100","b","200"]}' -P "OR      ('Org1MSP.member','Org2MSP.member')"
```

- 在peer0.org1.example.com上查看chaincode的状态，登录到VM1上并进入cli容器内部执行：

```
peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
```

- 转账,把a账户的10元转给b
```
ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

peer chaincode invoke -o orderer.example.com:7050  --tls true --cafile $ORDERER_CA -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'
```
