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







































