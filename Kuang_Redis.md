```bash
#路径
/usr/local/bin
redis-server c-config/redis.conf #指定配置文件启动
redis-cli -p 7379 #开启服务
ps -ef|grep redis #查看redis进程是否开启
set key value
get key
select 3 #切换数据库
dbsize #数据库大小
keys * #显示所有的key
flushdb #清除当前数据库
flushall #清除所有数据库
exists key #判断key是否存在 1表示存在 0表示不存在
move key 1 #将key从当前数据库移除
expire key 10 #设置key 10秒后过期
ttl key	#查看key还剩多少秒过期 -2表示已经过期
```



# Redis入门

## Linux安装

1. 下载安装包 redis 

2. 解压Redis的安装包到 /opt目录下

   1. ![image-20210602230629810](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602230629810.png)

3. 进入解压后的文件

   1. ![image-20210602230653970](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602230653970.png)

4. 进入解压后的文件, 可以看到redis.config

5. 基本的安装环境 gcc

   1. ```bash
      #更新Ubuntu安装包信息：
      sudo apt update
      #安装编译功能包
      sudo apt install build-essential
      #安装开发文档
      sudo apt-get install manpages-dev
      # 确认gcc版本
      gcc --version
      # make 所有需要执行的文件
      make
      
      #安装
      make install
      ```

   2. ![image-20210602231752799](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602231752799.png)

   3. ![image-20210602231823312](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602231823312.png)

6. redis的默认安装路径   `/usr/local/bin`

   1. ![image-20210602232035488](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602232035488.png)

7. 将redis配置文件,  复制到我们当前目录下

   1. ![image-20210602232224031](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602232224031.png)

8. redis默认不是后台启动的, 修改配置文件!

   1. ![image-20210602232529143](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602232529143.png)

9. 启动redis服务 , 通过指定的config文件启动

   1. ![image-20210602232729327](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602232729327.png)

10. 连接 `redis-cli -p 6379`

    1. ![image-20210602232920351](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602232920351.png)

11. 查看redis进程是否开启

    1. ![image-20210602233044605](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602233044605.png)

12. 如何关闭redis服务 `shutdowm`  `exit`

    1. ![image-20210602233214548](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602233214548.png)

13. 再次查看redis进程是否存在

    1. ![image-20210602233233406](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602233233406.png)

14. 后面会使用单机多集群使用



## 测试性能

**redis-benchmark**是一个压力测试工具



![image-20210602233552594](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602233552594.png)

- 我们来简单测试一下

```bash
#测试: 100个并发连接  100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```

- 如何查看这些分析呢?
  - ![image-20210602234028656](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602234028656.png)
  - ![image-20210602234303456](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602234303456.png)
  - 对我们的10万个请求进行写入测试
  - 100个并发客服端
  - 每次写入3个字节
  - 只有一个服务器来处理这些请求, 单击性能
  - 所有请求在8毫秒内处理完成
  - 每一秒处理46860.36个请求



## 基础知识

- redis默认有16个数据库, 默认使用第0个

  - ![image-20210602234540778](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602234540778.png)

- 可以使用select进行切换数据库

  - ```bash
    127.0.0.1:6379> select 3  #切换数据
    OK
    127.0.0.1:6379[3]> dbsize #数据库大小
    (integer) 0
    
    ```

  - ![image-20210602234903987](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210602234903987.png)

  - ```bash
    127.0.0.1:6379[3]> keys * #查看当前数据库所有的key
    1) "name"
    ```

- 清除当前数据库

  - ```bash
    127.0.0.1:6379[3]> flushdb #清除当前数据库
    OK
    127.0.0.1:6379[3]> keys *
    (empty array)
    
    ```

- 清除全部数据库内容

  - ```bash
    127.0.0.1:6379[3]> flushall #清除全部数据库
    OK
    127.0.0.1:6379[3]> keys *
    (empty array)
    ```

- 思考: 为什么redis是6379

### Redis是单线程的

Redis是很快的, Redis是基于内存操作, CPU不是Redis性能瓶颈, Redis瓶颈是根据及其的内存和网路贷款, 既然可以使用单线程来实现, 就是用单线程了, 所以就使用了单线程了

Redis 是C语言写的, 官方数据 100000+ 的QPS, 完全不比同样使用key-value的Memecache差

### Redis为什么单线程还这么快?

1. 误区1: 高性能的服务器一定是多线程的
2. 误区2: 多线程(CPU上下文切换) 一定比单线程效率高
3. CPU>内存>硬盘速度有所了解

- 核心: redis是将所有的数据全部放在内存中, 所以使用单线程去操作效率是最高的  多线程(CPU上下文切换: 耗时的操作), 对于内存系统来说, 如果没有上下文切换效率就是最高的, 多次读写都是一个CPU上的, 在内存情况下, 这个就是最佳的方案 





# 五大数据类型

> 官网文档

![image-20210603000414728](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603000414728.png)



## Redis-Key

```bash
redis-server c-config/redis.conf #指定配置文件启动
redis-cli -p 7379 #开启服务
ps -ef|grep redis #查看redis进程是否开启
set key value
get key
select 3 #切换数据库
dbsize #数据库大小
keys * #显示所有的key
flushdb #清除当前数据库
flushall #清除所有数据库
exists key #判断key是否存在 1表示存在 0表示不存在
move key 1 #将key从当前数据库移除
expire key 10 #设置key 10秒后过期
ttl key	#查看key还剩多少秒过期 -2表示已经过期
type key #查看key的类型

##apend
append key "string" #向key对应的value后面追加"string" 如果key不存在等于set key
strlen key #获取字符串长度

##增减
incr key #key对应value +1 (自增1)
decr key #key对应value -1 (自减1)
incrby key num  #key对应value +num
decrby key num  #key对应value -num

##字符串范围
getrange key startIndex endIndex  #获取key对应字符串从 startIdnex到endIndex(inclusive)的字符串 endIndex=-1表示一直截取到字符串末尾

##替换
setrange key offset "string" #从key对应value从offset开始替换为指定的 "string"

#setex (set with expire)  #设置过期时间
setex key seconds value #设置key对应的value在seconds秒后过期
#setnx (set if not exist) #不存在再设置key (在分布式锁中会常常使用)
setnx key value #如果不存在key就设置key为value, 如果已经存在就没办法覆盖

##批量操作
mset k1 v1 k2 v2 k3 v3  #批量设置k1=v1 k2=v2 k3=v3
mget k1 k2 k3 #批量获取k1 k2 k3的value

msetnx k1 v1 k4 v4 #如果不存在就批量设置 k1=v1 k4=v4 但是是原子操作, 因为k1存在, 即便k4不存在, k4也没有设置成功


#对象
#初级方法
set user:1 {name:ccq, age:3} 设置1号user对象 name=ccq age=3 值是一个json字符来保存一个对象
#高阶方法
#这里的key是一个巧妙的设计  user:{1}:{field} 如此设置
mset user:1:name ccq user:1:age 2 #设置1号user对象 name=ccq age=2
mget user:1:name user:1:age #获取1号user对象的name age属性


getset key value #先get key的值, 再set为value
#如果不存在值 返回nil, 第二次get就会获取到设置的值 
#如果存在值 返回get时的值, 并且设置为新的值
127.0.0.1:6379> getset db redis
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db MongoDB
"redis"
127.0.0.1:6379> get db
"MongoDB"

```

遇到不会的命令可以在官网查看帮助文档



## String(字符串)

大部分只会用String

```bash
##apend
append key "string" #向key对应的value后面追加"string" 如果key不存在等于set key
strlen key #获取字符串长度

##增减
incr key #key对应value +1 (自增1)
decr key #key对应value -1 (自减1)
incrby key num  #key对应value +num
decrby key num  #key对应value -num

##字符串范围
getrange key startIndex endIndex  #获取key对应字符串从 startIdnex到endIndex(inclusive)的字符串 endIndex=-1表示一直截取到字符串末尾

##替换
setrange key offset "string" #从key对应value从offset开始替换为指定的 "string"

#setex (set with expire)  #设置过期时间
setex key seconds value #设置key对应的value在seconds秒后过期
#setnx (set if not exist) #不存在再设置key (在分布式锁中会常常使用)
setnx key value #如果不存在key就设置key为value, 如果已经存在就没办法覆盖

##批量操作
mset k1 v1 k2 v2 k3 v3  #批量设置k1=v1 k2=v2 k3=v3
mget k1 k2 k3 #批量获取k1 k2 k3的value

msetnx k1 v1 k4 v4 #如果不存在就批量设置 k1=v1 k4=v4 但是是原子操作, 因为k1存在, 即便k4不存在, k4也没有设置成功


#对象
#初级方法
set user:1 {name:ccq, age:3} 设置1号user对象 name=ccq age=3 值是一个json字符来保存一个对象
#高阶方法
#这里的key是一个巧妙的设计  user:{1}:{field} 如此设置
mset user:1:name ccq user:1:age 2 #设置1号user对象 name=ccq age=2
mget user:1:name user:1:age #获取1号user对象的name age属性


getset key value #先get key的值, 再set为value
#如果不存在值 返回nil, 第二次get就会获取到设置的值 
#如果存在值 返回get时的值, 并且设置为新的值
127.0.0.1:6379> getset db redis
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db MongoDB
"redis"
127.0.0.1:6379> get db
"MongoDB"
```

String类似的使用场景: value除了是字符串还可以是数字

- 计数器
- 统计多单位数量
- 粉丝数
- 对象缓存



## List (列表)

- 基本的数据类型, 列表
- 在redis里面, 我们可以将list看成栈, 队列, 
- 所有的list操作都是l开头 不区分大小写

```bash
#插入
lpush
rpush
127.0.0.1:6379> lpush list one  #将一个值或者多个值 插入列表头部(左)
(integer) 1
127.0.0.1:6379> lpush list two
(integer) 2
127.0.0.1:6379> lpush list three
(integer) 3
127.0.0.1:6379> lrange list 0 -1 #获取列表所有的元素
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> lrange list 0 1 #获取列表指定范围的元素
1) "three"
2) "two"
127.0.0.1:6379> Rpush list right #将一个值放入列表的尾部(右)
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"

#移除
lpop
rpop
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
127.0.0.1:6379> lpop list  #移除list第一个元素
"three"
127.0.0.1:6379> rpop list #移除list最后一个元素
"right"
127.0.0.1:6379> lrange list 0 -1
1) "two"
2) "one"

#获取
lindex
127.0.0.1:6379> lrange list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> lindex list 1  #通过下标获取list中的某一个值
"one"
127.0.0.1:6379> lindex list 0
"two"

#长度
llen

127.0.0.1:6379> lpush list one
(integer) 1
127.0.0.1:6379> lpush list two
(integer) 2
127.0.0.1:6379> lpush list three
(integer) 3
127.0.0.1:6379> llen list  #返回列表长度
(integer) 3

#移除指定的值
lrem

127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "three"
3) "two"
4) "one"
127.0.0.1:6379> lrem list 1 one  #移除list中指定个数的value, 精确匹配
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "three"
3) "two"
127.0.0.1:6379> lrem list 1 three
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
127.0.0.1:6379> lpush list three
(integer) 3
127.0.0.1:6379> lrem list 2 three
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "two"


#截断
ltrim  修剪

127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "hello1"
(integer) 2
127.0.0.1:6379> rpush mylist "hello2"
(integer) 3
127.0.0.1:6379> rpush mylist "hello3"
(integer) 4
127.0.0.1:6379> ltrim mylist 1 2 #截取指定下标范围内的元素 闭区间, 这个list已经被改变了
OK
127.0.0.1:6379> lrange mylist 0 -1
1) "hello1"
2) "hello2"


#移除放入组合命令
rpoplpush #移除列表的最后一个元素, 将它移动到新的列表中

127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "hello1"
(integer) 2
127.0.0.1:6379> rpush mylist "hello2"
(integer) 3
127.0.0.1:6379> rpoplpush mylist myotherlist #移除列表的最后一个元素, 将它移动到新的列表中
"hello2"
127.0.0.1:6379> lrange mylist 0 -1 #查看原来的列表
1) "hello"
2) "hello1"
127.0.0.1:6379> lrange myotherlist 0 -1 #查看目标列表中缺失存在该值
1) "hello2"

#存在
lset #将列表中指定下标的值替换为另外一个值 更新操作

127.0.0.1:6379> exists list #判断列表是否存在
(integer) 0
127.0.0.1:6379> lset list 0 item #如果不存在列表, 我们去更新就会报错
(error) ERR no such key
127.0.0.1:6379> lpush list "value1"
(integer) 1
127.0.0.1:6379> lrange list 0 0
1) "value1"
127.0.0.1:6379> lset list 0 item #如果存在, 更新当前下标的值
OK
127.0.0.1:6379> lrange list 0 0
1) "item"
127.0.0.1:6379> lset list 1 other #如果不存在对应下标的值, 就会报错
(error) ERR index out of range

#插入
linsert #将某一个具体的value插入到列表中某个元素的前面或者后面

127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "world"
(integer) 2
127.0.0.1:6379> linsert mylist  before "world" "other" #other 插入到 world 前面
(integer) 3
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "other"
3) "world"
127.0.0.1:6379> linsert mylist after "world" "new" #new 插入到 world后面
(integer) 4
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "other"
3) "world"
4) "new"

```



>小结

- 实际上是一个链表, before Node after, left, right 都可以插入值
- 如果key不存在, 创建新的链表
- 如果key存在, 新增内容
- 如果移除了所有值, 空链表, 也代表不存在
- 在两边插入或者改动值, 效率最高, 中间元素, 相对来说效率会低一点



消息队列 ( Lpush  Rpop)  , 栈 (Lpush Lpop)



## Set(集合)'

set中的值不能重复

```bash
#添加, 查看, 判断包含
sadd smembers sismember

127.0.0.1:6379> sadd myset hello #set集合中添加元素
(integer) 1
127.0.0.1:6379> sadd myset ccq
(integer) 1
127.0.0.1:6379> sadd myset love
(integer) 1
127.0.0.1:6379> smembers myset #查看指定set的所有值
1) "ccq"
2) "love"
3) "hello"
127.0.0.1:6379> sismember myset hello #判断某一个值是不是在set集合中
(integer) 1
127.0.0.1:6379> sismember myset world
(integer) 0

#获取set集合内容元素个数
scard 

127.0.0.1:6379> scard myset #获取set集合内容元素个数
(integer) 4

#移除set集合中的指定元素
srem

127.0.0.1:6379> srem myset hello #移除set集合中的指定元素
(integer) 1
127.0.0.1:6379> scard myset
(integer) 3
127.0.0.1:6379> smembers myset
1) "ccq"
2) "love2"
3) "love"


#无序不重复集合, 抽随机
srandmember 

127.0.0.1:6379> smembers myset
1) "ccq"
2) "love2"
3) "love"
127.0.0.1:6379> srandmember myset  #随机抽选一个元素
"love"
127.0.0.1:6379> srandmember myset
"ccq"
127.0.0.1:6379> srandmember myset
"love"
127.0.0.1:6379> srandmember myset 2 #随机抽选出指定个数的元素
1) "ccq"
2) "love"


#删除指定的key, 随机删除key
spop

127.0.0.1:6379> smembers myset
1) "ccq"
2) "love2"
3) "love"
127.0.0.1:6379> spop myset #随机删除set集合的元素
"love"
127.0.0.1:6379> spop myset
"ccq"
127.0.0.1:6379> smembers myset
1) "love2"


#将一个指定的值, 移动到另外一个set集合
smove 

127.0.0.1:6379> sadd myset hello
(integer) 1
127.0.0.1:6379> sadd myset world
(integer) 1
127.0.0.1:6379> sadd myset ccq
(integer) 1
127.0.0.1:6379> sadd myset2 set2
(integer) 1
127.0.0.1:6379> smove myset myset2 ccq #将一个指定的值 移动到另外一个set集合
(integer) 1
127.0.0.1:6379> smembers myset
1) "world"
2) "hello"
127.0.0.1:6379> smembers myset2
1) "ccq"
2) "set2"


#差集, 并集, 交集
sdiff sinter sunion
数字集合类:
	差集
	交集
	并集
	
127.0.0.1:6379> sadd key1 a
(integer) 1
127.0.0.1:6379> sadd key1 b
(integer) 1
127.0.0.1:6379> sadd key1 c
(integer) 1
127.0.0.1:6379> sadd key2 c
(integer) 1
127.0.0.1:6379> sadd key2 d
(integer) 1
127.0.0.1:6379> sadd key2 e
(integer) 1
127.0.0.1:6379> sdiff key1 key2  #差集 key1中与key2不同的
1) "b"
2) "a"
127.0.0.1:6379> sinter key1 key2 #交集 key1 key2相同的
1) "c"
127.0.0.1:6379> sunion key1 key2 #并集 key1 key2所有的
1) "a"
2) "c"
3) "d"
4) "b"
5) "e"

```

- 微博, A用户所有关注的人放入一个set集合, 粉丝放入一个集合中
- 共同关注, 共同爱好, 二度好友 推荐好友 (六度分割理论)



## Hash(哈希)

- Map集合  key-map集合 这时候这个值是一个map集合   key-<key, value>  本质和String类型没有太大区别, 还是一个简单的key-value
- set myhash field ccq



```bash

#设置, 获取
hset hget hmset hmget hgetall
127.0.0.1:6379> hset myhash field1 ccq  #set一个具体的key-value
(integer) 1
127.0.0.1:6379> hget myhash field1 #获取一个字段值
"ccq"
127.0.0.1:6379> hmset myhash field1 hello field2 world  #同时set多个key-value
OK
127.0.0.1:6379> hmget myhash field1 field2 #获取多个字段值
1) "hello"
2) "world"
127.0.0.1:6379> hgetall myhash #获取全部的数据
1) "field1"
2) "hello"
3) "field2"
4) "world"

#删除
hdel 

127.0.0.1:6379> hdel myhash field1 #删除hash指定的key字段, 对应的value也就消失了
(integer) 1
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "world"


#获取长度
hlen

127.0.0.1:6379> hmset myhash field1 hello field2 world
OK
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "world"
3) "field1"
4) "hello"
127.0.0.1:6379> hlen myhash #获取hash表的字段数量
(integer) 2

#判断存在
hexists

127.0.0.1:6379> hexists myhash field1  #判断hash中指定字段是否存在
(integer) 1
127.0.0.1:6379> hexists myhash field3
(integer) 0

#只获得所有的field
#只获得所有的value
hkeys hvals

127.0.0.1:6379> hkeys myhash #只获得所有的field
1) "field2"
2) "field1"
127.0.0.1:6379> hvals myhash #只获得所有的value
1) "world"
2) "hello"


#增加
hincrby #只有hincrby 没有 hdecrby
127.0.0.1:6379> hset myhash field3 5
(integer) 1
127.0.0.1:6379> hincrby myhash field3 1 #指定字段值增加指定增量
(integer) 6
127.0.0.1:6379> hincrby myhash field3 -1 #负数相当于减少
(integer) 5
127.0.0.1:6379> hdecrby myhash field3 1  #没有 hdecrby
(error) ERR unknown command `hdecrby`, with args beginning with: `myhash`, `field3`, `1`, 
127.0.0.1:6379> hsetnx myhash field4 hello #如果不存在, 可以设置为指定值
(integer) 1
127.0.0.1:6379> hsetnx myhash field4 world #如果存在, 不能设置
(integer) 0


```

hash变更的数据 user  name age 尤其是用户信息, 经常变得的信息, hash更适合对象存储, string适合字符串

```bash
127.0.0.1:6379> hset user:1 name ccq age 3
(integer) 2
127.0.0.1:6379> hgetall user:1
1) "name"
2) "ccq"
3) "age"
4) "3"
```



## Zset (有序集合)

在set的基础上,增加了一个值, set k1 v1  zset k1 score1 v1

```bash
#添加
zadd

127.0.0.1:6379> zadd myset 1 one #添加一个值
(integer) 1
127.0.0.1:6379> zadd myset 2 two 3 three #添加多个值
(integer) 2
127.0.0.1:6379> zrange myset 0 -1
1) "one"
2) "two"
3) "three"

#排序 
zrangbyscore  从小到大
zrevrangebyscore 从大到小

127.0.0.1:6379> zadd salary 2500 xiaohong #添加三个用户
(integer) 1
127.0.0.1:6379> zadd salary 5000 zhangsan
(integer) 1
127.0.0.1:6379> zadd salary 500 ccq
(integer) 1

127.0.0.1:6379> zrangebyscore salary -inf +inf  #显示全部用户, 从小到大排序
1) "ccq"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> zrevrange salary 0 -1  #显示全部用户, 从大到小排序
1) "zhangsan"
2) "ccq"

127.0.0.1:6379> zrangebyscore salary -inf +inf withscores #显示全部用户 并带上scores
1) "ccq"
2) "500"
3) "xiaohong"
4) "2500"
5) "zhangsan"
6) "5000" 
127.0.0.1:6379> zrevrangebyscore salary +inf -inf withscores #显示全部用户, 从大到小排序 并带上scores
1) "zhangsan"
2) "5000"
3) "xiaohong"
4) "2500"
5) "ccq"
6) "500"
127.0.0.1:6379> zrangebyscore salary -inf 2500 withscores #显示指定区间的用户 并带上scores
1) "ccq"
2) "500"
3) "xiaohong"
4) "2500"

#移除有序集合中的指定元素
zrem 移除指定的key的

127.0.0.1:6379> zrange salary 0 -1
1) "ccq"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> zrem salary xiaohong #移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "ccq"
2) "zhangsan"

#获取有序集合中的个数
zcard 

127.0.0.1:6379> zcard salary  #获取有序集合中的个数
(integer) 2

#统计
zcount

127.0.0.1:6379> zadd myset 1 hello
(integer) 1
127.0.0.1:6379> zadd myset 2 world
(integer) 1
127.0.0.1:6379> zadd myset 3 ccq
(integer) 1
127.0.0.1:6379> zcount myset 1 3 #获取指定区间的成员数量
(integer) 3
127.0.0.1:6379> zcount myset 1 2
(integer) 2

```

案例思路:

- set 排序, 存储班级成绩表, 工资表排序
- 普通消息 1 重要消息 2
- 排行榜应用实现 Top K问题



# 三种特殊数据类型

## geospatial地理位置

- 朋友的定位, 附近的人, 打车距离计算
- Redis的Geo在Redis3.2版本推出, 这个功能可以推算出地理位置的信息, 两地间的距离, 半径范围内人
- 可以查询一些测试数据http://www.jsons.cn/lngcode/
- 只有6个命令

### geoadd #添加地理位置

```bash
#geoadd
#规则: 两极无法直接添加, 我们一般会下载城市数据, 直接通过java程序一次性导入
#参数 key 值(经度 纬度 城市)
- Valid longitudes are from -180 to 180 degrees.
- Valid latitudes are from -85.05112878 to 85.05112878 degrees.
- The command will report an error when the user attempts to index coordinates outside the specified ranges.

127.0.0.1:6379> geoadd china:city 116.40 39.90 beijin
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.231 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 106.504 29.533 chongqin 114.05 22.52 shenzhen
(integer) 2
127.0.0.1:6379> geoadd china:city 120.16 30.24 hangzhou 108.96 34.26 xian
(integer) 2

```

### geopos #获取指定城市的经纬度

获得当前定位: 一定是一个坐标值

```bash
#geopos
#获取指定城市的经纬度

127.0.0.1:6379> geopos china:city beijin #获取指定城市的经纬度
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
127.0.0.1:6379> geopos china:city beijin chongqin #获取多个城市的经纬度
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
2) 1) "106.50399953126907349"
   2) "29.53300068885924645"

```

### geodist

两人之间的距离!

指定单位的参数 unit 必须是以下单位的其中一个：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

```bash
#获取两个坐标的直线距离 指定单位
geodist

127.0.0.1:6379> geodist china:city beijin shanghai km
"1067.2770"
127.0.0.1:6379> geodist china:city beijin chongqin km
"1463.5744"

```



### geodist 以给定的经纬度为中心, 找出某一半径内的元素

我附近的人?

- 获得所有附近的人的地址, 定位, 通过半径来查询

以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。

范围可以使用以下其中一个单位：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

所有的数据都应该录入 china:city 

```bash
#以110 30经纬度为中心寻找1000km半径内的城市
127.0.0.1:6379> georadius china:city 110 30 1000 km 
1) "chongqin"
2) "xian"
3) "shenzhen"
4) "hangzhou"
#以110 30经纬度为中心寻找500km半径内的城市
127.0.0.1:6379> georadius china:city 110 30 500 km
1) "chongqin"
2) "xian"
#显示到中心距离的位置
127.0.0.1:6379> georadius china:city 110 30 500 km withdist
1) 1) "chongqin"
   2) "341.4997"
2) 1) "xian"
   2) "483.8340"
#显示他人的定位信息
127.0.0.1:6379> georadius china:city 110 30 500 km withcoord
1) 1) "chongqin"
   2) 1) "106.50399953126907349"
      2) "29.53300068885924645"
2) 1) "xian"
   2) 1) "108.96000176668167114"
      2) "34.25999964418929977"
      
# 筛选出指定数量的结果
127.0.0.1:6379> georadius china:city 110 30 1000 km count 2
1) "chongqin"
2) "xian"
127.0.0.1:6379> georadius china:city 110 30 1000 km withdist withcoord count 2
1) 1) "chongqin"
   2) "341.4997"
   3) 1) "106.50399953126907349"
      2) "29.53300068885924645"
2) 1) "xian"
   2) "483.8340"
   3) 1) "108.96000176668167114"
      2) "34.25999964418929977"

```



### georadiusmember #找出位于指定元素周围的其他元素



```bash
#找出位于指定元素周围的其他元素
127.0.0.1:6379> georadiusbymember china:city beijin 1000 km
1) "beijin"
2) "xian"
127.0.0.1:6379> georadiusbymember china:city shanghai 400 km
1) "hangzhou"
2) "shanghai"

```



### geohash 返回11个字符的Geohash字符串

该命令将返回11个字符的Geohash字符串

```bash
#将经纬度转换成字符串, 如果两个字符串越接近则距离越近
127.0.0.1:6379> geohash china:city beijin chongqin
1) "wx4fbxxfke0"
2) "wm78p83f5n0"

```

GEO 底层的实现原理其实就是Zset

```bash
#查看地图中全部元素
127.0.0.1:6379> zrange china:city 0 -1
1) "chongqin"
2) "xian"
3) "shenzhen"
4) "hangzhou"
5) "shanghai"
6) "beijin"
#移除指定元素
127.0.0.1:6379> zrem china:city beijin
(integer) 1
127.0.0.1:6379> zrange china:city 0 -1
1) "chongqin"
2) "xian"
3) "shenzhen"
4) "hangzhou"
5) "shanghai"

```



## Hyperloglog

> 什么是基数?

A(1,3,5,7,8,7)

B(1,3,5,7,8)

基数(不重复的元素) = 5 可以接受误差

> 简介

Redis HyperLogLog 是用来做基数统计的算法，

HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。

因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

其底层使用string数据类型


**网页的访问量（UV）：一个用户多次访问，也只能算作一个人。**

传统实现，存储用户的id,然后每次进行比较。当用户变多之后这种方式及其浪费空间，而我们的目的只是计数，Hyperloglog就能帮助我们利用最小的空间完成。



> 测试使用

```bash
#创建第一组元素 mykey
127.0.0.1:6379> pfadd mykey a b c d e f g h i j
(integer) 1
#统计mykey元素的基数数量
127.0.0.1:6379> pfcount mykey
(integer) 10
#创建第二组元素 mykey2
127.0.0.1:6379> pfadd mykey2 i j z s ds sda ds  dsa dada d
(integer) 1
127.0.0.1:6379> pfcount mykey2
(integer) 9
#合并两组 mykey mykey2 => mykey3 并集
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2
OK
#查看并集的数量
127.0.0.1:6379> pfcount mykey3
(integer) 16

```

如果允许容错, 一定可以使用hyperloglog

如果不允许容错, 不能使用



## Bitmap

> 位存储

使用位存储，信息状态只有 0 和 1

Bitmap是一串连续的2进制数字（0或1），每一位所在的位置为偏移(offset)，在bitmap上可执行AND,OR,XOR,NOT以及其它位操作。

使用bitmap来记录周一到周日的打卡 [0,6]

```bash
#setbit
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 0
(integer) 0

查看某一天是否有打卡

​```bash
#getbit
127.0.0.1:6379> getbit sign 3
(integer) 1
127.0.0.1:6379> getbit sign 6
(integer) 0

```

统计操作, 统计打卡的天数

```bash
#bitcount
#统计这周的打卡记录
127.0.0.1:6379> bitcount sign
(integer) 3

```



# 事务

- MySQL: ACID

- Redis 事务本质, 一组命令的集合, 一个事务中的所有命令都会被序列化, 在执行的过程中, 会按照顺序执行
- 一次性, 顺序性, 排他性! 执行一系列的命令

```
------队列 set set set 执行------
```

- Redis单条命令式保证原子性的, redis事务不保证原子性
- Redis事务没有隔离级别的概念
  - 所有的命令在事务中, 并没有被指向, 只有发起指向命令的时候才执行 Exec
- Redis的事务
  - 开启事务 MULTI 
  - 命令入队
  - 执行事务 Exec
- 锁, Redis可以实现乐观锁



> 正常执行事务

```bash
#开启事务
127.0.0.1:6379> MULTI
OK
#命令入队
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> get k2
QUEUED
127.0.0.1:6379(TX)> set k3 v3
QUEUED
#执行事务
127.0.0.1:6379(TX)> EXEC
#事务执行的结果
1) OK
2) OK
3) "v2"
4) OK
```

> 放弃事务

```bash
#开启事务
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> set k4 v4
QUEUED
#取消事务
127.0.0.1:6379(TX)> DISCARD
OK
#事务队列中的命令都不会被执行
127.0.0.1:6379> get k4
(nil)

```



> 编译型异常 (代码有问题, 命令有错) 事务中所有的命令都不会被执行

```bash
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> set k3 v3
QUEUED
127.0.0.1:6379(TX)> getset k3  #错误的命令
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379(TX)> set k4 v4
QUEUED
127.0.0.1:6379(TX)> set k5 v5
QUEUED
127.0.0.1:6379(TX)> exec #执行事务报错
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k1 #所有的命令都不会被执行
(nil)
```



> 运行时异常(1/0) 如果事务队列中存在语法性错误, 那么执行命令的时候, 其他命令是可以正常执行的, 错误命令抛出异常

```bash
127.0.0.1:6379> set k1 "v1"
OK
127.0.0.1:6379> MULTI
OK
#会执行失败
127.0.0.1:6379(TX)> incr k1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> set k3 v3
QUEUED
127.0.0.1:6379(TX)> get k3
QUEUED
127.0.0.1:6379(TX)> exec
#虽然第一条命令报错, 但是其他的命令成功了
1) (error) ERR value is not an integer or out of range 
2) OK
3) OK
4) "v3"
127.0.0.1:6379> get k2 
"v2"
127.0.0.1:6379> get k3
"v3"

```



## 监控 Watch

### 悲观锁

- 认为什么时候都会出问题, 无论做什么都会加锁

### 乐观锁

- 认为什么时候都不会出问题, 所以不会上锁, 更新数据的时候, 判断在此期间是否有人修改过这个数据
- 获取version
- 更新的时候比较version

### Redis监测测试

#### 正常执行成功

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money #监视money对象
OK
127.0.0.1:6379> MULTI #事务正常结束, 数据期间没有发生变动, 这个时候正常执行成功
OK
127.0.0.1:6379(TX)> decrby money 20
QUEUED
127.0.0.1:6379(TX)> incrby out 20
QUEUED
127.0.0.1:6379(TX)> exec
1) (integer) 80
2) (integer) 20

```

#### 测试多线程修改值, 使用watch可以当做redis的乐观锁

```bash
127.0.0.1:6379> watch money #监视
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> DECRBY money 10
QUEUED
127.0.0.1:6379(TX)> incrby out 10
QUEUED
127.0.0.1:6379(TX)> exec #执行之前, 另外一个线程修改了值, 这个时候, 事务执行失败
(nil)

127.0.0.1:6379> unwatch #如果发现事务执行失败, 就先解锁
OK
127.0.0.1:6379> watch money #获取最新的值, 再次监视
OK
127.0.0.1:6379> MULTI 
OK
127.0.0.1:6379(TX)> decrby money 10
QUEUED
127.0.0.1:6379(TX)> incrby out 10
QUEUED
127.0.0.1:6379(TX)> exec #比对监视的值 是否发生了变化, 如果没有变化, 那么可以执行成功
1) (integer) 990
2) (integer) 30

```

如果修改失败, 获取最新的值就好



# Jedis

Redis官方推荐的java连接开发工具, 使用Java操作Redis中间件



1. 导入对应的依赖

   ```xml
   <!--导入jedis的包-->
       <dependencies>
           <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
           <dependency>
               <groupId>redis.clients</groupId>
               <artifactId>jedis</artifactId>
               <version>3.6.0</version>
           </dependency>
           <dependency>
               <groupId>com.alibaba</groupId>
               <artifactId>fastjson</artifactId>
               <version>1.2.62</version>
           </dependency>
       </dependencies>
   ```

2. 编码测试

   1. 连接数据库

   2. 操作命令

   3. 断开连接

   4. ```java
      public static void main(String[] args) {
              // 1. new Jedis对象即可
              Jedis jedis = new Jedis("127.0.0.1", 6379);
              //jedis所有的命令就是我们之前学习的所有指令
              System.out.println(jedis.ping());
          }
      ```

输出

![image-20210603210847079](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603210847079.png)



## 常用API

String

List

Set

Hash

Zset



# SpringBoot整合

## 整合测试

<img src="C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603212838640.png" alt="image-20210603212838640" style="zoom:150%;" />

源码分析

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {

	@Bean
	@ConditionalOnMissingBean(name = "redisTemplate")//我们可以自己定义一个redisTemplate来替换这个默认的
	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        //默认的RedisTemplate 没有过多的设置, redis 对象都需要序列化
        // 两个泛型都是Obejet Object, 需要强转成<string, Object>
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

	@Bean
	@ConditionalOnMissingBean //由于String是redis最常用的类型, 单独提出来一个bean
	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
		StringRedisTemplate template = new StringRedisTemplate();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

}

```

1. 导入依赖

   1. ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-redis</artifactId>
      </dependency>
      ```

2. 配置连接

   1. ```properties
      #springboot 所有的配置类, 都有一个自动配置类 RedisAutoConfiguration
      #自动配置类都会绑定一个properties配置文件 RedisProperties
      
      #配置redis
      spring.redis.host=127.0.0.1
      spring.redis.port=6379
      ```

      

3. 测试

   1. ```java
      @SpringBootTest
      class Redis02SpringbootApplicationTests {
      
          @Autowired
          private RedisTemplate redisTemplate;
      
          @Test
          void contextLoads() {
      
              //redisTemplate
              /*
              opsForValue 操作字符串
              opsForList 操作List
              opsForSet
              opsForHash
              opsForGeo
              opsForZset
              opsForHyperloglog
      
              除了基本的操作, 我们常用的方法都可以直接通过后RedisTemplate操作, 比如事务和基本的增删改查
      
               */
              //获取redis连接对象
              /*
              RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
              connection.flushAll();
              connection.flushDb();
              */
              redisTemplate.opsForValue().set("mykey", "ccq");
              System.out.println(redisTemplate.opsForValue().get("mykey"));
          }
      
      }
      ```

4. ![image-20210603220229436](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603220229436.png)



编写自己的redistemplate

```java
@Configuration
public class RedisConfig {


    @Bean
    @ConditionalOnMissingBean(name = "redisTemplate")
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        //配置不同的序列化

        //Json序列化配置
        Jackson2JsonRedisSerializer<Object> objectJackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        objectJackson2JsonRedisSerializer.setObjectMapper(om);
        //String的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        //key采用String
        template.setKeySerializer(stringRedisSerializer);
        //hash的key采用String序列化
        template.setHashKeySerializer(stringRedisSerializer);
        //hash的value采用jackson序列化
        template.setValueSerializer(objectJackson2JsonRedisSerializer);

        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```



# Redis.conf详解

启动的时候, 通过配置文件来启东

1. 单位大小写不敏感

2. 包含其他的配置文件

3. 网络

   1. ```bash
      bind 127.0.0.1 -::1 #绑定ip
      protected-mode yes #保护模式
      port 6379			#端口设置
      ```

4. 通用

   1. ```bash
      daemonize yes #以守护进程方式运行, 默认是no, 我们需要自己开启为yes
      pidfile /var/run/redis_6379.pid #如果我们以后台的方式的运行, 我们需要指定一个pid文件
      
      #日志
      # Specify the server verbosity level.
      # This can be one of:
      # debug (a lot of information, useful for development/testing)
      # verbose (many rarely useful info, but not a mess like the debug level)
      # notice (moderately verbose, what you want in production probably)
      # warning (only very important / critical messages are logged)
      
      loglevel notice
      logfile "" #日志文件的位置名
      databases 16 #数据库的数量, 默认16个
      always-show-logo no #是否总是显示logo
      
      
      ```

5. 快照

   1. 持久化, 在规定时间内, 执行了多少次操作,, 则会持久化到文件 .rdb.aof

   2. redis是内存数据库, 如果没有持久化, 数据断电即失

      ```bash
      #如果900秒内, 至少有1个key 进行了修改, 就持久化操作
      # save 3600 1
      #如果300秒内, 至少有100个key 进行了修改, 就持久化操作
      # save 300 100
      # save 60 10000
      
      stop-writes-on-bgsave-error yes #持久化出错是否需要继续工作
      rdbcompression yes #是否压缩rdb文件, 会消耗一些cpu资源
      rdbchecksum yes #保存rdb文件的时候, 进行错误的检查
      dir ./ #rdb文件保存的目录
      
      ```

6. REPLICATION 复制

7.  SECURITY 

   1. 可以在这里设置redis面, 默认没有密码

   2. ```bash
      config get requirepass #获取redis的密码
      config set requirepass "123" #设置redis密码
      auth "123" #使用密码
      ```

8. CLIENTS 

   1. ```bash
      #最大客户端数量
      maxclients 10000
      
      ```

9. MEMORY MANAGEMENT 

   1. ```bash
      #最大内存设置
      maxmemory <bytes>
      #内存到达上限 处理策略
      maxmemory-policy noeviction
      # volatile-lru -> Evict using approximated LRU, only keys with an expire set.
      # allkeys-lru -> Evict any key using approximated LRU.
      # volatile-lfu -> Evict using approximated LFU, only keys with an expire set.
      # allkeys-lfu -> Evict any key using approximated LFU.
      # volatile-random -> Remove a random key having an expire set.
      # allkeys-random -> Remove a random key, any key.
      # volatile-ttl -> Remove the key with the nearest expire time (minor TTL)
      # noeviction -> Don't evict anything, just return an error on write operations.
      
      ```

10. APPEND ONLY MODE (AOF)

    1. ```bash
       appendonly no #默认不开启aof, 默认使用rdb, 大部分情况rdb完全够用
       appendfilename "appendonly.aof" #持久化文件的名字
       
       # appendfsync always 	#每次修改都sync 消耗性能
       appendfsync everysec  # 每秒都执行一次sync, 可能会丢失这1s的数据
       # appendfsync no		#不执行 sync 操作系统自己同步数据, 速度最快
       ```

       



# Redis持久化

## RDB(Redis DataBase)

主从复制, rdb就是备用的, 从机上面 不占主机内存

![image-20210603231115369](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603231115369.png)

![image-20210603231529432](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603231529432.png)

![](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603231448334.png)



### 触发机制

- save的规则满足的情况下，会自动触发rdb规则！
- 执行flushall命令，也会触发我们的rdb规则！
- 退出redis，也会产生rdb文件！

备份就自动生成一个dump.rdb

### 如何恢复rdb文件

- 只需要将rdb文件放在我们redis启动目录就可以了, redis启动的时候就会自动检查dump.rdb恢复其中的数据

- 查看需要存放的位置

  - ```bash
    127.0.0.1:6379> config get dir
    1) "dir"
    2) "/usr/local/bin" #如果在这个目录下存在dump.rdb文件, 启动就会恢复其中的数据
    
    ```

### 优缺点

![image-20210603232904748](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603232904748.png)



## AOF(Append Only File)

- 将我们所有的命令都记录下来, history, 恢复的时候, 将这个文件重新执行一遍

![image-20210603233204844](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603233204844.png)

以日志的形式来记录每个写的操作，将Redis执行过的所有指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。


### append

![image-20210603233321749](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603233321749.png)

- 默认不开启, 需要手动配置, 我们只需要将appendonly该位yes开启aof
- 重启, redis就可以生效了
- 如果这个aof文件有错误, redis无法启动, 我们需要修复这个配置aof文件, 
- redis给我们提供了一个工具, `redis-check-aof --fix`

如果文件正常, 重启就可以直接恢复

### 重写规则

aof默认就是文件的无限追加, 文件就会越来越大

如果aof文件大于64m, 太大了, fork一个新的进程来将我们的文件进行重写

### 优缺点

```bash
appendonly yes  # 默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分的情况下，rdb完全够用
appendfilename "appendonly.aof"

# appendfsync always # 每次修改都会sync 消耗性能
appendfsync everysec # 每秒执行一次 sync 可能会丢失这一秒的数据
# appendfsync no # 不执行 sync ,这时候操作系统自己同步数据，速度最快
```



**优点**

1. 每一次修改都会同步，文件的完整性会更加好
2. 没秒同步一次，可能会丢失一秒的数据
3. 从不同步，效率最高

**缺点**

1. 相对于数据文件来说，aof远远大于rdb，修复速度比rdb慢！
2. Aof运行效率也要比rdb慢，所以我们redis默认的配置就是rdb持久化



## 扩展

![image-20210603235012969](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603235012969.png)

![image-20210603234958613](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603234958613.png)

# Redis发布订阅



![image-20210603235918491](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210603235918491.png)



![image-20210604000029353](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604000029353.png)

![image-20210604000048697](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604000048697.png)



## 测试

- 订阅端

  - ```bash
    127.0.0.1:6379> SUBSCRIBE ccq  # 订阅一个频道 ccq
    Reading messages... (press Ctrl-C to quit) #等待推送的信息
    1) "subscribe"
    2) "ccq"
    3) (integer) 1
    1) "message" #消息
    2) "ccq" #频道
    3) "This is the first message from channel ccq" #消息
    1) "message"
    2) "ccq"
    3) "This is the second message from channel ccq"
    ```

- 发布端

  - ```bash
    #发布者发布消息到频道
    127.0.0.1:6379> PUBLISH ccq "This is the first message from channel ccq"
    (integer) 1
    #发布者发布消息到频道
    127.0.0.1:6379> PUBLISH ccq "This is the second message from channel ccq"
    (integer) 1
    
    ```

## 原理

每个 Redis 服务器进程都维持着一个表示服务器状态的 redis.h/redisServer 结构， 结构的 pubsub_channels 属性是一个字典， 这个字典就用于保存订阅频道的信息，其中，字典的键为正在被订阅的频道， 而字典的值则是一个链表， 链表中保存了所有订阅这个频道的客户端。

通过publish命令向订阅者发送消息, redis-server会使用给定的频道作为键, 在他维护的channel字典中查找记录了订阅这个频道的所有客户端的链表, 遍历这个链表, 将消息发布给所有的订阅者

![image-20210604000830126](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604000830126.png)

客户端订阅，就被链接到对应频道的链表的尾部，退订则就是将客户端节点从链表中移除。

### 缺点

- 如果一个客户端订阅了频道，但自己读取消息的速度却不够快的话，那么不断积压的消息会使redis输出缓冲区的体积变得越来越大，这可能使得redis本身的速度变慢，甚至直接崩溃。
- 这和数据传输可靠性有关，如果在订阅方断线，那么他将会丢失所有在短线期间发布者发布的消息。

### 应用

- 消息订阅：公众号订阅，微博关注等等（起始更多是使用消息队列来进行实现）
  - 多人在线聊天室。
  - 稍微复杂的场景，我们就会使用消息中间件MQ处理。



# Redis主从复制

## 概念

-  主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节（Master/Leader）,后者称为从节点（Slave/Follower）， **数据的复制是单向的！只能由主节点复制到从节点**（主节点以写为主、从节点以读为主）。
- 默认情况下，每台Redis服务器都是主节点，一个主节点可以有0个或者多个从节点，但每个从节点只能由一个主节点。


### 作用

- 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余的方式。
- 故障恢复：当主节点故障时，从节点可以暂时替代主节点提供服务，是一种服务冗余的方式
- 负载均衡：在主从复制的基础上，配合读写分离，由主节点进行写操作，从节点进行读操作，分担服务器的负载；尤其是在多读少写的场景下，通过多个从节点分担负载，提高并发量。
- 高可用基石：主从复制还是哨兵和集群能够实施的基础。

### 为什么使用集群

- 单台服务器难以负载大量的请求
- 单台服务器故障率高，系统崩坏概率大
- 单台服务器内存容量有限。



## 环境配置

只配置从库, 不用配置主库

```bash
127.0.0.1:6379> info replication #查看当前库的信息
# Replication
role:master #角色 master
connected_slaves:0 #没有从机
master_failover_state:no-failover
master_replid:9e8e7b1ed81a2d563c8bacf46f320cc3b0512cb4
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

```



1. 复制三份redis.conf 重命名与端口号对应
   1. redis79.conf
   2. redis80.conf
   3. redis81.conf
2. 依次修改配置文件
   1. port : 6379 6380 6381
   2. pidfile /var/run/redis_63(79/80/81).pid
   3. logfile " 6379 6380 6381 .log"
   4. dbfilename dump6379/80/81.rdb
3. 修改完毕通过不同的配置文件开启三个服务

![image-20210604004607970](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604004607970.png)



## 一主二从

- ==默认情况下，每台Redis服务器都是主节点；==我们一般情况下只用配置从机就好了！
- 认老大！一主（79）二从（80，81）
- 使用`SLAVEOF host port`就可以为从机配置主机了。

```bash
127.0.0.1:6380> SLAVEOF 127.0.0.1 6379  #SLAVEOF 127.0.0.1 6379  找ip为127.0.0.1的6379端口为主机
OK 
127.0.0.1:6380> info replication
# Replication
role:slave #当前角色为从机
master_host:127.0.0.1 #可以看到主机的信息
master_port:6379
master_link_status:down
master_last_io_seconds_ago:-1
master_sync_in_progress:1
slave_repl_offset:1
master_sync_total_bytes:-1
master_sync_read_bytes:0
master_sync_left_bytes:-1
master_sync_perc:-0.00
master_sync_last_io_seconds_ago:0
master_link_down_since_seconds:-1
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:053ab87f3aa861dfb8acc3535ac14dcdb3473b69
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

#在主机中查看

127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:1 #多了从机的配置
slave0:ip=127.0.0.1,port=6380,state=wait_bgsave,offset=0,lag=0
master_failover_state:no-failover
master_replid:054fb09aced0bf04add5db34480921b0843b82f9
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:0


```

如果两个都配置完了, 有两个配置

![image-20210604005550384](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604005550384.png)

我们这里是使用命令搭建，是暂时的，==真实开发中应该在从机的配置文件中进行配置，==这样的话是永久的。

![image-20210604005809113](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604005809113.png)



### 细节

- 主机可以写, 从机不能写只能读, 主机中所有的信息和配置都会被从机保存

![image-20210604010142964](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604010142964.png)



- 当主机断电宕机后，默认情况下从机的角色不会发生变化 ，集群中只是失去了写操作，当主机恢复以后，又会连接上从机恢复原状。


- 当从机断电宕机后，若不是使用配置文件配置的从机，再次启动后作为主机是无法获取之前主机的数据的，若此时重新配置称为从机，又可以获取到主机的所有数据。这里就要提到一个同步原理。


- 第二条中提到，默认情况下，主机故障后，不会出现新的主机，有两种方式可以产生新的主机：
  - 从机手动执行命令slaveof no one,这样执行以后从机会独立出来成为一个主机
  - 使用哨兵模式（自动选举）

如果没有老大了，这个时候能不能选择出来一个老大呢？手动！

如果主机断开了连接，我们可以使用SLAVEOF no one让自己变成主机！其他的节点就可以手动连接到最新的主节点（手动）！如果这个时候老大修复了，那么久重新连接！


### 复制原理

![image-20210604011103898](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604011103898.png)

### 层层链路

上一个M连接下一个S

![image-20210604011926061](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604011926061.png)

这个时候也可以完成主从复制



### 主节点宕机后, 需要手动选择下一个主机

- 如果主机断开了连接, 我们可以使用 `SLAVEOF no one` 让自己变成主机, 其他节点可以手动连接到最新的这个主节点上(手动)
- 

## 哨兵模式(自动选举主机)

### 概述

![image-20210604012556677](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604012556677.png)

![image-20210604012712352](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604012712352.png)

**哨兵的作用：**

- 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。
- 当哨兵监测到master宕机，会自动将slave切换成master，然后通过**发布订阅模式**通知其他的从服务器，修改配置文件，让它们切换主机。



然而一个哨兵堆Redis服务器进行监控, 可能会出现问题, 为此, 我们可以使用多个哨兵进行监控, 各个哨兵之间还会进行监控, 这样形成了多哨兵模式

![image-20210604012831768](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604012831768.png)

![image-20210604013014700](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604013014700.png)

### 测试

目前的状态 一主二从



1. 配置哨兵配置文件 sentinel.conf

   ```bash
   #sentinel monitor 被监控的名称, host port 1
   sentinel monitor myredis 127.0.0.1 6379 1
   #1代表主机挂了, slave投票看让谁接替成为主机, 票数最多的就会成为主机
   ```

2. 启动

   1.  redis-sentinel c-config/sentinel.conf 

   2. ```bash
      root@ccq-virtual-machine:/usr/local/bin# redis-sentinel c-config/sentinel.conf 
      61052:X 04 Jun 2021 01:35:42.473 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
      61052:X 04 Jun 2021 01:35:42.473 # Redis version=6.2.4, bits=64, commit=00000000, modified=0, pid=61052, just started
      61052:X 04 Jun 2021 01:35:42.473 # Configuration loaded
      61052:X 04 Jun 2021 01:35:42.474 * Increased maximum number of open files to 10032 (it was originally set to 1024).
      61052:X 04 Jun 2021 01:35:42.474 * monotonic clock: POSIX clock_gettime
                      _._                                                  
                 _.-``__ ''-._                                             
            _.-``    `.  `_.  ''-._           Redis 6.2.4 (00000000/0) 64 bit
        .-`` .-```.  ```\/    _.,_ ''-._                                  
       (    '      ,       .-`  | `,    )     Running in sentinel mode
       |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
       |    `-._   `._    /     _.-'    |     PID: 61052
        `-._    `-._  `-./  _.-'    _.-'                                   
       |`-._`-._    `-.__.-'    _.-'_.-'|                                  
       |    `-._`-._        _.-'_.-'    |           https://redis.io       
        `-._    `-._`-.__.-'_.-'    _.-'                                   
       |`-._`-._    `-.__.-'    _.-'_.-'|                                  
       |    `-._`-._        _.-'_.-'    |                                  
        `-._    `-._`-.__.-'_.-'    _.-'                                   
            `-._    `-.__.-'    _.-'                                       
                `-._        _.-'                                           
                    `-.__.-'                                               
      
      61052:X 04 Jun 2021 01:35:42.480 # Sentinel ID is e0f693554fb567178f854a15ac6cee8af4b93e5e
      61052:X 04 Jun 2021 01:35:42.480 # +monitor master myredis 127.0.0.1 6379 quorum 1
      61052:X 04 Jun 2021 01:35:42.485 * +slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
      61052:X 04 Jun 2021 01:35:42.487 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
      
      ```

3. ![image-20210604014827983](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604014827983.png)

4. 如果主机此时回来了, 只能变成新的主机的从机, 这就是哨兵模式的规则

### 优缺点

- **优点：**
  - 哨兵集群，基于主从复制模式，所有主从复制的优点，它都有
  - 主从可以切换，故障可以转移，系统的可用性更好
  - 哨兵模式是主从模式的升级，手动到自动，更加健壮

- 缺点：

- Redis不好在线扩容，集群容量一旦达到上限，在线扩容就十分麻烦
- 实现哨兵模式的配置其实是很麻烦的，里面有很多配置项
  

### 哨兵模式的全部配置

```bash
# Example sentinel.conf
 
# 哨兵sentinel实例运行的端口 默认26379
port 26379
 
# 哨兵sentinel的工作目录
dir /tmp
 
# 哨兵sentinel监控的redis主节点的 ip port 
# master-name  可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 1
 
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
 
 
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
 
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
这个数字越小，完成failover所需的时间就越长，
但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1
 
 
 
# 故障转移的超时时间 failover-timeout 可以用在以下这些方面： 
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。  
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
 
# SCRIPTS EXECUTION
 
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
 
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，
#这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，
#一个是事件的类型，
#一个是事件的描述。
#如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
  sentinel notification-script mymaster /var/redis/notify.sh
 
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。 
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh

```



# Redis缓存穿透和雪崩

![image-20210604015632180](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604015632180.png)

## 缓存穿透(查不到)

### 概念

在默认情况下，用户请求数据时，会先在缓存(Redis)中查找，若没找到即缓存未命中，再在数据库中进行查找，数量少可能问题不大，可是一旦大量的请求数据（例如秒杀场景）缓存都没有命中的话，就会全部转移到数据库上，造成数据库极大的压力，就有可能导致数据库崩溃。网络安全中也有人恶意使用这种手段进行攻击被称为洪水攻击。

### 解决方案

#### **布隆过滤器**

对所有可能查询的参数以Hash的形式存储，以便快速确定是否存在这个值，在控制层先进行拦截校验，校验不通过直接打回，减轻了存储系统的压力。



![image-20210604020048232](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604020048232.png)



#### 缓存空对象

一次请求若在缓存和数据库中都没找到，就在缓存中方一个空对象用于处理后续这个请求。

![image-20210604020201339](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604020201339.png)



 这样做有一个缺陷：存储空对象也需要空间，大量的空对象会耗费一定的空间，存储效率并不高。解决这个缺陷的方式就是设置较短过期时间

即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响

## 缓存击穿（一个点查询量太大，缓存过期）

### 概念

 相较于缓存穿透，缓存击穿的目的性更强，一个存在的key，在缓存过期的一刻，同时有大量的请求，这些请求都会击穿到DB，造成瞬时DB请求量大、压力骤增。这就是缓存被击穿，只是针对其中某个key的缓存不可用而导致击穿，但是其他的key依然可以使用缓存响应。

 比如热搜排行上，一个热点新闻被同时大量访问就可能导致缓存击穿。


### 解决方案

#### 设置热点数据永不过期

这样就不会出现热点数据过期的情况，但是当Redis内存空间满的时候也会清理部分数据，而且此种方案会占用空间，一旦热点数据多了起来，就会占用部分空间。

#### 加互斥锁(分布式锁)

在访问key之前，采用SETNX（set if not exists）来设置另一个短期key来锁住当前key的访问，访问结束再删除该短期key。保证同时刻只有一个线程访问。这样对锁的要求就十分高。

## 缓存雪崩

### 概念

某一个时间段, 缓存集中过期失效, Redis宕机

大量的key设置了相同的过期时间，导致在缓存在同一时刻全部失效，造成瞬时DB请求量大、压力骤增，引起雪崩。

![image-20210604020857117](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210604020857117.png)

### 解决方案

#### redis高可用

这个思想的含义是，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续工作，其实就是搭建的集群

#### 限流降级

这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。

#### 数据预热

数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。
