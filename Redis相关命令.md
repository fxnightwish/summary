# Redis相关命令

## 底层原理

> 单线程+多路IO复用

## 五大数据类型

> 1. Redis字符串（String）
> 2. Redis列表（List）
> 3. Redis集合（Set）
> 4. Redis哈希（Hash）
> 5. Redis有序集合（Zset）

## Redis键

```shell
#清空全部库
flushall
#清空当前库
flushdb
#选择某个库，Redis有16个库，从0号库开始
select 0
#查看当前库所有的key
keys *
#查看某个key是否存在
exists key
#查看某个key的数据类型
type key
#删除指定的key
del key
#删除指定的key，非阻塞删除
unlink key
#指定key的过期时间，单位秒。重新expire，会重新设置失效时间
expire key
#查看某个key，还有多少秒失效。-1永不失效，-2已过期
ttl key
```

## Redis字符串

> String类型是二进制安全的，意味着Redis的String可以包含任何数据，比如jpg图片或者序列化的对象。
>
> String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M
>
> Redis的存储的key增加减少，执行的是原子操作。
>
> 所谓原子操作是指不会被线程调度机制打断的操作。
>
> 这种操作一旦开始，就一直运行到结束，中间不会有任何的线程切换。
>
> 在单线程中，能够在单条指令中完成的操作都可以认为是原子操作，因为中断只能发生在指令之间
>
> 在多线程中，不能被其他进程（线程）打断的操作就叫原子操作。
>
> Redis单命令的原子性主要得益于Redis的单线程。

```shell
#添加键值对
set <key> <value> 
#将给定的<value>追加到原值的末尾
append <key> <value> 
#获取值的长度
strlen <key>
#只有在key不存在时，设置key的值
setnx <key> <value>
#将key中存储的数字值增1，只能对数字值操作
incr <key>
#将key中存储的数字值减1
decr <key>
#将key中存储的数字值增减，自定义步长
incrby/decrby <key> <步长>
```

```shell
#同时设置一个或多个key-value对
mset <key1> <value1> <k2> <value2>...
#同时获取一个或多个key的值
mget <key1> <key2>...
#同时设置一个或多个key-value对，当且仅当给定key都不存在，其具有原子性，有一个失败，其他的都失败
msetnx <key1> <value1> <key2> <value2>...
#获得值的范围，类型Java中的substring,前包，后包
getrange <key> <起始位置> <结束位置>
#用<value>覆写<key>所存储的字符串值，从<起始位置>开始，所有从零开始
setrange <key> <起始位置> <value>
#设置键值的同时，设置过期时间
setex <key> <过期时间> <value>
#以新换旧，设置了新值的同时获得旧值
getset <key> <value>
```

> String的数据结构为简单动态字符串，是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用预分配勇于空间的方式，来减少内存的频繁分配。

## Redis列表

> 单键多值
>
> Redis列表是简单的字符串列表，按照插入顺序排序，你可以添加一个元素到列表的左边或者右边
>
> 它的底层实际是个双向链表，对两端的操作性能很高，通过索引下表的操作中间的节点性能比较差

```shell
#从左边、右边插入一个或多个值
lpush/rpush <key> <value1> <value2> <value3>...
#从左边、右边吐出一个值，值在键在，值光键亡
lpop/rpop <key> 
#从<key1>列表右边吐出一个值，插入到<key2>列表的左边
rpoplpush <key1> <key2>
#按照索引下标获得元素
lrange <key> <start> <stop>
#按照索引下标获得元素
lindex <key> <index>
#获得列表长度
llen <key> 
#在<value>的后面插入<newvalue>值
linsert <key> before/after <value> <newvalue>
#从左边删除n个value
lrem <key> <n> <value>
#将列表key下标为index的值替换成value
lset <key> <index> <value>
```

> list的数据结构为快速链表quickList
>
> 首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，也即使压缩列表
>
> 它将所有的元素紧挨着一起存储，分配的是一块连续的内存
>
> 当数据量比较多的时候才会改成quicklist
>
> 因为普通的链表需要的附加指针空间太大，会比较浪费时间，比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针prev和next
>
> Redis将链表和ziplist结合起来组成了quicklist，也就是将多个ziplist使用双向指针串起来，这样既满足了快速的插入删除性能，又不会出现太大的空间浪费。

