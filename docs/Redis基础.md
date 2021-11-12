### Redis是什么

是一个开源的使用**C**语言编写，支持网络，可基于内存亦可持久化的日志key-value非关系型数据库。并支持多种语言的API。

### Redis特性

- Redis远程的：分为客户端，服务端。可以分别部署在不同的机器上，通过自定义协议进行传输和交互的。平时说的Redis通常指的是Redis的服务端。

- Redis基于内存的：所有数据结构存在内存中。所有操作非常高速。性能优越于硬盘存储的MySQL，因为存在内存中，所有也是比较吃内存的。

- Redis非关系型数据库：本质是数据库，存储数据，区别于MySQL。关系型数据库在存储之前，必须要定义好所谓的数据字典，后续的存储数据按照存储字典来存储，而Redis就不需要了。

### Redis应用场景

- 缓存：当系统的接口数据比较慢的时候，可以把系统数据接口的数据缓存起来，当下次取的时候，可以直接从缓存中取就可以了。

- 数据存储：redis有两种非常完备的持久化机制【**RDB和AOF**】，可以定期将数据持久化硬盘中，保障数据的完整性，安全性。

### 为什么使用Redis

> 1. 性能：如下图所示，我们在碰到需要执行耗时特别久，且结果不频繁变动的SQL，就特别适合将运行结果放入缓存。这样，后面的请求就去缓存中读取，使得请求能够迅速响应。

![image-20200418163331873](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200418163331873.png)

> 2. 并发：如下图所示，在大并发的情况下，所有的请求直接访问数据库，数据库会出现连接异常。这个时候，就需要使用redis做一个缓冲操作，让请求先访问到redis，而不是直接访问数据库。

![image-20200418163537472](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200418163537472.png)

### Redis数据结构及常用命令

> 非数据类型常用命令

```bash
# 切换库
select 0
# 启动服务器，用客户端访问
./redis-server redis.conf 
./redis-cli
# 测试验证连接是否正常
ping
# 查看当前库的所有键
keys *
# 判断key是否存在
exists <key>
# 查看键的类型
type <key>   
# 删除某个键
del <key>
# 设置key的过期时间单位为 秒
expire <key> <seconds>
# 查看还有多少秒过期，-1表示永不过期，-2表示已过期
ttl <key>
# 查看当前数据库的key的数量
dbsize
# 清空当前库
flushdb
# 通杀全部库
flushall
```

> String字符串（存储一个变量，例如：微博的粉丝数 ）

```bash
# 添加键值对
set <key> <value>
# 查询对应键值
get <key>
# 将给定的<value>追加到原值的末尾
append <key> <value>
# 获得值的长度
strlen <key> 
# 只有在key不存在时设置key的值
setnx <key> <value>
# 将key中储存的数字值增1,只能对数字值操作，如果为空，新增值为1
incr <key>
# 将key中储存的数字值减1,只能对数字值操作，如果为空，新增值为-1
decr <key>
# 将 key 中储存的数字值增减。自定义步长。
incrby/decrby <key> <步长>
# 同时设置一个或多个key-value对  
mset <key1> <value1> <key2> <value2> ..... 
# 同时获取一个或多个 value  
mget <key1> <key2> <key3> ..... 
# 同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。
msetnx <key1> <value1> <key2> <value2> ..... 
# 获得值的范围
getrange <key> <起始位置> <结束位置>
# 用<value>覆写<key> 所储存的字符串值，从<起始位置>开始。
setrange <key> <起始位置> <value>
# 设置键值的同时，设置过期时间，单位秒。
setex <key> <过期时间> <value>
# 以新换旧，设置了新值同时获得旧值
getset <key> <value>

```

> List列表（列表秒杀 ）

```bash
# 从左边/右边插入一个或多个值。 
lpush/rpush <key> <value1> <value2> <value3> ....
# 从左边/右边吐出一个值。值在键在，值光键亡
lpop/rpop <key> 
# 按照索引下标获得元素(从左到右) 元素不会丢失！
lrange <key> <start> <stop>
# 获得列表长度 
llen <key>
# 在<value>的后面插入<newvalue> 插入值
linsert <key> before <value> <pivot> <newvalue>

```

> Set集合（求微博共同的好友 ）

```bash
# 将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略。
sadd <key> <value1> <value2> .....   
# 取出该集合的所有值。
smembers <key>
# 判断集合<key>是否为含有该<value>值，有返回1，没有返回0
sismember <key> <value>
# 删除集合中的某个元素。
srem <key> <value1> <value2> ....
# 随机从该集合中吐出一个值。
spop <key>  
# 随机从该集合中取出n个值，不会从集合中删除。
srandmember <key> <n>
# 返回两个集合的交集元素。
sinter <key1> <key2>  
# 返回两个集合的并集元素。
sunion <key1> <key2>  
# 返回两个集合的差集元素，返回的结果跟key的顺序有关系
sdiff <key1> <key2>  

```

> Zset有序集合（排行榜 ）

```bash
# 将一个或多个 member 元素及其 score 值加入到有序集 key 当中。
zadd  <key> <score1> <value1> <score2> <value2> ...
# 返回有序集 key 中，下标在<start> <stop>之间的元素带WITHSCORES，可以让分数一起和值返回到结果集。
zrange <key> <start> <stop> [WITHSCORES]  
WITHSCORES 如果在命令行上加上该选项，则将score 和 value 一同取出，如果不加该选项，则只取value值！
# 返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从小到大)次序排列。 
zrangebyscore key min max [withscores] [limit offset count]
zrevrangebyscore key max min [withscores] [limit offset count] 从大到小
# 为元素的score加上增量
zincrby <key> <increment> <member>
# 删除该集合下，指定值的元素 
zrem <key> <value>  
# 统计该集合，分数区间内的元素个数
zcount <key> <min> <max>
# 返回该值在集合中的排名，从0开始。
zrank <key> <value>

```

> Hash对象（存储对象 ）

```bash
# 给<key>集合中的  <field>键赋值<value>
hset <key> <field> <value>
# 从<key1>集合<field> 取出 value
hget <key1> <field>
# 批量设置hash的值
hmset <key1> <field1> <value1> <field2> <value2>...   
# 批量取出hash的值
hmget <key1> <field1> <field2>...   
# 查看哈希表 key 中，给定域 field 是否存在。 有返回1，没有返回0
hexists key <field>
# 列出该hash集合的所有field
hkeys <key>
# 列出该hash集合的所有value
hvals <key>
```

### Redis持久化

- RDB
- AOF

### 常见问题

缓存穿透：指查询一个在数据库中根本不存在的数据。可以将对应的 key 的 value 存为 null；

缓存击穿：当热款商品，热 key 被大量用户访问的时候，出现高并发的情况。此时 key 在缓存中失效了！ 直接去数据库查询！可能会导致数据库崩塌！缓存击穿问题可以使用分布式锁来解决，简单来说就是缓存失效的时候，不是直接去查数据库，而是先使用缓存某些带成功返回值的操作。比如 Redis 的 setNx 操作，当操作返回成功的时候，再进行查询数据库，然后把数据再放入缓存中，操作返回失败时，就重试整个查询的方法；

缓存雪崩：很多key同时失效，给数据库带来的冲击。在往 Redis 存数据的时候，每个 key 的失效时间加个随机值就可以，这样保证数据不会同一时间大面积失效；

