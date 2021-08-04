# LAB2



## LAB2.1

- - [x] You can't easily run your Raft implementation directly; instead you should run it by way of the tester, i.e. `go test -run 2A`.
- - [x] Follow the paper's Figure 2. At this point you care about 
    - [ ] sending and receiving RequestVote RPCs, 
    - [ ] the Rules for Servers that relate to elections,
    - [ ]  and the State related to leader election,
- - [x] Add the Figure 2 state for leader election to the `Raft` struct in `raft.go`. 
  - [x] You'll also need to define a struct to hold information about each log entry.
- - [x] Fill in the `RequestVoteArgs` and `RequestVoteReply` structs. 
- Modify `Make()` to create a background goroutine that will kick off leader election periodically by sending out `RequestVote` RPCs when it hasn't heard from another peer for a while. 
  - This way a peer will learn who is the leader, if there is already a leader,
  - or become the leader itself. 
  - Implement the `RequestVote()` RPC handler so that servers will vote for one another.



- To implement heartbeats, define an `AppendEntries` RPC struct (though you may not need all the arguments yet), and have the leader send them out periodically. Write an `AppendEntries` RPC handler method that resets the election timeout so that other servers don't step forward as leaders when one has already been elected.
- Make sure the election timeouts in different peers don't always fire at the same time, or else all peers will vote only for themselves and no one will become the leader.
- The tester requires that the leader send heartbeat RPCs no more than ten times per second.
- The tester requires your Raft to elect a new leader within five seconds of the failure of the old leader (if a majority of peers can still communicate). Remember, however, that leader election may require multiple rounds in case of a split vote (which can happen if packets are lost or if candidates unluckily choose the same random backoff times). You must pick election timeouts (and thus heartbeat intervals) that are short enough that it's very likely that an election will complete in less than five seconds even if it requires multiple rounds.
- The paper's Section 5.2 mentions election timeouts in the range of 150 to 300 milliseconds. Such a range only makes sense if the leader sends heartbeats considerably more often than once per 150 milliseconds. Because the tester limits you to 10 heartbeats per second, you will have to use an election timeout larger than the paper's 150 to 300 milliseconds, but not too large, because then you may fail to elect a leader within five seconds.
- You may find Go's [rand](https://golang.org/pkg/math/rand/) useful.
- You'll need to write code that takes actions periodically or after delays in time. The easiest way to do this is to create a goroutine with a loop that calls [time.Sleep()](https://golang.org/pkg/time/#Sleep). Don't use Go's `time.Timer` or `time.Ticker`, which are difficult to use correctly.
- Read this advice about [locking](http://nil.csail.mit.edu/6.824/2020/labs/raft-locking.txt) and [structure](http://nil.csail.mit.edu/6.824/2020/labs/raft-structure.txt).
- If your code has trouble passing the tests, read the paper's Figure 2 again; the full logic for leader election is spread over multiple parts of the figure.
- Don't forget to implement `GetState()`.
- The tester calls your Raft's `rf.Kill()` when it is permanently shutting down an instance. You can check whether `Kill()` has been called using `rf.killed()`. You may want to do this in all loops, to avoid having dead Raft instances print confusing messages.
- A good way to debug your code is to insert print statements when a peer sends or receives a message, and collect the output in a file with `go test -run 2A > out`. Then, by studying the trace of messages in the `out` file, you can identify where your implementation deviates from the desired protocol. You might find `DPrintf` in `util.go` useful to turn printing on and off as you debug different problems.
- Go RPC sends only struct fields whose names start with capital letters. Sub-structures must also have capitalized field names (e.g. fields of log records in an array). The `labgob` package will warn you about this; don't ignore the warnings.
- Check your code with `go test -race`, and fix any races it reports.





## Introduction

build a fault-tolerant key/value storage system.

implement Raft, a replicated state machine protocol. 

build a key/value service on top of Raft

 “shard” your service over multiple replicated state machines for higher performance.

A replicated service achieves fault tolerance by storing complete copies of its state (i.e., data) on multiple replica servers. 

Replication allows the service to continue operating even if some of its servers experience failures (crashes or a broken or flaky network). 

The challenge is that failures may cause the replicas to hold differing copies of the data.

基于Raft 一种复制状态机协议建议了一个容错性的k/v存储系统, 并且将服务分片到不同的复制状态机上实现了高可用

- 复制系统通过将所有的数据存储在多个复制的服务器上实现容错性
- 复制允许即便一些服务器崩溃, 或因为网络阻塞无法提供有效的服务, 仍然能继续服务
- 挑战的地方在于一些崩溃的服务会造成数据的不一致性

Raft organizes client requests into a sequence, called the log, and ensures that all the replica servers see the same log. 

Each replica executes client requests in log order, applying them to its local copy of the service's state.

Since all the live replicas see the same log contents, they all execute the same requests in the same order, and thus continue to have identical service state. 

If a server fails but later recovers, Raft takes care of bringing its log up to date. 

Raft will continue to operate as long as at least a majority of the servers are alive and can talk to each other. 

If there is no such majority, Raft will make no progress, but will pick up where it left off as soon as a majority can communicate again.

Raft将客户的请求串行化变成日志, 保证了所有的复制服务器看到的都是同样的日志

- 所有的复制服务器按顺序执行这些请求, 并将执行后的数据保存到本地的服务器状态的拷贝
- 因为所有现阶段存活的复制服务器都看到同样的日志内容, 并且按照同样的请求顺序执行, 所以他们的数据状态都是相同的
- 如果存在一个服务器失败后重连, raft会负责将这个服务器的日志更新到最新
- raft会保持一直可用只要超过半数的raft实例都出存活状态并且互相之间可以通信
- 如果没有超过半数的raft实例存活, raft会保持在这个状态, 直到这个条件满足就会马上继续提供服务

In this lab you'll implement Raft as a Go object type with associated methods, meant to be used as a module in a larger service. 

A set of Raft instances talk to each other with RPC to maintain replicated logs.

Your Raft interface will support an indefinite sequence of numbered commands, also called log entries. 

The entries are numbered with *index numbers*. The log entry with a given index will eventually be committed. At that point, your Raft should send the log entry to the larger service for it to execute.

Lab2 会通过Go对象类型以及相关的方法实现Raft算法

- 一些raft实例通过RPC互相通信来复制日志
- raft接口可以实现一些列编号的命令(日志项)
- 当日志项提交后, 就会将这个日志项发送到更大的服务去执行

所有的raft设计都是基于 extended raft paper这篇论文的内容实现的

http://nil.csail.mit.edu/6.824/2020/papers/raft-extended.pdf

- 保存持久化的数据状态
- 失败重连后 读取持久化数据状态并且重新开始
- 实现日志压缩, 快照

## LAB2A

- 实现Raft 领头的选取和心跳机制
  - 目标是实现单个raft领头的选取, 
  - 如果没有任何失败, 保持这个领头的状态
  - 如果旧的领头失败或者发送的包丢失了导致的通信阻塞, 选取新的领头



## LAB2B

- 实现raft领头和跟随者能够将新的日志添加到自己的日志项数组



## LAB2C

- 实现将数据持久化的功能
- 当对持久化数据状态改变的时候调用persist()函数持久化新的状态





# LAB3

In this lab you will build a fault-tolerant key/value storage service using your Raft library from [lab 2](http://nil.csail.mit.edu/6.824/2020/labs/lab-raft.html). 

Your key/value service will be a replicated state machine, consisting of several key/value servers that use Raft for replication. 

Your key/value service should continue to process client requests as long as a majority of the servers are alive and can communicate, in spite of other failures or network partitions.

使用Raft 库构建一个容错性的k/v存储服务

- k/v服务是一个复制状态机, 由多个使用raft复制的k/v服务器组成
- k/v服务在超过一半服务器存活并可以通信的情况可以处理客户的请求

The service supports three operations: `Put(key, value)`, `Append(key, arg)`, and `Get(key)`. 

It maintains a simple database of key/value pairs. Keys and values are strings. 

`Put()` replaces the value for a particular key in the database, 

`Append(key, arg)` appends arg to key's value, 

`Get()` fetches the current value for a key. 

A `Get` for a non-existant key should return an empty string.

An `Append` to a non-existant key should act like `Put`. 

Each client talks to the service through a `Clerk` with Put/Append/Get methods. 

A `Clerk` manages RPC interactions with the servers.

服务提供三个操作: `Put(key, value)`, `Append(key, arg)`,  `Get(key)`. 

- 维护一个简单的k/v数据库, 键值都是字符串
- Put() 替换数据库中某个key的value
- append() 添加一个args到key的value上
  - 如果append(key, args) key不存在等同于 put(key, args)
- get() 获取key的value
  - 如果获取的key不存在, 返回一个空字符串
- 每个客户通过一个拥有Put/Append/Get方法的Clerk与服务器通信
- Clerk管理和服务器的RPC通信



Your service must provide strong consistency to application calls to the `Clerk` Get/Put/Append methods. 

Here's what we mean by strong consistency. 

If called one at a time, the Get/Put/Append methods should act as if the system had only one copy of its state, and each call should observe the modifications to the state implied by the preceding sequence of calls. 

For concurrent calls, the return values and final state must be the same as if the operations had executed one at a time in some order. 

Calls are concurrent if they overlap in time, for example if client X calls `Clerk.Put()`, then client Y calls `Clerk.Append()`, and then client X's call returns. Furthermore, a call must observe the effects of all calls that have completed before the call starts (so we are technically asking for linearizability).

Strong consistency is convenient for applications because it means that, informally, all clients see the same state and they all see the latest state. Providing strong consistency is relatively easy for a single server. It is harder if the service is replicated, since all servers must choose the same execution order for concurrent requests, and must avoid replying to clients using state that isn't up to date.

对于客户的Get/Put/Append 方法调用 服务必须提供强一致性

- 调用一次Get/Put/Append 方法, 就好像系统只有一份数据的拷贝, 每一个调用都能看到前一个调用对于数据库的改变
- 对于并发的调用Get/Put/Append 方法, 返回值和最终的数据库状态就好像所有的请求都是串行化执行的结果
  - 并发的调用表示, 他们的调用过程会重叠
  - 但是一个调用之前所有已经完成的调用的结果对于当前调用都是可见的(线性一致性)
- 强一致性给应用带来了便利
  - 对于所有的客户来说都见到了同样的状态, 并且他们都看到了最新的状态
  - 强一致性对于单服务器来说很容易, 但是对于多个复制的集群非常困难, 因为所有的结点对于并发的请求必须选择同样的执行顺序, 并且避免使用过时的状态回复客户







## LAB3A

### client.go

Put/Append/Get



### server.go

PutAppend() and Get()

Op



不需要考虑raft log 会无限增长导致的问题

- 首先实现可用的服务, 没有丢失的消息, 失败的结点
- 添加RPC-发送代码到Clerk Put/Append/Get方法, client.go
- 实现PutAppend() Get() RPC处理器 server.go
- 这些处理器应该使用start() 进入 raft log中的 Op  
- 实现 Op结构体, server.go , 描述  Put/Append/Get操作
- 当Raft提交每个结点都需要执行 Op 命令

kvservers <---------> Raft peer

Clerk --> send Put/Append/Get RPCs to --> kvserver <-----------> raft peer --> raft log hold sequence Put/Append/Get operation

- 所有的kvservers从raft log 中按顺序执行命令
- 将命令应用到k/v数据库, 
- 这样可以保证服务器结点都可以维持一致的k/v数据库

Clerk 有时候不知道那一个kvserver是raft leader, 

- 如果Clerk发送RPC到错误的kvserver, 或者不能到达kvserver, Cleark应该重传RPC到不同的kvserevr
- 如果k/v服务提交了这个请求命令到自己的Raft log(应用到了自己的k/v状态机) leader会通过回应RPC来通知Cleark
- 如果操作不能提交(比如leader被替换了,) 服务器回复错误, Cleark重试另一个server

修改使得可以应对网络服务器的失败

- Clerk会重复发送一个RPC多次直到得到一个正面的回应
- 如果一个leader在刚刚提交请求到自己的Raft log 就刚好失败了, Cleark也许不会收到回复, 所以会再次发送同样的请求到另一个leader
- 每次Put Append应该只会导致一次执行, 所以需要保证命令的重发不会导致服务器执行一个命令两次



### 需要处理的边界情况

a leader that has called Start() for a Clerk's RPC, but loses its leadership before the request is committed to the log.

- you should arrange for the Clerk to re-send the request to other servers until it finds the new leader.

- One way to do this is for the server to detect that it has lost leadership, by noticing that a different request has appeared at the index returned by Start(), 
- or that Raft's term has changed. 
- If the ex-leader is partitioned by itself, it won't know about new leaders; but any client in the same partition won't be able to talk to a new leader either, so it's OK in this case for the server and client to wait indefinitely until the partition heals.

have to modify your Clerk to remember which server turned out to be the leader for the last RPC, and send the next RPC to that server first. 

- This will avoid wasting time searching for the leader on every RPC, which may help you pass some of the tests quickly enough.

You will need to uniquely identify client operations to ensure that the key/value service executes each one just once.

Your scheme for duplicate detection should free server memory quickly, 

- for example by having each RPC imply that the client has seen the reply for its previous RPC. 
- It's OK to assume that a client will make only one call into a Clerk at a time.









## LAB3B

实现快照, raft 会抛弃旧的log, 













# Go 

## Printf 格式化输出

```
v     值的默认格式。
%+v   添加字段名(如结构体)
%#v　 相应值的Go语法表示 
%T    相应值的类型的Go语法表示 
%%    字面上的百分号，并非值的占位符　

%t   true 或 false

%b     二进制表示 
%c     相应Unicode码点所表示的字符 
%d     十进制表示 
%o     八进制表示 
%q     单引号围绕的字符字面值，由Go语法安全地转义 
%x     十六进制表示，字母形式为小写 a-f 
%X     十六进制表示，字母形式为大写 A-F 
%U     Unicode格式：U+1234，等同于 "U+%04X"

%b     无小数部分的，指数为二的幂的科学计数法，与 strconv.FormatFloat中的 'b' 转换格式一致。例如 -123456p-78 
%e     科学计数法，例如 -1234.456e+78 
%E     科学计数法，例如 -1234.456E+78 
%f     有小数点而无指数，例如 123.456 
%g     根据情况选择 %e 或 %f 以产生更紧凑的（无末尾的0）输出 
%G     根据情况选择 %E 或 %f 以产生更紧凑的（无末尾的0）输出

%s     字符串或切片的无解译字节 
%q     双引号围绕的字符串，由Go语法安全地转义 
%x     十六进制，小写字母，每字节两个字符 
%X     十六进制，大写字母，每字节两个字符

%p     十六进制表示，前缀 0x
```





## log

### log.Fatal

```go
func Fatal
func Fatal(v ...interface{})
Fatal is equivalent to Print() followed by a call to os.Exit(1).
```



# 什么是RPC





# Raft总结

https://juejin.cn/post/6907151199141625870



http://nil.csail.mit.edu/6.824/2020/papers/raft-extended.pdf

## 目的

实现了Raft算法(一种复制状态机协议的)

基于Raft算法实现了提供k/v服务的存储系统

将服务分片到多个复制状态机, 以此获得更高的性能

## 实现

- 在分布式系统中，为了消除单点提高系统可用性，通常会使用副本来进行容错，但这会带来另一个问题，即如何保证多个副本之间的一致性？

- 所谓的强一致性（线性一致性）并不是指集群中所有节点在任一时刻的状态必须完全一致，而是指一个目标，即让一个分布式系统看起来只有一个数据副本，并且读写操作都是原子的，这样应用层就可以忽略系统底层多个数据副本间的同步问题。也就是说，我们可以将一个强一致性分布式系统当成一个整体，一旦某个客户端成功的执行了写操作，那么所有客户端都一定能读出刚刚写入的值。即使发生网络分区故障，或者少部分节点发生异常，整个集群依然能够像单机一样提供服务。

- 那么当我们向 Raft 集群发起一系列读写操作时，集群内部究竟发生了什么呢？

  - 首先，Raft 集群必须存在一个主节点（leader），我们作为客户端向集群发起的所有操作都必须经由主节点处理。
  - 所以 Raft 核心算法中的第一部分就是**选主**（**Leader election**）——没有主节点集群就无法工作，先票选出一个主节点，再考虑其它事情。

  - 其次，主节点需要承载什么工作呢？
    - 它会负责接收客户端发过来的操作请求，将操作包装为**日志**同步给其它节点，
    - 在保证**大部分**(>=n/2+1)节点都同步了本次操作后，将这个log标记为commited, 然后可以apply到自己本地的状态机上
    - 通过状态的机上的数据就可以安全地给客户端回应响应了。
    - 这一部分工作在 Raft 核心算法中叫**日志复制**（**Log replication**）。



# LAB2

## 目的

- 将Raft作为一个Go对象类型, 它具有一些属性, 以及一些方法, 他作为一个模块用在更大的服务系统中
- Raft之间通过RPC通信来进行日志复制
  - test case 可以通过延迟, 打乱, 丢弃这些RPC来模拟网络失败的各种情况, 



## 2A

- 每个term都只有一个leader被选举出来
- 在没有任何失败的情况下 这个leader会保持leader的状态
- 如果leader失败, 或者leader和follower之间的RPC通信因为网络原因没有及时到达或丢失就会有开始新的选主, 选择一名新的leader

### 生成Raft

#### Make(peers, me, ...)

生成Raft peers

- peers 一个数组, 

  - 数组中的每个元素都是一个raft peer 的网络标识, 
  - 各个raft之间互相进行RPC传输的通道, 
  - 通过数组的下标可以和指定的raft peer RPC通信

  

#### Make流程

##### 生成一个Raft结点, 初始化Raft对象的属性

- 属性
  - peers[] 提供的包括所有raft结点的数组, 通过数组的索引向对应的raft结点发送RPC
  - persister 提供的persister用来持久化当前Raft状态数据到对应的Persister对象
  - me 当前raft在peers数组中的索引
  - currentTerm 表示当前的leader的任期, 每进行一次选举都会将term+1
  - votedFor 表示当前raft选择投票的raft在peers数组中的索引 没有投票为-1
  - logEntries 表示当前raft的本地日志集合
- chan 管道
  - applyCh 提供的chan用来传送每一个已经提交的日志条目(log entry)
  - stopCh 从这个chan收到消息会停止当前raft
  - notifyCh 通过这个chan leader和follower之间可以传递开始apply log到本地状态机的消息
- Timer 计时器
  - electionTimer[] 表示每个raft对应的选举计时器, 
    - 当每个raft收到leader传来的RPC 就会重置
    - 计时器结束说明当前leader可能失效, 就会自己开始新一轮的选举
  - appendEntriesTimer[] leader会要求所有的follower将日志项(leader收到的client的请求封装成的日志项)添加到follower本地的日志集合的计时器
    - 先将每个raft peer的这个timer都初始化
    - 这个计时器结束就开始将日志项添加到本地的日志集合
  - applyTimer 这个计时器结束当前raft就开始将本地的日志应用到本地的状态机

##### 三个gorountine分别用来 apply log,  request vote,  要求follower复制leader的log

- Apply log  
  - 等待三个chan或timer的消息
  - stopCh
  - applyTimer.C
    - 这个计时器到时间就像notifyCh发送一个空的struct{}{}消息, 表示可以开始apply log到本地状态机
  - notifyCh
    - 收到这个chan的消息就开始将本地的日志集合apply到本地状态机

- request vote
  - 等待 electionTimer到时间
  - 收到这个计时器的消息就开始自己改变状态称为candidate term+1 向其他peers发送要求向自己投票的RPC
- 要求follower开始复制leader的日志
  - 等待每一个peer的appendEntriesTimer到时间, 开始向对应的raft发送append Entries的RPC



#### Start(command)

要求当前raft将收到的client请求封装成一个logEntry并添加到本地的日志集合中

- 这个过程必须是leader才可以做, 因为只有leader才接受客户的请求
- logEntry中包括 
  - 当前的term
  - 当前的日志应该被放入日志集合的位置索引(上一次append的index+1)
  - Command收到的客户的请求命令
- 持久化状态因为更新的状态
- 重置心跳计时器

### 选主 (raft_vote)

#### 开始选举 startElection

1. 重置electionTimer计时器
2. 如果当前raft是leader, 当前raft不参与选举
3. 如果当前raft不是leader
   1. 更换角色到Candidate (changeRole)
   2. 获取当前raft的term, 在peer数组中的index, 日志集合最新的logIndex, 上一次添加日志的term, 讲这些数据封装成RequestVoteArgs结构体
4. 新建一个voteChan chan[] 收集其他raft对于自己的requestVote RPC是否投票的回复, 通过这个chan统计有多少raft投票给自己进而决定自己是否能称为leader
5. 遍历peers[]中每一个raft peer, 发送RequestVote(sendRequestVoteToPeer) RPC 请求
   - 获得对应raft对于当前请求的回复
     1. reply.VoteGranted 发送到 voteChan 会缓存, 后面自己接收
     2. reply.Term>当前raft的term, 说明自己不是拥有最全log的raft
        1. 将自己的term更新成reply.Term
        2. 更换角色到follower
        3. 重置electionTimer
6. 等待voteChan, 
   1. 每次收到一个voteChan=true的消息, 就将收到的投票总数的计数器+1
   2. 当已经完全收到所有raft的回复, 或者 同意或者不同意投票给自己的数量超过一半,就结束等待, 因为这个时候结果已经确定
7. 如果小于等于一半的raft投票给自己, 结束
8. 如果超过一半的raft投票给自己, 将自己变成leader并且重置自己的electionTimer防止马上又进入了新的选举



#### 发送requestVote, sendRequestVoteToPeer(发送方)

1. 首先设置两个计时器用 
   1. totalTimer 向这个peer发送RPC消耗的总时间 因为可能发送多次RPC才得到结果
   2. rpcTimer 统计单次RPC的时间
2. 循环发送RPC直到得到回复
   1. 每一轮的开始都重置rpcTimer 这样来限制本次RPC的时间
   2. 新建一个replyChan来收集这个raft的回复
   3. 通过labrpc.call方法传入raft的index, args reply 来进行RPC通信
   4. 把得到的结果发送到replyChan 自己在后面接收
   5. 等待消息
      1. 如果totalTimer结束说明本次RPC通信到达最大时间限制, 结束本次RPC
      2. 如果rpcTimer结束说明本轮循环没有得到回复, 进入下一轮
      3. 如果收到replyChan的消息, 检查这个消息是否是true, 
         1. 如果false, 说明这次RPC没有成功, 进入下一轮
         2. 如果true, 将这个raft回复的term和表示是否投票的voteGranted通过reply结构体返回给发起requestVote的candidate

#### 接收requestVote RequestVote(接收方, 处理requestVote)

1. 先在回复的结构体reply里放入自己的term信息
2. 比较请求方的term和接收方的term
   1. 请求方.term < 接收方.term
      1. 直接返回, 因为不会向log不如自己新的raft投票, 这样保证了成为leader的人一定拥有最新的log
   2. 请求方.term = 接收方.term
      1. 如果接收方已经是leader就返回
      2. 如果接收方刚好投票给了请求方, voteGranted=true并返回
      3. 如果接收方已经投票了并且没有投给这个请求方, 返回, 一个peer一轮选举只会投票给一个raft
   3. 请求方.term > 接收方.term
      1. 接收方更新自己的term为请求方的term
      2. 更换角色为Follower(因为当前接受方也可能是一个candidate身份, 但是发现了一个比自己log更新的raft就不会继续参与选举)
3. 比较请求方最新的log对应的LogTerm和LogIndex
   1. 如果请求方.logTerm < 接受方.logTerm
   2. 或者logTerm一样, 请求方.logIndex < 接受方.logIndex 都会返回因为不会投票给一个log落后于自己的人
4. 到这一步说明请求方的log不会落后于接收方, 
   1. 接收方更新自己的term为请求方的term
   2. 投票给请求方
   3. 改变身份为follower
   4. 重置选举计时器

#### changeRole 更换当前raft的角色为指定的角色

- 首先是改变自己的role为指定的role
  - Follower什么都不用做
  - Candidate (开始选举)
    - 将当前raft的term+1
    - votedFor=rf.me 首先自己会投票给自己
    - 重置选举计时器
  - Leader (选举结束)
    - 新建 nextIndex[] 
      - 标记每个follower下次应该append log到日志集合数据的索引 
      - 等于当前leader上次logIdnex+1
    - 新建 matchIndex[]
      - 标记follower与自己logEntry数组同步的最新的index
    - 重置选举计时器







### 添加日志条目 AppendEntry

#### 发送 AppendEntry RPC appendEntriesToPeer(发送方)

1. 设置本次RPC通信的计时器, 超过时间没有得到回复直接返回
2. 检查当前raft是否是leader
   1. 不是leader直接返回
3. 将当前raft的信息封装成AppendEntriesArgs结构体
   1. 当前的term, 
   2. peers[]中的索引, 
   3. 最新的logTerm logIndex
   4. logEntry整个日志集合,
      1. 如果接收方的日志和当前leader差了很多就需要更新到和当前raft一致
   5. LeaderCommit leader已经提交的日志Index
4. 开始准备发送appendEntry RPC每轮的开始重置RPC Timer
5. 创建 replyCh chan bool 用来接收RPC的回复, 查看是否成功
6. 通过labrpc.Call方法向指定的peer发送 append Entry RPC
7. 将得到的回复发送到replyCh 自己在后面接收
8. 等待消息
   1. RPCTimer
      1. 说明本次RPC通信超时, 继续下一轮
   2. replyCh 
      1. 本次RPC通信follower的回复
      2. 如果为false 继续下一轮
9. RPC通信成功 处理接收方的回复
   1. 如果接收方.term > 发送方.term
      1. 发送方raft变成Follower
      2. 重置选举计时器
      3. 更新发送方raft.term为接收方.term
   2. 接收方回复成功
      1. 更新接收方raft在nextIndex[] matchIndex[]中对应的index
      2. 当前raft最新的logTerm是当前term, 提交这个新的log到本地的状态机
   3. 如果出现接收方回复的最新的logIndex<上次快照的logIndex
      1. 说明这个接收方日志还停留在上次快照之前, 需要先安装上次快照的数据

#### 接收 ApplyEntries RPC 处理收到的RPC

1. 判断请求方的term, 
   1. 小于接收方的term 拒绝append log
2. 将自己的term更新为请求方的term
   1. 请求方的term至少和自己一样, 
3. 自己变成Follower
4. 重置选举计时器
5. 根据请求方最新的logIndex, 上次快照logIndex, 接收方最新的logIndex, 决定回复的信息
   1.  args.PrevLogIndex < rf.lastSnapshotIndex 
      1. 拒绝, 请求方的logIndex落后于上次快照
   2. args.PrevLogIndex > lastLogIndex 
      1. 拒绝, 请求方的log和接收方的log存在gap, 不一致
   3. args.PrevLogIndex == rf.lastSnapshotIndex 
      1. 同意, 当前的leader是快照后第一次请求append Entry
      2. 将请求方的发送过来的日志集合的第一个添加到接收方的日志集合
   4. 请求方最新的logTerm = 接收方通过请求方args.logIndex在自己的日志集合中找到的logIndex对应的term相等
      1. 说明请求方接收方之前的log都匹配, 可以append Entry
      2. 从匹配的log后面开始更新



#### updateCommitIndex 提交当前term本地log到状态机

1. 遍历上一次提交的commitIndex到当前最新的logIndex
   1. 对于每个logIndex,
      1. 遍历所有的raft peer的matchIndex m
      2. 如果m >当前的logIndex, 计数器+1
      3. 当计数器超过一半时, 这个logIdnex可以提交
      4. 并且更新当前raft.comitIndex=当前提交的logIndex
   2. 当不能提交的时候结束循环
2. 提交过log以后向notifyCh发送一个空消息表示提交了日志

### 心跳机制

#### AppendEntries RPC

有两个作用, 

- 一个是leader要求其他节点复制结构体中包含的日志到各个结点本地的log Entry
- 另一个是不带任何数据的AppendEntries 作为心跳机制的实现
  - 如果一个结点收到leader发来的空的AppendEntries
  - 这个结点就会重置关于选举的定时器
  - 这个RPC会周期性的发送给各个结点作为心跳机制的实现, 而各个结点也都有响应的handler来处理这个心跳(重置选举的定时器)

#### election timeout (150~300ms)

这个定时器的重置需要在很小的范围内设置成随机数, 

- 这样不会出现大部分的peers在同一时间过期, 导致都转变为candidates 互相向所有的peers发送RequestVote RPC 
- 这样可能会导致follower很少或者投票很分散无法达到一个candidate得到查过一半的票数, 也就会一致停留在选主而无法提供服务的阶段

- 通过随机数的方法可以很好的解决一定会很快的选出一个leader

这个定时器需要设置的足够小, 才能避免才一个leader失效后可以在比较短的时间内选出下一个leader

- 如果长时间没有leader就会造成无法提供服务这是不能接受的
- 一次leader的选举过程可能需要经历很多轮, 因为即便每个raft election timeout 都不同, 还是一轮会有多个candidate, 进而导致投票的分散
- 那么我们就需要将这个election timeout尽可能的短, 这样一旦超时就可以迅速进行选主阶段, 
- 而不是等待很长时间的election timeout 才发现当前leader已经失效了, 才开始选主, 这样因为比较长的election timeout就会导致从上一个leader失效到下一个leader选出来开始接受服务的时间很长



### 应用日志到状态机 (Apply Log)

applyTimer.c计时器结束机会主动开始apply logEntry 到本地的状态机

1. 需要将从上一次lastAppliedlog 到当前commitIndex中间的logEntries都需要apply, 这些都已经标记为提交, 所以可以提交
2. 如果 rf.lastApplied < rf.lastSnapshotIndex 
   1. 说明当前raft落后于上次快照的状态
   2. 需要创建一个ApplyMsg结构体, 
      1. commandValid = false, command=updateBySnapshot, commandIndex = lastSnapshotIndex
3. 如果 rf.commitIndex <= rf.lastApplied
   1. 说明当前已提交的log落后于上次已经applied过的
   2. 不apply
4. 否则说明当前应该更新 [rf.lastApplied+1:rf.commitIndex ]范围内的logEntry
   1. 创建区间长度的ApplyMsg[]数组
   2. 遍历区间的每个logEnrty 创建一个ApplyMsg结构体,
   3. 传入logEntry中的信息
5. 遍历每个ApplyMsg[]数组中的ApplyMsg, 发送到applyCh
6. 更新rf.lastApplied为最后一个apply的index







## 2B

实现leader和follower的 将新的日志append到本地的日志集合中(log entries)中

### 选主的限制(election restriction)(5.4.1)

- **每个 candidate 必须在 RequestVote RPC 中携带自己本地日志的最新 (term, index)，如果 follower 发现这个 candidate 的日志还没有自己的新，则拒绝投票给该 candidate。**

- Candidate 想要赢得选举成为 leader，必须得到集群大多数节点的投票，那么**它的日志就一定至少不落后于大多数节点**。又因为一条日志只有复制到了大多数节点才能被 commit，因此**能赢得选举的 candidate 一定拥有所有 committed 日志**。


因此, Follower 不可能比 leader 多出一些 committed 日志。

比较两个 (term, index) 的逻辑非常简单：如果 term 不同 term 更大的日志更新，否则 index 大的日志更新。

#### RequestVote RPC

- 在一段限定的时间内没有收到leader的消息, 这个结点会认为leader已经失效了
  - 自己身份变成candidate 
  - term+1
  - 开始向其他所有的raft peers 发出 requestVote RPC, 要求其他的peers给自己投票
  - 如果这个结点收到 >=n/2+1 的投票, 就会自动当选leader 并且立刻向其他所有的peers发送心跳(不带参数的AppendEntries)来维持自己leader的地位



## 2C

当一个服务结点回复的时候, 需要恢复到从上次断线的状态, 这就要求Raft必须有持久化机制

### 持久化(persister)

- 真实的实现是将Raft的状态写入磁盘, 恢复的时候从磁盘读取出来,
- 这里我们将持久化状态保存到Persister对象中, 在创建Raft的时候就会传入一个Persister参数, 用来保存这个Raft的持久化数据
  - Raft从Persister中初始化状态, 
    - ReadRaftState()
  - 每次状态的改变都会将最新的状态保存到 Persister中
    -  SaveRaftState()

#### SavePersistAndSnapshot 保存快照

1. logIndex < rf.lastSnapshotIndex
   1. 拒绝保存, 当前请求保存的数据不如上次快照的数据新, 
2. logIndex > rf.commitIndex
   1. 拒绝保存, 当前请求保存的数据中含有未提交的数据, 这些数据没有被复制到超过一半的raft peer中
3. 可以保存
   1. 本地日志集合中以及快照的数据可以删除
   2. 更新lastSnapshotIndex  lastSnapshotTerm
   3. 持久化当前raft的状态
      1. term commitIndex, logEntries...
   4. 调用persister的持久化方法保存raft状态数据和快照数据
      1. 快照数据是上一次快照到当前提交的数据之间的数据



#### sendInstallSnapshot

向指定的raft peer发送自己的快照数据要求其将logEntry更新快照数据

1. 获取自己的快照数据, 封装成InstallSnapshotArgsh结构体
   1. term leaderId, lastSnapshotIndex, lastSnapshotTerm Data
2. 设置RPCTimer 作为本次RPC通信每轮的时间限制
3. 需要循环发送RPC直到对方安装快照成功, 
4. 建立replyCh chan bool, 用来接收raft peer对于本次RPC通信的回复
5. 通过labrpc.call 进行发送方和接收方的通信
6. 将通信的回复结果发送到replyCh 自己在后面接收
7. 不论是RPCTimer结束 或者 收到通信失败都继续下一轮
8. 通信成功, 查看回复
   1. 如果 reply.Term > rf.currentTerm
      1. 说明发送方已经落后与接收方, 变成Follower, 重置心跳计时器, term变成接收方的term

#### InstallSnapshot

1. args.Term < rf.currentTerm
   1. 请求方落后于接收方, 拒绝
2. rf.lastSnapshotIndex > args.LastIncludedIndex
   1. 接收方快照领先于发送方 拒绝
3. args.Term > rf.currentTerm || rf.role != Follower
   1. 发送方领先于接收方
   2. 安装快照, 接收方更新自己信息为发送方
4. 计算需要安装的快照范围, 即哪些logEntry需要被更新
   1. start := args.LastIncludedIndex - rf.lastSnapshotIndex 开始更新的起始log索引
   2. start<0 报错, 说明请求方快照落后于接收方 但是这种情况以及在之前处理过了
   3. start >= len(rf.logEntries)
      1. 说明接收方落后了超过整个快照
      2. 直接重新创建logEntry日志集合, 日志中只存放快照的最后一个
   4. start < len(rf.logEntries)
      1. 更新本地日志集合为 start以后的日志项
5. 调用Persister.SaveStateAndSnapshot方法持久化快照数据和raft的状态数据



# LAB3

构建一个基于Raft复制状态机的k/v存储系统, 提供 Get Put Append三个方法

服务提供了强一致性

> 所谓的强一致性（线性一致性）并不是指集群中所有节点在任一时刻的状态必须完全一致，而是指一个目标，即让一个分布式系统看起来只有一个数据副本，并且读写操作都是原子的，这样应用层就可以忽略系统底层多个数据副本间的同步问题。也就是说，我们可以将一个强一致性分布式系统当成一个整体，一旦某个客户端成功的执行了写操作，那么所有客户端都一定能读出刚刚写入的值。即使发生网络分区故障，或者少部分节点发生异常，整个集群依然能够像单机一样提供服务。

“**像单机一样提供服务**”从感官上描述了一个线性一致性系统应该具备的特性，那么我们该如何判断一个系统是否具备线性一致性呢？通俗来说就是不能读到旧（stale）数据，但具体分为两种情况：

- 对于调用时间存在重叠（**并发**）的请求，生效顺序可以任意确定。
- 对于调用时间存在先后关系（**偏序**）的请求，后一个请求不能违背前一个请求确定的结果。



## 3A Key/value service without log compaction

每一个KVServer都一个相关联的Raft Server, 所有的client请求必须请求到对应Raft leader的那个Server

KVServer 将Put/Append/Get请求发送给自己的raft, 这样raft 保存着所有操作命令的log

所有的KVServer都根据自己的Raft 的logEntry按顺序执行日志中的命令, 然后apply到自己的K/V数据库中

当一个客户端请求到不是Leader的KVServer的时候, 或者无法和服务端通信, 会尝试另一个KVServer

当KVServer将客户端的请求提交的时候(也会把日志apply到本地的状态机上), Leader会将结果通过RPC返回给客户端 

如果这个请求提交失败, KVServer会返回error, 客户端再请求其他KVServer



### Client.go

#### Clerk 

- Clerk 结构体代表客户端以及请求的命令信息
  - servers []*labrpc.ClientEnd KVServer的数组
  - size int KVServer的数量
  - recentLeader int 上一次请求成功的KVServer的索引
  - ClerkId
    - Uid 每个客户端唯一的唯一id
    - Seq 每有一个请求这个数字+1, 对于这个客户端全局唯一
    - 这两个信息一起保证了一条命令的唯一性

#### Get 

1. req++ 代表了这个客户端增加了一条请求
2. 将请求封装成GetRequest结构体
   1. key
   2. ClerkId
3. 从上次请求成功的KVServer开始请求(因为上次是leader这次可能还是)
4. 遍历每一个KVServer
   1. 通过labrpc.call RPC请求KVServer
   2. 如果KVServer回复成功
      1. 更新recentLeader
      2. 返回回复的resp.value
   3. 不成功继续向下一个KVServer请求



#### Put/Append

1. req++ 代表了这个客户端增加了一条请求
2. 将请求封装成 PutAppendRequest结构体
   1. key value
   2. OpType: Put|Append
   3. ClerkId
3. 从上次请求成功的KVServer开始请求
4. 遍历每一个KVServer
   1. 通过labrpc.call RPC请求KVServer
   2. 如果KVServer回复成功
      1. 更新recentLeader
      2. 无返回值
   3. 如果KVServer回复 重复的请求
      1. 说明这次的请求在之前请求过并且已经成功提交apply到数据库
      2. 但是因为当时处理请求的leader在提交log以后瞬间失效, 导致没有回复客户端成功, 
      3. 所以当前客户端超时后重复发送请求
      4. 但是因为我们会通过Clerk检测重复的请求, 会拦截, 保证每个请求最多只会执行一次
   4. 不成功继续向下一个KVServer请求





### Server.go

#### KVServer

- | 锁                                      | mu      sync.Mutex                                           |
  | --------------------------------------- | ------------------------------------------------------------ |
  | 自己在servers[]数组中的下标             | me      int                                                  |
  | KVServer对应的raft peer                 | rf      *raft.Raft                                           |
  | apply log 到对应数据库的chan            | applyCh chan raft.ApplyMsg                                   |
  |                                         | dead    int32                                                |
  | lock名字                                | lockName string                                              |
  | lock时间                                | lockTime time.Time                                           |
  | 当状态机达到这个大小就会执行快照        | maxraftstate int // snapshot if log grows this big           |
  |                                         | lastIdx int                                                  |
  | map[logIndex]map[Term]chan RaftResponse | distros map[int]map[int]chan RaftResponse // distribution channels |
  | map[Clerk.Uid]Clerk.Seq                 | clients map[string]int64                   // sequence number for each known client |
  | 数据库 map[key]value                    | state  map[string]string                 // state machine    |



#### StartKVServer

- 初始化KVServer 
- 调用kv.executionLoop等待客户端请求
- 返回生成的KVServer



#### Get

如果一个KVServer不是主要的保持一致的那一部分就不会处理客户端的Get()请求, 因为它存储的可能是过期的信息

解决办法就是把Get也放入Log, 这样就会在达成一致后由leader返回给客户端

1. 将客户端的请求封装成RaftRequest
   1. key 
   2. ClerkId
   3. OpType
2. 尝试将请求发送给对应的raft, 并且复制并apply这个log, 然后得到结果
3. 将得到的结果返回给请求客户端

#### PutAppend() and Get() RPC Handlers

这些Handler通过Start()提交客户端的命令到Raft, 然后等待Op出现在 applyCh 

1. 将客户端的请求封装成RaftRequest
   1. key value
   2. ClerkId
   3. OpType
2. 尝试将请求发送给对应的raft, 并且复制并apply这个log, 然后得到结果
3. 将得到的结果返回给请求客户端



#### raft.Start()

传入请求参数后, 返回三个变量

调用这个方法, raft会开始尝试将这个请求复制到所有的raft peers中, 如果复制成功并且提交,

会返回

- index 这个请求对应的log 在日志集合中的索引
- term 这个请求提交成功时的term
- isLeader 处理这个请求的raft是否是leader





#### tryApplyAndGetResult

1. 调用kv.rf.Start(req) 得到 index term isLeader
   1. 如果对于raft不是leader isleader=false 说明请求到不是leader的KVServer 返回
2. 创建一个用于接收raft回复的RaftResponse chan
3. 根据 idnex , term 创建一个map  对应这个请求的 index -> term ->RaftResponse
   1. distros map[index ]map[term ]chan RaftResponse 
   2. 对应每一个成功提交的log 都有一个唯一的RaftResponse chan 与这个log的index  term 一一对应
4. 设置本次apply的计时器
5. 等待消息
   1. RaftResponse的消息, 删除这个raftResponse chan 因为每个log都对应唯一的chan 不可以复用 返回结果给客户端
   2. apply计时器 说明raft超时 删除这个raftResponse chan 返回



#### executeLoop

1. 循环等待客户端请求(等待kv.applyCh的消息)
   1. 收到这个chan的消息说明当前的请求对应的log被提交了
2. 从这个applyCh得到的消息中得到这个被提交的 请求在日志集合中的 index 对应提交时的term, 以及请求具体是什么r = ClerkId
3. 检查这个请求是否是之前提交过的重复的请求, 因为没有及时得到回复重复提交的
   1. 获取这个请求的序列号 seq := kv.clients[r.Uid]
   2. 如果r.OpType != GET && r.Seq <= seq
      1. 这是一个Put/Append请求, 并且请求的序列号<这个客户端最新的序列号, 表示这是这个客户端之前提交过请求
      2. 通过 kv.distros\[idx][term]获取这个请求的唯一的chan RaftResponse 返回重复请求的信息
      3. 从distros中删除这个请求对应de kv.distros\[idx][term]
      4. 继续等待下一个请求
4. 这个请求不是重复的, 更新这个客户端的最新的请求序列号  kv.clients[r.Uid] = r.Seq
5. 根据请求的类型
   1. GET
      1. 获取对应key的value val := kv.state[r.Key]
      2.  kv.distros\[idx][term] 获取请求对应的chan RaftResponse
      3. 将val 已经成功的消息通过chan返回给客户端
      4. 删除请求对应的唯一的chan
   2. PUT
      1. 将val插入数据库  kv.state[r.Key] = r.Value
      2. kv.distros\[idx][term] 获取请求对应的chan RaftResponse
      3. 将val 已经成功的消息通过chan返回给客户端
      4. 删除请求对应的唯一的chan
   3. Append
      1. kv.state[r.Key] = kv.state[r.Key] + r.Value
      2. kv.distros\[idx][term] 获取请求对应的chan RaftResponse
      3. 将val 已经成功的消息通过chan返回给客户端
      4. 删除请求对应的唯一的chan
6. 如果当前raftstate已经达到了状态机的最大限制 maxraftstate 生成快照, 并且删除所有的commitIndex之前的请求相关的chan, 因为commitIndex之前的日志都已经生成了快照



#### Op

描述了Put/Append/Get 操作的信息 (K,V)

每一个Server提交log的时候都需要提交这些日志到本地状态机, 这样其他客户端请求可以看到之前的请求的结果 (出现在applyCh就提交)

需要一个RPC Handler, 注意到Raft提交Op的时候, 就回复RPC





#### edge case

Cleark发送RPC给一个leader , leader提交了这个日志, 但是马上就断线了, 没有回复Cleark, 但是这个提交的日志会被已经复制过的raft都标记为commit 并且apply到本地状态机, 所以事实上这个Op已经作用到了数据库, 但是因为没有回复Cleark, Cleark还会继续发送这个Op到其他的服务器, 

- 我们需要保证这种情况下, 一个相同的Op只会被执行一次, 即当这个Cleark向其他服务器发起同样的请求的时候, 这个请求不会被执行

- 这样要求我们需要对每个客户端的请求做唯一性的确认, 通过请求Id和ClientId构成唯一的标识, 如果两个都相同, 不会被执行第二次

# LAB4

## 建立一个分片的存储系统

- 分片: 表示把数据按照键分成多个部分, 每个分片都是整个数据的子集, 主要是为了提高性能
- 每个复制的raft群体都可以负责处理一部分分片的 put get请求, 这些raft群体是并行处理的, 所以提高了整体的吞吐量

## K/V分片存储系统的组成

### 复制群体集合

- 集合里的每个复制群体都负责了一部分分片的请求处理
- 每个复制都是由一些KVServer组成, 这些KVServer使用Raft来复制群里的分片数据

### 分片领主

- 整个系统唯一的领主, 通过使用Raft提供服务
- master决定了每个复制群体应该服务哪些分片, 这些信息叫做configuration, configuration会随着时间改变
- Client询问Master来找到key对应的replicate group
- replica group 询问master找到自己应该服务哪些shards

### 分片迁移

- 分片存储系统可以提供分片在不同复制群体间的迁移
- 提供负载均衡, 某些复制群体也许承载了太多的分片, 所以分片的迁移可以使得分片分布更平均
- 某些复制群体可能会下线, 新的复制群体也可能会加入, 所以分片需要在这些情况被移动

### 处理reconfiguration

#### 在分配分片到不同的复制群体时, 客户端的请求该如何处理?

在一个复制群体内, 所有的群体成员都必须同意 reconfiguration 与 客户端的 Put/Append/Get 请求发生的相对时间关系

- 客户端的PUT请求到来, 但是这个时候, 正好这个复制群体进行重新分片导致这个复制群体不在负责这个分片的请求,
  - 这个复制群体里的所有的结点需要一致性的同意 这个PUT请求发生在reconfiguration之前还是之后
    - 如果是之前, 那么负责这个分片的replica group 需要看见这个PUT的结果
    - 如果之后, PUT不会生效, 客户端需要重新向这个分片的新的replica group 再次请求

可以通过Raft把reconfiguration也当做log, 这样就可以保证replica group的leader处理这些请求, 以及线性一致性

#### replica group之间的RPC通信

replica group之间的分片是通过RPC进行, 

- configuration 10 G1负责S1
- configuration 11 G2负责S1
- 在configuration 10到11之间, G1必须使用RPC把分片迁移到G2

### Numbered Configuration 配置编号

- shardMaster管理一系列numbered configuration

- 每一个numbered configuration 都描述了replica group 和 分片的映射关系
- 当映射关系改变的时候, 需要重新生成新的configuration 编号增加1

## 4A The Shard Master 

需要实现shards在不同的replica group之间的转移

### Join

- 加入一些新的 replica groups, 
- 参数: map[GIDs]\<Server name>  replica group Identifier (GIDs) --> server name
- masert创建新的configuration 包括新的replica groups, 并且尽可能少的移动shards
- GIDs只要不存在在当前的configuration就可以被重用

### Leave

- 参数 []GIDS
- 创建新的configuration 去除一些之前存在的GIDs, 
- 将这个replica负责的shards均匀的分配到其他的replica groups

### Move

- 参数 shard number, GID
- 创建新的configuration 将指定的 shard 分配给 GID对应的replica groups

### Query

- 参数 configuration number
- 返回这个configuration number对应的configuration
- 如果numer=-1 或者比当前最大的number还要大, 返回当前最大的configuration number对应的configuration



### Client

- 封装 Join/Leave/Move/Query 对应的参数到对应的 args结构体
- 循环遍历每个server, 发送对应请求的RPC
- 得到回复, 如果是操作成功的回复就返回, 否则继续向下一个server请求



### ShardMaster

#### ShardMaster{}

相比KVServer多了

- Config[] 保存每个numbered configuration对应的shards-->replicas group信息
- client server都通过请求master的这个信息来得知自己负责哪些shards和key对应的shards是由哪个replica group负责

#### Join/Leave/Move/Query Handler

- 封装client的请求数据以及server的信息
- 调用master的 tryApplyAndGetResult() 方法将这个请求apply到database



####  tryApplyAndGetResult

- 开始复制这个即将放到logEntry里面的请求

1. 调用sc.rf.Start(req) 得到 index term isLeader
   1. 如果对于raft不是leader isleader=false 说明请求到不是leader的KVServer 返回
2. 创建一个用于接收raft回复的GeneralOutput chan 
   1. ch := make(chan GeneralOutput, 1)
3. 如果这个日志(通过index term确定唯一性)对应的Map不存在就创建一个
   1. mm = make(map[int]chan GeneralOutput)
   2. sc.sigChans[idx] = mm
   3. mm[term] = ch
   4. sigChans[index] = map[term]chan GeneralOutPut 通过 index-->term-->确定一个logEntry唯一的chan
4. 设置本次apply的计时器
5. 等待消息
   1. GeneralOutPut的消息, 删除这个sigChans[index]  因为每个log都对应唯一的chan 不可以复用 返回结果给客户端
   2. apply计时器 说明raft超时 删除这个raftResponse chan 返回

#### executeLoop

1. 循环等待客户端请求(等待kv.applyCh的消息)
   1. 收到这个chan的消息说明当前的请求对应的log被提交了
2. 从这个applyCh得到的消息中得到这个被提交的 
   1. 请求在日志集合中的 index 
   2. 对应提交时的term, 
   3. 以及请求具体是什么r := msg.Command.(GeneralInput)
3. 检查这个请求是否是之前提交过的重复的请求, 因为没有及时得到回复重复提交的
   1. 获取这个请求的序列号 seq := sc.clientSeq[r.Uid]
   2. 如果 r.OpType != QUERY && r.Seq <= seq
      1. 这是一个Join/Leave/Move请求, 并且请求的序列号<这个客户端最新的序列号, 表示这是这个客户端之前提交过请求
      2. 获得这个index term 对应log唯一的chan  sc.sigChans\[idx][term]
      3. 向这个chan发送重复请求的信息
      4. 从sigChans中删除这个chan, 因为这个请求已经执行过了
      5. 继续等待下一个请求
4. 根据这个提交请求的类型 r.OpType
   1. Join
      1. 获取replica group集合信息
      2. 向replica group集合中加入新的replica group 调用joinGroups方法
      3. 向这个log对应的chan发送成功的消息
      4. 删除这个请求对应的chan  sigChan\[index][term]
   2. Leave
      1. 获取需要删除的replica group GIDs数组
      2. 调用leaveGroup方法 创建新的configuration的时候删除数组中对应的replica groups
      3. 向这个log对应的chan发送成功的消息
      4. 删除这个请求对应的chan  sigChan\[index][term]
   3. move
      1. 获取指定的  shard-->replica group信息
      2. 调用moveOneShard方法, 创建新的configuration的时候指定一个对应的 replica-shard关系
      3. 向这个log对应的chan发送成功的消息
      4. 删除这个请求对应的chan  sigChan\[index][term]
   4. query
      1. 获取请求的configuration对应的num
      2. 如果 num != -1 && num < len(sc.configs) 
         1. 这是一个存在的config 直接返回
         2. 否则返回当前最新的config
      3. 向这个log对应的chan发送成功的消息
      4. 删除这个请求对应的chan  sigChan\[index][term]



####  moveOneShard

1. 获取上一个configuration lastConfig
2. 新的Configuration的编号比上次+1
3. replica group信息
   1. 复制Groups属性 map\[int][]string 是GIDs-->server name的映射 
4. shards信息
   1. 复制Shards属性 Shards[s] 
5. 分配move指定配对的 shards--replica group
   1. newConfig.Shards[m.Shard] = m.GID
6. 将这个新生成的config append到旧的configuration数组中



#### joinGroups

1. idx+ 复制 Groups shards信息
2. 将新的replicas groups加入进来
3. reallocate所有的shards使得尽量平均和尽量少的移动shards
   1. reallocSlots
4. 新的configuration append到config数组

#### leaveGroups

1. idx+ 复制 Groups 信息
2. 删除leave的那些replica group
3. reallocate所有的shards使得尽量平均和尽量少的移动shards
   1. reallocSlots
4. 新的configuration append到config数组

#### reallocSlots

1. 得到replica groups r, shards s的数量
2.  s/r s%r 
3. 先给每个replica 分配 s/r个 shards, 如果s%r还有剩余就分配一个

 







## 4B Sharded Key/Value Server 

### shard KVServer

- 每个server 都是replica group 的一部分

- 每个replica group 处理一些shards 的 Put/Append/Get
- 多个replica group合作处理所有的shards
- master 分配shards到replica groups
- 新的configuration创建, replica groups 之间互相传递shards 在这个过程中, client不会看见不一致的信息

- 需要提供线性一致性, 每个完成的 Cleark.Get() Clerk.Put() Clerk.Append() 需要对所有的replicas 产生相同顺序的影响
- Clerk.Get()可以看到最新的改变
  - Get Put 同时到达, 并且这时正在发生configuration change 重新分片
- 在保证超过一半的serves都可以工作的情况下, shards可以继续更新
- 一个shard KVServer只属于一个replica group 并且每个replica group中的server不会改变

### shard KVClient

- 需要处理重复的client RPC请求




### task1 single shard

单一分片, server需要根据client的请求找到configuration中对应key所属的shards, 以及这个shards对应的replica group

### task2 sharding change

- server需要监视configuration 改变
  - 每个100ms询问master是否有新的configuration
- 当一个server检测到configuration改变, 开始shard 迁移
  - 如果一个replica 不再负责一个shards, 马上停止接收对应shards的请求, 并且开始将shards转移到新的replica
  - 如果一个replica收到更多的shards, 需要等待旧的owner将数据转移过来



### Hints

- server poll master获取最新的configuration, 失去对一个shards的ownership以后拒绝对应clients请求
- 更新了configuration以后, 允许shards的旧Owner保持那部分shards数据
- 当server通过RPC 会传输包含自己state信息的map的时候, 会产生死锁, 因为接受者需要读取map, 但是没有锁就没办法读, 所以需要传送一份map copy



## 















**笔者期望通过一篇权威靠谱、清晰易懂的系统性文章，帮助读者深入理解 Raft 算法，并能付诸于工程实践中，同时解读不易理解或容易误解的关键点。**

本文是 Raft 实战系列理论内容的整合篇，我们结合 Raft 论文讲解 Raft 算法思路，并遵循 Raft 的模块化思想对难理解及容易误解的内容抽丝剥茧。算法方面讲解：选主机制、基于日志实现状态机机制、安全正确维护状态机机制；工程实现方面讲解：集群成员变更防脑裂策略、解决数据膨胀及快速恢复状态机策略、线性一致读性能优化策略等。

------

# 1. 概述

## 1.1 Raft 是什么？

> Raft is a consensus algorithm for managing a replicated log. It produces a result equivalent to (multi-)Paxos, and it is as efficient as Paxos, but its structure is different from Paxos; this makes Raft more understandable than Paxos and also provides a better foundation for building practical systems.
>
> --《In Search of an Understandable Consensus Algorithm》

在分布式系统中，为了消除单点提高系统可用性，通常会使用副本来进行容错，但这会带来另一个问题，即如何保证多个副本之间的一致性？

> 这里我们只讨论强一致性，即线性一致性。弱一致性涵盖的范围较广，涉及根据实际场景进行诸多取舍，不在 Raft 系列的讨论目标范围内。

所谓的强一致性（线性一致性）并不是指集群中所有节点在任一时刻的状态必须完全一致，而是指一个目标，即让一个分布式系统看起来只有一个数据副本，并且读写操作都是原子的，这样应用层就可以忽略系统底层多个数据副本间的同步问题。也就是说，我们可以将一个强一致性分布式系统当成一个整体，一旦某个客户端成功的执行了写操作，那么所有客户端都一定能读出刚刚写入的值。即使发生网络分区故障，或者少部分节点发生异常，整个集群依然能够像单机一样提供服务。

共识算法（Consensus Algorithm）就是用来做这个事情的，它保证即使在小部分（≤ (N-1)/2）节点故障的情况下，系统仍然能正常对外提供服务。共识算法通常基于状态复制机（Replicated State Machine）模型，也就是所有节点从同一个 state 出发，经过同样的操作 log，最终达到一致的 state。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/715f38a55fae4ef6bed7bb7cfd134825~tplv-k3u1fbpfcp-watermark.image) *图：Replicated State Machine*

共识算法是构建强一致性分布式系统的基石，Paxos 是共识算法的代表，而 Raft 则是其作者在博士期间研究 Paxos 时提出的一个变种，主要优点是容易理解、易于实现，甚至关键的部分都在论文中给出了伪代码实现。

## 1.2 谁在使用 Raft

采用 Raft 的系统最著名的当属 [etcd](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fetcd-io%2Fetcd) 了，可以认为 etcd 的核心就是 Raft 算法的实现。作为一个分布式 kv 系统，etcd 使用 Raft 在多节点间进行数据同步，每个节点都拥有全量的状态机数据。我们在学习了 Raft 以后将会深刻理解为什么 etcd 不适合大数据量的存储（for the most critical data）、为什么集群节点数不是越多越好、为什么集群适合部署奇数个节点等问题。

作为一个微服务基础设施，[consul](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fhashicorp%2Fconsul) 底层使用 Raft 来保证 consul server 之间的数据一致性。在阅读完第六章后，我们会理解为什么 consul 提供了 `default`、`consistent`、`stale` 三种一致性模式（Consistency Modes）、它们各自适用的场景，以及 consul 底层是如何通过改变 Raft 读模型来支撑这些不同的一致性模式的。

[TiKV](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ftikv%2Ftikv) 同样在底层使用了 Raft 算法。虽然都自称是“分布式 kv 存储”，但 TiKV 的使用场景与 etcd 存在区别。其目标是支持 100TB+ 的数据，类似 etcd 的单 Raft 集群肯定无法支撑这个数据量。因此 TiKV 底层使用 Multi Raft，将数据划分为多个 region，每个 region 其实还是一个标准的 Raft 集群，对每个分区的数据实现了多副本高可用。

目前 Raft 在工业界已经开始大放异彩，对于其各类应用场景这里不再赘述，感兴趣的读者可以参考 [这里](https://link.juejin.cn/?target=https%3A%2F%2Fraft.github.io%2F)，下方有列出各种语言的大量 Raft 实现。

## 1.3 Raft 基本概念

Raft 使用 Quorum 机制来实现共识和容错，我们将对 Raft 集群的操作称为提案，每当发起一个提案，必须得到大多数（> N/2）节点的同意才能提交。

> 这里的“提案”我们可以先狭义地理解为对集群的读写操作，“提交”理解为操作成功。

那么当我们向 Raft 集群发起一系列读写操作时，集群内部究竟发生了什么呢？我们先来概览式地做一个整体了解，接下来再分章节详细介绍每个部分。

首先，Raft 集群必须存在一个主节点（leader），我们作为客户端向集群发起的所有操作都必须经由主节点处理。所以 Raft 核心算法中的第一部分就是**选主**（**Leader election**）——没有主节点集群就无法工作，先票选出一个主节点，再考虑其它事情。

其次，主节点需要承载什么工作呢？它会负责接收客户端发过来的操作请求，将操作包装为**日志**同步给其它节点，在保证**大部分**节点都同步了本次操作后，就可以安全地给客户端回应响应了。这一部分工作在 Raft 核心算法中叫**日志复制**（**Log replication**）。

然后，因为主节点的责任是如此之大，所以节点们在选主的时候一定要谨慎，只有**符合条件**的节点才可以当选主节点。此外主节点在处理操作日志的时候也一定要谨慎，为了保证集群对外展现的一致性，不可以**覆盖或删除**前任主节点已经处理成功的操作日志。所谓的“谨慎处理”，其实就是在选主和提交日志的时候进行一些限制，这一部分在 Raft 核心算法中叫**安全性**（**Safety**）。

**Raft 核心算法其实就是由这三个子问题组成的：选主（Leader election）、日志复制（Log replication）、安全性（Safety）。这三部分共同实现了 Raft 核心的共识和容错机制。**

除了核心算法外，Raft 也提供了几个工程实践中必须面对问题的解决方案。

第一个是关于日志无限增长的问题。Raft 将操作包装成为了日志，集群每个节点都维护了一个不断增长的日志序列，状态机只有通过重放日志序列来得到。但由于这个日志序列可能会随着时间流逝不断增长，因此我们必须有一些办法来避免无休止的磁盘占用和过久的日志重放。这一部分叫**日志压缩**（**Log compaction**）。

第二个是关于集群成员变更的问题。一个 Raft 集群不太可能永远是固定几个节点，总有扩缩容的需求，或是节点宕机需要替换的时候。直接更换集群成员可能会导致严重的**脑裂**问题。Raft 给出了一种安全变更集群成员的方式。这一部分叫**集群成员变更**（**Cluster membership change**）。

此外，我们还会额外讨论**线性一致性**的定义、为什么 **Raft 不能与线性一致划等号**、如何**基于 Raft 实现线性一致**，以及在如何**保证线性一致的前提下进行读性能优化**。

以上便是理论篇内将会讨论到的大部分内容的概要介绍，这里我们对 Raft 已经有了一个宏观上的认识，知道了各个部分大概是什么内容，以及它们之间的关系。

接下来我们将会详细讨论 Raft 算法的每个部分。让我们先从第一部分**选主**开始。

# 2. 选主

## 2.1 什么是选主

选主（Leader election）就是在分布式系统内抉择出一个主节点来负责一些特定的工作。在执行了选主过程后，集群中每个节点都会识别出一个特定的、唯一的节点作为 leader。

我们开发的系统如果遇到选主的需求，通常会直接基于 zookeeper 或 etcd 来做，把这部分的复杂性收敛到第三方系统。然而作为 etcd 基础的 Raft 自身也存在“选主”的概念，这是两个层面的事情：基于 etcd 的选主指的是利用第三方 etcd 让集群对谁做主节点的决策达成一致，技术上来说利用的是 etcd 的一致性状态机、lease 以及 watch 机制，这个事情也可以改用单节点的 MySQL/Redis 来做，只是无法获得高可用性；而 Raft 本身的选主则指的是在 Raft 集群自身内部通过票选、心跳等机制来协调出一个大多数节点认可的主节点作为集群的 leader 去协调所有决策。

**当你的系统利用 etcd 来写入谁是主节点的时候，这个决策也在 etcd 内部被它自己集群选出的主节点处理并同步给其它节点。**

## 2.2 Raft 为什么要进行选主？

按照论文所述，原生的 Paxos 算法使用了一种点对点（peer-to-peer）的方式，所有节点地位是平等的。在理想情况下，算法的目的是制定**一个决策**，这对于简化的模型比较有意义。但在工业界很少会有系统会使用这种方式，当有一系列的决策需要被制定的时候，先选出一个 leader 节点然后让它去协调所有的决策，这样算法会更加简单快速。

此外，和其它一致性算法相比，Raft 赋予了 leader 节点更强的领导力，称之为 **Strong Leader**。比如说日志条目只能从 leader 节点发送给其它节点而不能反着来，这种方式简化了日志复制的逻辑，使 Raft 变得更加简单易懂。

## 2.3 Raft 选主过程

### 2.3.1 节点角色

Raft 集群中每个节点都处于以下三种角色之一：

- **Leader**: 所有请求的处理者，接收客户端发起的操作请求，写入本地日志后同步至集群其它节点。
- **Follower**: 请求的被动更新者，从 leader 接收更新请求，写入本地文件。如果客户端的操作请求发送给了 follower，会首先由 follower 重定向给 leader。
- **Candidate**: 如果 follower 在一定时间内没有收到 leader 的心跳，则判断 leader 可能已经故障，此时启动 leader election 过程，本节点切换为 candidate 直到选主结束。

### 2.3.2 任期

每开始一次新的选举，称为一个**任期**（**term**），每个 term 都有一个严格递增的整数与之关联。

每当 candidate 触发 leader election 时都会增加 term，如果一个 candidate 赢得选举，他将在本 term 中担任 leader 的角色。但并不是每个 term 都一定对应一个 leader，有时候某个 term 内会由于选举超时导致选不出 leader，这时 candicate 会递增 term 号并开始新一轮选举。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3e463285d0e4d6299fca19c7ef7e334~tplv-k3u1fbpfcp-watermark.image)

Term 更像是一个**逻辑时钟**（**logic clock**）的作用，有了它，就可以发现哪些节点的状态已经过期。每一个节点都保存一个 current term，在通信时带上这个 term 号。

节点间通过 RPC 来通信，主要有两类 RPC 请求：

- **RequestVote RPCs**: 用于 candidate 拉票选举。
- **AppendEntries RPCs**: 用于 leader 向其它节点复制日志以及同步心跳。

### 2.3.3 节点状态转换

我们知道集群每个节点的状态都只能是 leader、follower 或 candidate，那么节点什么时候会处于哪种状态呢？下图展示了一个节点可能发生的状态转换：

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d9f2d1f2d3674a50900eda504ecaa326~tplv-k3u1fbpfcp-watermark.image)

接下来我们详细讨论下每个转换所发生的场景。

#### 2.3.3.1 Follower 状态转换过程

Raft 的选主基于一种心跳机制，集群中每个节点刚启动时都是 follower 身份（**Step: starts up**），leader 会周期性的向所有节点发送心跳包来维持自己的权威，那么首个 leader 是如何被选举出来的呢？方法是如果一个 follower 在一段时间内没有收到任何心跳，也就是选举超时，那么它就会主观认为系统中没有可用的 leader，并发起新的选举（**Step: times out, starts election**）。

这里有一个问题，即这个“选举超时时间”该如何制定？如果所有节点在同一时刻启动，经过同样的超时时间后同时发起选举，整个集群会变得低效不堪，极端情况下甚至会一直选不出一个主节点。Raft 巧妙的使用了一个随机化的定时器，让每个节点的“超时时间”在一定范围内随机生成，这样就大大的降低了多个节点同时发起选举的可能性。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc2b320077124da39bade7adeaf7ef08~tplv-k3u1fbpfcp-watermark.image) *图：一个五节点 Raft 集群的初始状态，所有节点都是 follower 身份，term 为 1，且每个节点的选举超时定时器不同*

若 follower 想发起一次选举，follower 需要先增加自己的当前 term，并将身份切换为 candidate。然后它会向集群其它节点发送“请给自己投票”的消息（RequestVote RPC）。

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/316b81a42ea843a192cc2f3578fbdb81~tplv-k3u1fbpfcp-watermark.image) *图：S1 率先超时，变为 candidate，term + 1，并向其它节点发出拉票请求*

#### 2.3.3.2 Candicate 状态转换过程

Follower 切换为 candidate 并向集群其他节点发送“请给自己投票”的消息后，接下来会有三种可能的结果，也即上面**节点状态图中 candidate 状态向外伸出的三条线**。

**1. 选举成功（Step: receives votes from majority of servers）**

当candicate从整个集群的**大多数**（N/2+1）节点获得了针对同一 term 的选票时，它就赢得了这次选举，立刻将自己的身份转变为 leader 并开始向其它节点发送心跳来维持自己的权威。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a851f56a384b4a0f9da76644d645ae1f~tplv-k3u1fbpfcp-watermark.image) *图：“大部分”节点都给了 S1 选票*

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a6e0e622b7384ac78f733e471b280c27~tplv-k3u1fbpfcp-watermark.image) *图：S1 变为 leader，开始发送心跳维持权威*

每个节点针对每个 term 只能投出一张票，并且按照先到先得的原则。这个规则确保只有一个 candidate 会成为 leader。

**2. 选举失败（Step: discovers current leader or new term）**

Candidate 在等待投票回复的时候，可能会突然收到其它自称是 leader 的节点发送的心跳包，如果这个心跳包里携带的 term **不小于** candidate 当前的 term，那么 candidate 会承认这个 leader，并将身份切回 follower。这说明其它节点已经成功赢得了选举，我们只需立刻跟随即可。但如果心跳包中的 term 比自己小，candidate 会拒绝这次请求并保持选举状态。

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d4ce0c7fdf3b4039a0e4b0b200af731a~tplv-k3u1fbpfcp-watermark.image) *图：S4、S2 依次开始选举*

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2535cfeb9cfb44d4a0a04506f09f7485~tplv-k3u1fbpfcp-watermark.image) *图：S4 成为 leader，S2 在收到 S4 的心跳包后，由于 term 不小于自己当前的 term，因此会立刻切为 follower 跟随 S4*

**3. 选举超时（Step: times out, new election）**

第三种可能的结果是 candidate 既没有赢也没有输。如果有多个 follower 同时成为 candidate，选票是可能被瓜分的，如果没有任何一个 candidate 能得到大多数节点的支持，那么每一个 candidate 都会超时。此时 candidate 需要增加自己的 term，然后发起新一轮选举。如果这里不做一些特殊处理，选票可能会一直被瓜分，导致选不出 leader 来。这里的“特殊处理”指的就是前文所述的**随机化选举超时时间**。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04163db3b7ca4b4c8fbfdf4e7253a98f~tplv-k3u1fbpfcp-watermark.image) *图：S1 ~ S5 都在参与选举*

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/517e2f7939394b3ca35936702a107f07~tplv-k3u1fbpfcp-watermark.image) *图：没有任何节点愿意给他人投票*

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b52e004bd4654cb1b51cdb409182156f~tplv-k3u1fbpfcp-watermark.image) *图：如果没有随机化超时时间，所有节点将会继续同时发起选举……*

以上便是 candidate 三种可能的选举结果。

#### 2.3.3.3 Leader 状态转换过程

节点状态图中的最后一条线是：**discovers server with higher term**。想象一个场景：当 leader 节点发生了宕机或网络断连，此时其它 follower 会收不到 leader 心跳，首个触发超时的节点会变为 candidate 并开始拉票（由于随机化各个 follower 超时时间不同），由于该 candidate 的 term 大于原 leader 的 term，因此所有 follower 都会投票给它，这名 candidate 会变为新的 leader。一段时间后原 leader 恢复了，收到了来自新leader 的心跳包，发现心跳中的 term 大于自己的 term，此时该节点会立刻切换为 follower 并跟随的新 leader。

上述流程的动画模拟如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33f858652afc4be6bb1d7b1f1dc33eaa~tplv-k3u1fbpfcp-watermark.image) *图：S4 作为 term2 的 leader*

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d45e677f6c2428d9b9db7022416fe26~tplv-k3u1fbpfcp-watermark.image) *图：S4 宕机，S5 即将率先超时*

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1418b09699fe4614a594a565c55055d5~tplv-k3u1fbpfcp-watermark.image) *图：S5 当选 term3 的 leader*

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f0d9d314c75c4bd989dadf044ecd6307~tplv-k3u1fbpfcp-watermark.image) *图：S4 宕机恢复后收到了来自 S5 的 term3 心跳*

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/31b9745a2fa94fb693a7ea1257aa5f7f~tplv-k3u1fbpfcp-watermark.image) *图：S4 立刻变为 S5 的 follower*

以上就是 Raft 的选主逻辑，但还有一些细节（譬如是否给该 candidate 投票还有一些其它条件）依赖算法的其它部分基础，我们会在后续“安全性”一章描述。

当票选出 leader 后，leader 也该承担起相应的责任了，这个责任是什么？就是下一章将介绍的“日志复制”。

# 3. 日志复制

## 3.1 什么是日志复制

在前文中我们讲过：共识算法通常基于**状态复制机**（**Replicated State Machine**）模型，所有节点从**同一个 state** 出发，经过一系列**同样操作 log** 的步骤，最终也必将达到**一致的 state**。也就是说，只要我们保证集群中所有节点的 log 一致，那么经过一系列应用（apply）后最终得到的状态机也就是一致的。

Raft 负责保证集群中所有节点 **log 的一致性**。

此外我们还提到过：Raft 赋予了 leader 节点更强的领导力（**Strong Leader**）。那么 Raft 保证 log 一致的方式就很容易理解了，即所有 log 都必须交给 leader 节点处理，并由 leader 节点复制给其它节点。

这个过程，就叫做**日志复制**（**Log replication**）。

## 3.2 Raft 日志复制机制解析

### 3.2.1 整体流程解析

一旦 leader 被票选出来，它就承担起领导整个集群的责任了，开始接收客户端请求，并将操作包装成日志，并复制到其它节点上去。

整体流程如下：

- Leader 为客户端提供服务，客户端的每个请求都包含一条即将被状态复制机执行的指令。
- Leader 把该指令作为一条新的日志附加到自身的日志集合，然后向其它节点发起**附加条目请求**（**AppendEntries RPC**），来要求它们将这条日志附加到各自本地的日志集合。
- 当这条日志已经确保被**安全的复制**，即大多数（N/2+1）节点都已经复制后，leader 会将该日志 **apply** 到它本地的状态机中，然后把操作成功的结果返回给客户端。

整个集群的日志模型可以宏观表示为下图（x ← 3 代表 x 赋值为 3）：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60d6b91f49ac4d11bee43f86f33566d6~tplv-k3u1fbpfcp-watermark.image)

每条日志除了存储状态机的操作指令外，还会拥有一个**唯一的整数索引值**（**log index**）来表明它在日志集合中的位置。此外，每条日志还会存储一个 **term** 号（日志条目方块最上方的数字，相同颜色 term 号相同），该 term 表示 leader 收到这条指令时的当前任期，term 相同的 log 是由同一个 leader 在其任期内发送的。

当一条日志被 leader 节点认为可以安全的 apply 到状态机时，称这条日志是 **committed**（上图中的 **committed entries**）。那么什么样的日志可以被 commit 呢？答案是：**当 leader 得知这条日志被集群过半的节点复制成功时**。因此在上图中我们可以看到 (term3, index7) 这条日志以及之前的日志都是 committed，尽管有两个节点拥有的日志并不完整。

Raft 保证所有 committed 日志都已经被**持久化**，且“**最终**”一定会被状态机apply。

*注：这里的“最终”用词很微妙，它表明了一个特点：Raft 保证的只是集群内日志的一致性，而我们真正期望的集群对外的状态机一致性需要我们做一些额外工作，这一点在《线性一致性与读性能优化》一章会着重介绍。*

### 3.2.2 日志复制流程图解

我们通过 [Raft 动画](https://link.juejin.cn/?target=https%3A%2F%2Fraft.github.io%2F) 来模拟常规日志复制这一过程：

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/11bd5a3d291b43d2b31fea3a75fb3655~tplv-k3u1fbpfcp-watermark.image)

如上图，S1 当选 leader，此时还没有任何日志。我们模拟客户端向 S1 发起一个请求。

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7a17a0e447534541a520ec97a9942cdd~tplv-k3u1fbpfcp-watermark.image)

S1 收到客户端请求后新增了一条日志 (term2, index1)，然后并行地向其它节点发起 AppendEntries RPC。

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b0c350086014a52a4989f02f5262153~tplv-k3u1fbpfcp-watermark.image)

S2、S4 率先收到了请求，各自附加了该日志，并向 S1 回应响应。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6f062cd961a24c5a951f2c025f065bd4~tplv-k3u1fbpfcp-watermark.image)

所有节点都附加了该日志，但由于 leader 尚未收到任何响应，因此暂时还不清楚该日志到底是否被成功复制。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/97dbbaaf72cc4a2ba148cb252bce95ec~tplv-k3u1fbpfcp-watermark.image)

当 S1 收到**2个节点**的响应时，该日志条目的边框就已经变为实线，表示该日志已经**安全的复制**，因为在5节点集群中，2个 follower 节点加上 leader 节点自身，副本数已经确保过半，此时 **S1 将响应客户端的请求**。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a127762b466a48719967d52ab9f0636c~tplv-k3u1fbpfcp-watermark.image)

leader 后续会持续发送心跳包给 followers，心跳包中会携带当前**已经安全复制（我们称之为 committed）的日志索引**，此处为 (term2, index1)。

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f7ebe584867b4c0b98b1e46f595a5007~tplv-k3u1fbpfcp-watermark.image)

所有 follower 都通过心跳包得知 (term2, index1) 的 log 已经成功复制 （committed），因此所有节点中该日志条目的边框均变为实线。

### 3.2.3 对日志一致性的保证

前边我们使用了 (term2, index1) 这种方式来表示一条日志条目，这里为什么要带上 term，而不仅仅是使用 index？原因是 term 可以用来检查不同节点间日志是否存在不一致的情况，阅读下一节后会更容易理解这句话。

Raft 保证：**如果不同的节点日志集合中的两个日志条目拥有相同的 term 和 index，那么它们一定存储了相同的指令。**

为什么可以作出这种保证？因为 Raft 要求 leader 在一个 term 内针对同一个 index 只能创建一条日志，并且永远不会修改它。

同时 Raft 也保证：**如果不同的节点日志集合中的两个日志条目拥有相同的 term 和 index，那么它们之前的所有日志条目也全部相同。**

这是因为 leader 发出的 AppendEntries RPC 中会额外携带**上一条**日志的 (term, index)，如果 follower 在本地找不到相同的 (term, index) 日志，则**拒绝接收这次新的日志**。

所以，只要 follower 持续正常地接收来自 leader 的日志，那么就可以通过归纳法验证上述结论。

### 3.2.4 可能出现的日志不一致场景

在所有节点正常工作的时候，leader 和 follower的日志总是保持一致，AppendEntries RPC 也永远不会失败。然而我们总要面对任意节点随时可能宕机的风险，如何在这种情况下继续保持集群日志的一致性才是我们真正要解决的问题。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/012758dde1ba4305b417d860236a2ecc~tplv-k3u1fbpfcp-watermark.image)

上图展示了一个 term8 的 leader 刚上任时，集群中日志可能存在的混乱情况。例如 follower 可能缺少一些日志（a ~ b），可能多了一些未提交的日志（c ~ d），也可能既缺少日志又多了一些未提交日志（e ~ f）。

*注：Follower 不可能比 leader 多出一些已提交（committed）日志，这一点是通过选举上的限制来达成的，会在下一章《安全性》介绍。*

我们先来尝试复现上述 a ~ f 场景，最后再讲 Raft 如何解决这种不一致问题。

**场景a~b. Follower 日志落后于 leader**

这种场景其实很简单，即 **follower 宕机了一段时间**，follower-a 从收到 (term6, index9) 后开始宕机，follower-b 从收到 (term4, index4) 后开始宕机。这里不再赘述。

**场景c. Follower 日志比 leader 多 term6**

当 term6 的 leader 正在将 (term6, index11) 向 follower 同步时，该 leader 发生了宕机，且此时只有 follower-c 收到了这条日志的 AppendEntries RPC。然后经过一系列的选举，term7 可能是选举超时，也可能是 leader 刚上任就宕机了，最终 term8 的 leader 上任了，成就了我们看到的场景 c。

**场景d. Follower 日志比 leader 多 term7**

当 term6 的 leader 将 (term6, index10) 成功 commit 后，发生了宕机。此时 term7 的 leader 走马上任，连续同步了两条日志给 follower，然而还没来得及 commit 就宕机了，随后集群选出了 term8 的 leader。

**场景e. Follower 日志比 leader 少 term5 ~ 6，多 term4**

当 term4 的 leader 将 (term4, index7) 同步给 follower，且将 (term4, index5) 及之前的日志成功 commit 后，发生了宕机，紧接着 follower-e 也发生了宕机。这样在 term5~7 内发生的日志同步全都被 follower-e 错过了。当 follower-e 恢复后，term8 的 leader 也刚好上任了。

**场景f. Follower 日志比 leader 少 term4 ~ 6，多 term2 ~ 3**

当 term2 的 leader 同步了一些日志（index4 ~ 6）给 follower 后，尚未来得及 commit 时发生了宕机，但它很快恢复过来了，又被选为了 term3 的 leader，它继续同步了一些日志（index7~11）给 follower，但同样未来得及 commit 就又发生了宕机，紧接着 follower-f 也发生了宕机，当 follower-f 醒来时，集群已经前进到 term8 了。

### 3.2.5 如何处理日志不一致

通过上述场景我们可以看到，真实世界的集群情况很复杂，那么 Raft 是如何应对这么多不一致场景的呢？其实方式很简单暴力，想想 **Strong Leader** 这个词。

**Raft 强制要求 follower 必须复制 leader 的日志集合来解决不一致问题。**

也就是说，follower 节点上任何与 leader 不一致的日志，都会被 leader 节点上的日志所覆盖。这并不会产生什么问题，因为某些选举上的限制，如果 follower 上的日志与 leader 不一致，那么该日志在 follower 上**一定是未提交的**。未提交的日志并不会应用到状态机，也不会被外部的客户端感知到。

要使得 follower 的日志集合跟自己保持完全一致，leader 必须先找到二者间**最后一次**达成一致的地方。因为一旦这条日志达成一致，在这之前的日志一定也都一致（回忆下前文）。这个确认操作是在 AppendEntries RPC 的一致性检查步骤完成的。

Leader 针对每个 follower 都维护一个 **next index**，表示下一条需要发送给该follower 的日志索引。当一个 leader 刚刚上任时，它初始化所有 next index 值为自己最后一条日志的 index+1。但凡某个 follower 的日志跟 leader 不一致，那么下次 AppendEntries RPC 的一致性检查就会失败。在被 follower 拒绝这次 Append Entries RPC 后，leader 会减少 next index 的值并进行重试。

最终一定会存在一个 next index 使得 leader 和 follower 在这之前的日志都保持一致。极端情况下 next index 为1，表示 follower 没有任何日志与 leader 一致，leader 必须从第一条日志开始同步。

针对每个 follower，一旦确定了 next index 的值，leader 便开始从该 index 同步日志，follower 会删除掉现存的不一致的日志，保留 leader 最新同步过来的。

整个集群的日志会在这个简单的机制下自动趋于一致。此外要注意，**leader 从来不会覆盖或者删除自己的日志**，而是强制 follower 与它保持一致。

这就要求集群票选出的 leader 一定要具备“日志的正确性”，这也就关联到了前文提到的：选举上的限制。

下一章我们将对此详细讨论。

# 4. 安全性及正确性

前面的章节我们讲述了 Raft 算法是如何选主和复制日志的，然而到目前为止我们描述的**这套机制还不能保证每个节点的状态机会严格按照相同的顺序 apply 日志**。想象以下场景：

1. Leader 将一些日志复制到了大多数节点上，进行 commit 后发生了宕机。
2. 某个 follower 并没有被复制到这些日志，但它参与选举并当选了下一任 leader。
3. 新的 leader 又同步并 commit 了一些日志，这些日志覆盖掉了其它节点上的上一任 committed 日志。
4. 各个节点的状态机可能 apply 了不同的日志序列，出现了不一致的情况。

因此我们需要对“选主+日志复制”这套机制加上一些额外的限制，来保证**状态机的安全性**，也就是 Raft 算法的正确性。

## 4.1 对选举的限制

我们再来分析下前文所述的 committed 日志被覆盖的场景，根本问题其实发生在第2步。Candidate 必须有足够的资格才能当选集群 leader，否则它就会给集群带来不可预料的错误。Candidate 是否具备这个资格可以在选举时添加一个小小的条件来判断，即：

**每个 candidate 必须在 RequestVote RPC 中携带自己本地日志的最新 (term, index)，如果 follower 发现这个 candidate 的日志还没有自己的新，则拒绝投票给该 candidate。**

Candidate 想要赢得选举成为 leader，必须得到集群大多数节点的投票，那么**它的日志就一定至少不落后于大多数节点**。又因为一条日志只有复制到了大多数节点才能被 commit，因此**能赢得选举的 candidate 一定拥有所有 committed 日志**。

因此前一篇文章我们才会断定地说：Follower 不可能比 leader 多出一些 committed 日志。

比较两个 (term, index) 的逻辑非常简单：如果 term 不同 term 更大的日志更新，否则 index 大的日志更新。

## 4.2 对提交的限制

除了对选举增加一点限制外，我们还需对 commit 行为增加一点限制，来完成我们 Raft 算法核心部分的最后一块拼图。

回忆下什么是 commit：

> 当 leader 得知某条日志被集群过半的节点复制成功时，就可以进行 commit，committed 日志一定最终会被状态机 apply。

所谓 commit 其实就是对日志简单进行一个标记，表明其可以被 apply 到状态机，并针对相应的客户端请求进行响应。

然而 leader 并不能在任何时候都随意 commit 旧任期留下的日志，即使它已经被复制到了大多数节点。Raft 论文给出了一个经典场景：

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b1b4e211fca44ca9bf5a67639e92d4d4~tplv-k3u1fbpfcp-watermark.image)

上图从左到右按时间顺序模拟了问题场景。

**阶段a**：S1 是 leader，收到请求后将 (term2, index2) 只复制给了 S2，尚未复制给 S3 ~ S5。

**阶段b**：S1 宕机，S5 当选 term3 的 leader（S3、S4、S5 三票），收到请求后保存了 (term3, index2)，尚未复制给任何节点。

**阶段c**：S5 宕机，S1 恢复，S1 重新当选 term4 的 leader，继续将 (term2, index2) 复制给了 S3，已经满足大多数节点，我们将其 commit。

**阶段d**：S1 又宕机，S5 恢复，S5 重新当选 leader（S2、S3、S4 三票），将 (term3, inde2) 复制给了所有节点并 commit。注意，此时发生了致命错误，已经 committed 的 (term2, index2) 被 (term3, index2) 覆盖了。

为了避免这种错误，我们需要添加一个额外的限制：

**Leader 只允许 commit 包含当前 term 的日志。**

针对上述场景，问题发生在阶段c，即使作为 term4 leader 的 S1 将 (term2, index2) 复制给了大多数节点，它也不能直接将其 commit，而是必须等待 term4 的日志到来并成功复制后，一并进行 commit。

**阶段e**：在添加了这个限制后，要么 (term2, index2) 始终没有被 commit，这样 S5 在阶段d将其覆盖就是安全的；要么 (term2, index2) 同 (term4, index3) 一起被 commit，这样 S5 根本就无法当选 leader，因为大多数节点的日志都比它新，也就不存在前边的问题了。

以上便是对算法增加的两个小限制，它们对确保状态机的安全性起到了至关重要的作用。

至此我们对 Raft 算法的核心部分，已经介绍完毕。下一章我们会介绍两个同样描述于论文内的辅助技术：集群成员变更和日志压缩，它们都是在 Raft 工程实践中必不可少的部分。

# 5. 集群成员变更与日志压缩

尽管我们已经通过前几章了解了 Raft 算法的核心部分，但相较于算法理论来说，在工程实践中仍有一些现实问题需要我们去面对。Raft 非常贴心的在论文中给出了两个常见问题的解决方案，它们分别是：

1. **集群成员变更**：如何安全地改变集群的节点成员。
2. **日志压缩**：如何解决日志集合无限制增长带来的问题。

本文我们将分别讲解这两种技术。

## 5.1 集群成员变更

在前文的理论描述中我们都假设了集群成员是不变的，然而在实践中有时会需要替换宕机机器或者改变复制级别（即增减节点）。一种最简单暴力达成目的的方式就是：停止集群、改变成员、启动集群。这种方式在执行时会导致集群整体不可用，此外还存在手工操作带来的风险。

为了避免这样的问题，Raft 论文中给出了一种无需停机的、自动化的改变集群成员的方式，其实本质上还是利用了 Raft 的核心算法，将集群成员配置作为一个特殊日志从 leader 节点同步到其它节点去。

### 5.1.1 直接切换集群成员配置

先说结论：**所有将集群从旧配置直接完全切换到新配置的方案都是不安全的**。

因此我们不能想当然的将新配置直接作为日志同步给集群并 apply。因为我们不可能让集群中的全部节点在“**同一时刻**”**原子地**切换其集群成员配置，所以在切换期间不同的节点看到的集群视图可能存在不同，最终可能导致集群存在多个 leader。

为了理解上述结论，我们来看一个实际出现问题的场景，下图对其进行了展现。

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/00cd4fb677dc4a8987c015b6f074a3b8~tplv-k3u1fbpfcp-watermark.image) *图5-1*

**阶段a.** 集群存在 S1 ~ S3 三个节点，我们将该成员配置表示为 C-old，绿色表示该节点当前视图（成员配置）为 C-old，其中红边的 S3 为 leader。

**阶段b.** 集群新增了 S4、S5 两个节点，该变更从 leader 写入，我们将 S1 ~ S5 的五节点新成员配置表示为 C-new，蓝色表示该节点当前视图为 C-new。

**阶段c.** 假设 S3 短暂宕机触发了 S1 与 S5 的超时选主。

**阶段d.** S1 向 S2、S3 拉票，S5 向其它全部四个节点拉票。由于 S2 的日志并没有比 S1 更新，因此 S2 可能会将选票投给 S1，S1 两票当选（因为 S1 认为集群只有三个节点）。而 S5 肯定会得到 S3、S4 的选票，因为 S1 感知不到 S4，没有向它发送 RequestVote RPC，并且 S1 的日志落后于 S3，S3 也一定不会投给 S1，结果 S5 三票当选。最终集群出现了多个主节点的致命错误，也就是所谓的脑裂。

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1c063b8c7e824bb88ffe572439c90975~tplv-k3u1fbpfcp-watermark.image) *图5-2*

上图来自论文，用不同的形式展现了和图5-1相同的问题。颜色代表的含义与图5-1是一致的，在 **problem: two disjoint majorities** 所指的时间点，集群可能会出现两个 leader。

但是，多主问题并不是在任何新老节点同时选举时都一定可能出现的，社区一些文章在举多主的例子时可能存在错误，下面是一个案例（笔者学习 Raft 协议也从这篇文章中受益匪浅，应该是作者行文时忽略了。文章很赞，建议大家参考学习）：

> 来源：[zhuanlan.zhihu.com/p/27207160](https://link.juejin.cn/?target=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F27207160)

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/85436861e4484446afac9192d02bc292~tplv-k3u1fbpfcp-watermark.image) *图5-3*

该假想场景类似图5-1的阶段d，模拟过程如下：

1. S1 为集群原 leader，集群新增 S4、S5，该配置被推给了 S3，S2 尚未收到。
2. 此时 S1 发生短暂宕机，S2、S3 分别触发选主。
3. 最终 S2 获得了 S1 和自己的选票，S3 获得了 S4、S5 和自己的选票，集群出现两个 leader。

图5-3过程看起来好像和图5-1没有什么大的不同，只是参与选主的节点存在区别，然而事实是**图5-3的情况是不可能出现的**。

注意：Raft 论文中传递集群变更信息也是通过日志追加实现的，所以也受到选主的限制。很多读者对选主限制中比较的日志是否必须是 committed 产生疑惑，回看下在《安全性》一文中的描述：

> 每个 candidate 必须在 RequestVote RPC 中携带自己本地日志的最新 (term, index)，如果 follower 发现这个 candidate 的日志还没有自己的新，则拒绝投票给该 candidate。

这里再帮大家明确下，论文里确实间接表明了，**选主时比较的日志是不要求 committed 的，只需比较本地的最新日志就行**！

回到图5-3，不可能出现的原因在于，S1 作为原 leader 已经第一个保存了新配置的日志，而 S2 尚未被同步这条日志，根据上一章《安全性》我们讲到的**选主限制**，**S1 不可能将选票投给 S2**，因此 S2 不可能成为 leader。

### 5.1.2 两阶段切换集群成员配置

Raft 使用一种两阶段方法平滑切换集群成员配置来避免遇到前一节描述的问题，具体流程如下：

**阶段一**

1. 客户端将 C-new 发送给 leader，leader 将 C-old 与 C-new 取**并集**并立即apply，我们表示为 **C-old,new**。
2. Leader 将 C-old,new 包装为日志同步给其它节点。
3. Follower 收到 C-old,new 后立即 apply，当 **C-old,new 的大多数节点（即 C-old 的大多数节点和 C-new 的大多数节点）**都切换后，leader 将该日志 commit。

**阶段二**

1. Leader 接着将 C-new 包装为日志同步给其它节点。
2. Follower 收到 C-new 后立即 apply，如果此时发现自己不在 C-new 列表，则主动退出集群。
3. Leader 确认 **C-new 的大多数节点**都切换成功后，给客户端发送执行成功的响应。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3af62adcab904fbc8d606b1ad29d9a34~tplv-k3u1fbpfcp-watermark.image)

上图展示了该流程的时间线。虚线表示已经创建但尚未 commit 的成员配置日志，实线表示 committed 的成员配置日志。

为什么该方案可以保证不会出现多个 leader？我们来按流程逐阶段分析。

**阶段1. C-old,new 尚未 commit**

该阶段所有节点的配置要么是 C-old，要么是 C-old,new，但无论是二者哪种，只要原 leader 发生宕机，新 leader 都**必须得到大多数 C-old 集合内节点的投票**。

以图5-1场景为例，S5 在阶段d根本没有机会成为 leader，因为 C-old 中只有 S3 给它投票了，不满足大多数。

**阶段2. C-old,new 已经 commit，C-new 尚未下发**

该阶段 C-old,new 已经 commit，可以确保已经被 C-old,new 的大多数节点（**再次强调：C-old 的大多数节点和 C-new 的大多数节点**）复制。

因此当 leader 宕机时，新选出的 leader 一定是已经拥有 C-old,new 的节点，不可能出现两个 leader。

**阶段3. C-new 已经下发但尚未 commit**

该阶段集群中可能有三种节点 C-old、C-old,new、C-new，但由于已经经历了阶段2，因此 C-old 节点不可能再成为 leader。而无论是 C-old,new 还是 C-new 节点发起选举，都需要经过大多数 C-new 节点的同意，因此也不可能出现两个 leader。

**阶段4. C-new 已经 commit**

该阶段 C-new 已经被 commit，因此只有 C-new 节点可以得到大多数选票成为 leader。此时集群已经安全地完成了这轮变更，可以继续开启下一轮变更了。

以上便是对该两阶段方法可行性的分步验证，Raft 论文将该方法称之为**共同一致**（**Joint Consensus**）。

关于集群成员变更另一篇更详细的论文还给出了其它方法，简单来说就是论证**一次只变更一个节点的**的正确性，并给出解决可用性问题的优化方案。感兴趣的同学可以参考：[《Consensus: Bridging Theory and Practice》](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fongardie%2Fdissertation)。

## 5.2 日志压缩

我们知道 Raft 核心算法维护了日志的一致性，通过 apply 日志我们也就得到了一致的状态机，客户端的操作命令会被包装成日志交给 Raft 处理。然而在实际系统中，客户端操作是连绵不断的，但日志却不能无限增长，首先它会占用很高的存储空间，其次每次系统重启时都需要完整回放一遍所有日志才能得到最新的状态机。

因此 Raft 提供了一种机制去清除日志里积累的陈旧信息，叫做**日志压缩**。

**快照**（**Snapshot**）是一种常用的、简单的日志压缩方式，ZooKeeper、Chubby 等系统都在用。简单来说，就是将某一时刻系统的状态 dump 下来并落地存储，这样该时刻之前的所有日志就都可以丢弃了。所以大家对“压缩”一词不要产生错误理解，我们并没有办法将状态机快照“解压缩”回日志序列。

注意，**在 Raft 中我们只能为 committed 日志做 snapshot**，因为只有 committed 日志才是确保最终会应用到状态机的。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/00ef776fc5b64e56bd2abbfae3371329~tplv-k3u1fbpfcp-watermark.image)

上图展示了一个节点用快照替换了 (term1, index1) ~ (term3, index5) 的日志。

快照一般包含以下内容：

1. **日志的元数据**：最后一条被该快照 apply 的日志 term 及 index
2. **状态机**：前边全部日志 apply 后最终得到的状态机

当 leader 需要给某个 follower 同步一些旧日志，但这些日志已经被 leader 做了快照并删除掉了时，leader 就需要把该快照发送给 follower。

同样，当集群中有新节点加入，或者某个节点宕机太久落后了太多日志时，leader 也可以直接发送快照，大量节约日志传输和回放时间。

同步快照使用一个新的 RPC 方法，叫做 **InstallSnapshot RPC**。

至此我们已经将 Raft 论文中的内容基本讲解完毕了。[《In Search of an Understandable Consensus Algorithm (Extended Version)》](https://link.juejin.cn/?target=https%3A%2F%2Fraft.github.io%2Fraft.pdf) 毕竟只有18页，更加侧重于理论描述而非工程实践。如果你想深入学习 Raft，或自己动手写一个靠谱的 Raft 实现，[《Consensus: Bridging Theory and Practice》](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fongardie%2Fdissertation) 是你参考的不二之选。

接下来我们将额外讨论一下关于线性一致性和 Raft 读性能优化的内容。

# 6. 线性一致性与读性能优化

## 6.1 什么是线性一致性？

在该系列首篇《基本概念》中我们提到过：在分布式系统中，为了消除单点提高系统可用性，通常会使用副本来进行容错，但这会带来另一个问题，即如何保证多个副本之间的**一致性**。

什么是一致性？所谓一致性有很多种模型，不同的模型都是用来评判一个并发系统正确与否的不同程度的标准。而我们今天要讨论的是**强一致性**（Strong Consistency）模型，也就是**线性一致性**（Linearizability），我们经常听到的 CAP 理论中的 C 指的就是它。

其实我们在第一篇就已经简要描述过何为线性一致性：

> 所谓的强一致性（线性一致性）并不是指集群中所有节点在任一时刻的状态必须完全一致，而是指一个目标，即让一个分布式系统看起来只有一个数据副本，并且读写操作都是原子的，这样应用层就可以忽略系统底层多个数据副本间的同步问题。也就是说，我们可以将一个强一致性分布式系统当成一个整体，一旦某个客户端成功的执行了写操作，那么所有客户端都一定能读出刚刚写入的值。即使发生网络分区故障，或者少部分节点发生异常，整个集群依然能够像单机一样提供服务。

“**像单机一样提供服务**”从感官上描述了一个线性一致性系统应该具备的特性，那么我们该如何判断一个系统是否具备线性一致性呢？通俗来说就是不能读到旧（stale）数据，但具体分为两种情况：

- 对于调用时间存在重叠（**并发**）的请求，生效顺序可以任意确定。
- 对于调用时间存在先后关系（**偏序**）的请求，后一个请求不能违背前一个请求确定的结果。

只要根据上述两条规则即可判断一个系统是否具备线性一致性。下面我们来看一个非线性一致性系统的例子。

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/286a2248256b4b9f84ff613318432103~tplv-k3u1fbpfcp-watermark.image)

*本节例图均来自《Designing Data-Intensive Application》，作者 Martin Kleppmann*

如上图所示，裁判将世界杯的比赛结果写入了主库，Alice 和 Bob 所浏览的页面分别从两个不同的从库读取，但由于存在主从同步延迟，Follower 2 的本次同步延迟高于 Follower 1，最终导致 Bob 听到了 Alice 的惊呼后刷新页面看到的仍然是比赛进行中。

虽然线性一致性的基本思想很简单，只是要求**分布式系统看起来只有一个数据副本**，但在实际中还是有很多需要关注的点，我们继续看几个例子。

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d22cb375fc664ac2a5585417a50028de~tplv-k3u1fbpfcp-watermark.image)

上图从客户端的**外部视角**展示了多个用户同时请求读写一个系统的场景，每条柱形都是用户发起的一个请求，左端是请求发起的时刻，右端是收到响应的时刻。由于网络延迟和系统处理时间并不固定，所以柱形长度并不相同。

- `x` 最初的值为 `0`，Client C 在某个时间段将 `x` 写为 `1`。
- Client A 第一个读操作位于 Client C 的写操作之前，因此必须读到原始值 `0`。
- Client A 最后一个读操作位于 Client C 的写操作之后，如果系统是线性一致的，那么必须读到新值 `1`。
- 其它与写操作重叠的所有读操作，既可能返回 `0`，也可能返回 `1`，因为我们并不清楚写操作在哪个时间段内哪个精确的点生效，这种情况下读写是**并发**的。

仅仅是这样的话，仍然不能说这个系统满足线性一致。假设 Client B 的第一次读取返回了 `1`，如果 Client A 的第二次读取返回了 `0`，那么这种场景并不破坏上述规则，但这个系统仍不满足线性一致，因为客户端在写操作执行期间看到 `x` 的值在新旧之间来回翻转，这并不符合我们期望的“看起来只有一个数据副本”的要求。

所以我们需要额外添加一个约束，如下图所示。

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d9c3635c4d245188997538e36cb972d~tplv-k3u1fbpfcp-watermark.image)

在任何一个客户端的读取返回新值后，所有客户端的后续读取也必须返回新值，这样系统便满足线性一致了。

我们最后来看一个更复杂的例子，继续细化这个时序图。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5d2ab2d829fe4fa086298245edc24dea~tplv-k3u1fbpfcp-watermark.image)

如上图所示，每个读写操作在某个特定的时间点都是**原子性的生效**，我们在柱形中用竖线标记出生效的时间点，将这些标记按时间顺序连接起来。那么线性一致的要求就是：**连线总是按照时间顺序向右移动，而不会向左回退**。所以这个连线结果必定是一个**有效的寄存器读写序列**：任何客户端的每次读取都必须返回该条目最近一次写入的值。

> 线性一致性并非限定在分布式环境下，在单机单核系统中可以简单理解为“寄存器”的特性。

Client B 的最后一次读操作并不满足线性一致，因为在连线向右移动的前提下，它读到的值是错误的（因为Client A 已经读到了由 Client C 写入的 `4`）。此外这张图里还有一些值得指出的细节点，可以解开很多我们在使用线性一致系统时容易产生的误解：

- Client B 的首个读请求在 Client D 的首个写请求和 Client A 的首个写请求之前发起，但最终读到的却是最后由 Client A 写成功之后的结果。
- Client A 尚未收到首个写请求成功的响应时，Client B 就读到了 Client A 写入的值。

上述现象在线性一致的语义下都是合理的。

所以**线性一致性**（Linearizability）除了叫**强一致性**（Strong Consistency）外，还叫做**原子一致性**（Atomic Consistency）、**立即一致性**（Immediate Consistency）或**外部一致性**（External Consistency），这些名字看起来都是比较贴切的。

## 6.2 Raft 线性一致性读

在了解了什么是线性一致性之后，我们将其与 Raft 结合来探讨。首先需要明确一个问题，使用了 Raft 的系统都是线性一致的吗？不是的，Raft 只是提供了一个基础，要实现整个系统的线性一致还需要做一些额外的工作。

假设我们期望基于 Raft 实现一个线性一致的分布式 kv 系统，让我们从最朴素的方案开始，指出每种方案存在的问题，最终使整个系统满足线性一致性。

### 6.2.1 写主读从缺陷分析

写操作并不是我们关注的重点，如果你稍微看了一些理论部分就应该知道，所有写操作都要作为提案从 leader 节点发起，当然所有的写命令都应该简单交给 leader 处理。真正关键的点在于**读操作的处理方式，这涉及到整个系统关于一致性方面的取舍**。

在该方案中我们假设读操作直接简单地向 follower 发起，那么由于 Raft 的 Quorum 机制（大部分节点成功即可），针对某个提案在某一时间段内，集群可能会有以下两种状态：

- 某次写操作的日志尚未被复制到一少部分 follower，但 leader 已经将其 commit。
- 某次写操作的日志已经被同步到所有 follower，但 leader 将其 commit 后，心跳包尚未通知到一部分 follower。

以上每个场景客户端都可能读到**过时的数据**，整个系统显然是不满足线性一致的。

### 6.2.2 写主读主缺陷分析

在该方案中我们限定，所有的读操作也必须经由 leader 节点处理，读写都经过 leader 难道还不能满足线性一致？**是的！！** 并且该方案存在不止一个问题！！

**问题一：状态机落后于 committed log 导致脏读**

回想一下前文讲过的，我们在解释什么是 commit 时提到了写操作什么时候可以响应客户端：

> 所谓 commit 其实就是对日志简单进行一个标记，表明其可以被 apply 到状态机，并针对相应的客户端请求进行响应。

也就是说一个提案只要被 leader commit 就可以响应客户端了，Raft 并没有限定提案结果在返回给客户端前必须先应用到状态机。所以从客户端视角当我们的某个写操作执行成功后，下一次读操作可能还是会读到旧值。

这个问题的解决方式很简单，在 leader 收到读命令时我们只需记录下当前的 commit index，当 apply index 追上该 commit index 时，即可将状态机中的内容响应给客户端。

**问题二：网络分区导致脏读**

假设集群发生网络分区，旧 leader 位于少数派分区中，而且此刻旧 leader 刚好还未发现自己已经失去了领导权，当多数派分区选出了新的 leader 并开始进行后续写操作时，连接到旧 leader 的客户端可能就会读到旧值了。

因此，仅仅是直接读 leader 状态机的话，系统仍然不满足线性一致性。

### 6.2.3 Raft Log Read

为了确保 leader 处理读操作时仍拥有领导权，我们可以将读请求同样作为一个提案走一遍 Raft 流程，当这次读请求对应的日志可以被应用到状态机时，leader 就可以读状态机并返回给用户了。

这种读方案称为 **Raft Log Read**，也可以直观叫做 **Read as Proposal**。

为什么这种方案满足线性一致？因为该方案根据 commit index 对所有读写请求都一起做了线性化，这样每个读请求都能感知到状态机在执行完前一写请求后的最新状态，将读写日志一条一条的应用到状态机，整个系统当然满足线性一致。但该方案的缺点也非常明显，那就是**性能差**，读操作的开销与写操作几乎完全一致。而且由于所有操作都线性化了，我们无法并发读状态机。

## 6.3 Raft 读性能优化

接下来我们将介绍几种优化方案，它们在不违背系统线性一致性的前提下，大幅提升了读性能。

### 6.3.1 Read Index

与 Raft Log Read 相比，Read Index 省掉了同步 log 的开销，能够**大幅提升**读的**吞吐**，**一定程度上降低**读的**时延**。其大致流程为：

1. Leader 在收到客户端读请求时，记录下当前的 commit index，称之为 read index。
2. Leader 向 followers 发起一次心跳包，这一步是为了确保领导权，避免网络分区时少数派 leader 仍处理请求。
3. 等待状态机**至少**应用到 read index（即 apply index **大于等于** read index）。
4. 执行读请求，将状态机中的结果返回给客户端。

这里第三步的 apply index **大于等于** read index 是一个关键点。因为在该读请求发起时，我们将当时的 commit index 记录了下来，只要使客户端读到的内容在该 commit index 之后，那么结果**一定都满足线性一致**（如不理解可以再次回顾下前文线性一致性的例子以及2.2中的问题一）。

### 6.3.2 Lease Read

与 Read Index 相比，Lease Read 进一步省去了网络交互开销，因此更能**显著降低**读的**时延**。

基本思路是 leader 设置一个**比选举超时（Election Timeout）更短的时间作为租期**，在租期内我们可以相信其它节点一定没有发起选举，集群也就一定不会存在脑裂，所以在这个时间段内我们直接读主即可，而非该时间段内可以继续走 Read Index 流程，Read Index 的心跳包也可以为租期带来更新。

Lease Read 可以认为是 Read Index 的时间戳版本，额外依赖时间戳会为算法带来一些不确定性，如果时钟发生漂移会引发一系列问题，因此需要谨慎的进行配置。

### 6.3.3 Follower Read

在前边两种优化方案中，无论我们怎么折腾，核心思想其实只有两点：

- 保证在读取时的最新 commit index 已经被 apply。
- 保证在读取时 leader 仍拥有领导权。

这两个保证分别对应2.2节所描述的两个问题。

其实无论是 Read Index 还是 Lease Read，最终目的都是为了解决第二个问题。换句话说，读请求最终一定都是由 leader 来承载的。

那么读 follower 真的就不能满足线性一致吗？其实不然，这里我们给出一个可行的读 follower 方案：**Follower 在收到客户端的读请求时，向 leader 询问当前最新的 commit index，反正所有日志条目最终一定会被同步到自己身上，follower 只需等待该日志被自己 commit 并 apply 到状态机后，返回给客户端本地状态机的结果即可**。这个方案叫做 **Follower Read**。

注意：Follower Read 并不意味着我们在读过程中完全不依赖 leader 了，在保证线性一致性的前提下完全不依赖 leader 理论上是不可能做到的。

------



# 分布式事务





# 分布式锁







































