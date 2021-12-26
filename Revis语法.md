# Redis语法

## 一、基本语法和操作说明

### 1、cmd启动项

```cmd
redis-server.exe  redis.windows.conf
```

### 2、基本知识

Redis数据库总共有**16**个，初始设置使用第**0**个。

```bash
测试连接成功
127.0.0.1:6379> ping
PONG
```

### 3、普通语法

```bash
正常切换数据库
127.0.0.1:6379> select 2
OK
------------------------------------------------
set get 数据库键值对
127.0.0.1:6379[2]> set name danny
OK
127.0.0.1:6379[2]> set hello 17
OK
127.0.0.1:6379[2]> get name
"danny"
127.0.0.1:6379[2]> get hello
"17"
------------------------------------------------
获取当前数据库所有keys 若无数据则为empty
127.0.0.1:6379[2]> keys *
1) "name"
2) "hello"
127.0.0.1:6379[1]> keys *
(empty list or set)
------------------------------------------------
获取当前数据库内存(dbsize)
127.0.0.1:6379[2]> dbsize
(integer) 2
------------------------------------------------
删除当前数据库所有内存(flushdb)
127.0.0.1:6379> keys *
1) "test_key"
2) "MDM001302"
3) "LoginInfo"
4) "LoginInfoToken"
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> dbsize
(integer) 0
------------------------------------------------
删除所有数据库所有内存(flushall)
127.0.0.1:6379> select 2
OK
127.0.0.1:6379[2]> keys *
1) "name"
2) "hello"
127.0.0.1:6379[2]> select 0
OK
127.0.0.1:6379> flushall
OK
------------------------------------------------
判断当前数据库是否存在key值(exists)
127.0.0.1:6379[3]> set name1 26
OK
127.0.0.1:6379[3]> dbsize
(integer) 1
127.0.0.1:6379[3]> exists name1
(integer) 1
------------------------------------------------
从当前数据库移动键值对到另一个数据库(move)
127.0.0.1:6379[3]> keys *
1) "name2"
2) "name1"
127.0.0.1:6379[3]> move name1 2
(integer) 1
127.0.0.1:6379[3]> select 2
OK
127.0.0.1:6379[2]> keys *
1) "name1"
------------------------------------------------
设置键值对过期时间(expire)
127.0.0.1:6379[2]> get name1
"26"
127.0.0.1:6379[2]> expire name1 10
(integer) 1
127.0.0.1:6379[2]> ttl name1 #查看当前key剩余时间
(integer) 6
127.0.0.1:6379[2]> ttl name1
(integer) -2
127.0.0.1:6379[2]> keys *
(empty list or set)
------------------------------------------------
查看当前key储存数据类型(type)
127.0.0.1:6379[3]> keys *
1) "name2"
127.0.0.1:6379[3]> type name2
string
```

## 二、基本数据类型

### 1，String类型操作

```bash
字符串添加在末尾(append)
127.0.0.1:6379> keys *
1) "test"
127.0.0.1:6379> get test
"hello"
127.0.0.1:6379> append test this #如果当前key不存在，就直接set new key
(integer) 9
------------------------------------------------
查看字符串长度(strlen)
127.0.0.1:6379> get test
"hellothis"
127.0.0.1:6379> strlen test
(integer) 9
------------------------------------------------
步长设置(java里面的i++)
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> incr views #增长views (i++)
(integer) 1
127.0.0.1:6379> incr views
(integer) 2
127.0.0.1:6379> get views
"2"
127.0.0.1:6379> decr views #减少views (i--)
(integer) 1
127.0.0.1:6379> incrby views 4 #每次增加4 (i+=4)
(integer) 5
127.0.0.1:6379> decrby views 2 #每次减少2 (i-=2)
(integer) 3
------------------------------------------------
获取部分字符串(getrange)等同于substring
127.0.0.1:6379> set test helloworld
OK
127.0.0.1:6379> getrange test 0 3 # ！注意是[0,3]不是"hel"
"hell"
127.0.0.1:6379> getrange test 0 -1 #显示整个字符串
"helloworld"
------------------------------------------------
替换指定位置的字符串(setrange)
127.0.0.1:6379> get test
"helloworld"
127.0.0.1:6379> setrange test 3 xxxx
(integer) 10
127.0.0.1:6379> get test
"helxxxxrld"
127.0.0.1:6379> setrange test 9 hhhh
(integer) 13
127.0.0.1:6379> get test
"helxxxxrlhhhh"
127.0.0.1:6379> setrange test -1 123
(error) ERR offset is out of range
------------------------------------------------
设置过期时间(setex): set with expire
如果不存在则设置(setnx): set if not exist
127.0.0.1:6379> setex hello 10 testing
OK
127.0.0.1:6379> ttl hello
(integer) 7
127.0.0.1:6379> keys *
1) "test"
2) "hello"
127.0.0.1:6379> setnx test2 13
(integer) 1
127.0.0.1:6379> keys *
1) "test"
2) "test2"
127.0.0.1:6379> setnx test2 18
(integer) 0
127.0.0.1:6379> get test2
"13"
------------------------------------------------
批量设置及获取(mset mget)
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3
OK
127.0.0.1:6379> keys *
1) "test"
2) "test2"
3) "k1"
4) "k3"
5) "k2"
127.0.0.1:6379> mget k1 k2 k3
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k4 v4 k5 v5 #批量设置
(integer) 1
127.0.0.1:6379> keys *
1) "test"
2) "test2"
3) "k4"
4) "k1"
5) "k3"
6) "k2"
7) "k5"
127.0.0.1:6379> msetnx k3 v8 k6 v6 #如果有一个key存在则批量设置都不成功
(integer) 0
127.0.0.1:6379> keys *
1) "test"
2) "test2"
3) "k4"
4) "k1"
5) "k3"
6) "k2"
7) "k5"
------------------------------------------------
设置对象
127.0.0.1:6379> mset user:1:name danny user:1:gpa 3.8
OK
127.0.0.1:6379> keys *
1) "user:1:gpa"
2) "user:1:name"
127.0.0.1:6379> get user:1:gpa
"3.8"
------------------------------------------------
先get再set值(getset) 若get的值没有则返回(nil)
127.0.0.1:6379> set test 12
OK
127.0.0.1:6379> getset test 13
"12"
127.0.0.1:6379> get test
"13"
```

### 2，List

```bash
list添加元素和遍历(lpush, lrange)
127.0.0.1:6379> flushall
OK
127.0.0.1:6379> lpush list 1
(integer) 1
127.0.0.1:6379> lpush list 2
(integer) 2
127.0.0.1:6379> lpush list 3 
(integer) 3
127.0.0.1:6379> lrange list 0 -1 #注意lpush是从左侧开始
1) "3"
2) "2"
3) "1"
127.0.0.1:6379> lrange list 0 1
1) "3"
2) "2"
127.0.0.1:6379> rpush list hello #rpush从右侧添加元素
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "3"
2) "2"
3) "1"
4) "hello"
------------------------------------------------
list删除元素(lpop, rpop)
127.0.0.1:6379> lrange list 0 -1
1) "3"
2) "2"
3) "1"
4) "hello"
127.0.0.1:6379> lpop list #从左边删除
"3"
127.0.0.1:6379> rpop list #从右边删除
"hello"
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "1"
------------------------------------------------
list获取值(lindex)
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "1"
127.0.0.1:6379> lindex list 0
"2"
127.0.0.1:6379> lindex list 1
"1"
------------------------------------------------
获取list长度(llen)
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "1"
127.0.0.1:6379> llen list
(integer) 2
------------------------------------------------
删除指定的值(lrem)
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "2"
3) "2"
4) "2"
5) "1"
127.0.0.1:6379> lrem list 3 1
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "2"
3) "2"
4) "2"
127.0.0.1:6379> lrem list 3 2
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "0"
------------------------------------------------
截取一段(ltrim): 如同java的substring
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "3"
3) "2"
4) "1"
127.0.0.1:6379> ltrim list 0 2
OK
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "3"
3) "2"
------------------------------------------------
从source删除最后一位元素添加到destination的最左边(rpoplpush)
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "3"
3) "2"
127.0.0.1:6379> rpoplpush list newList
"2"
127.0.0.1:6379> lrange newList 0 -1
1) "2"
------------------------------------------------
修改list的值(lset)：仅适用于修改！
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "3"
127.0.0.1:6379> lset list 1 hello
OK
127.0.0.1:6379> lset list 3 7
(error) ERR index out of range
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "hello"
------------------------------------------------
在list中插入元素(linsert)
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "hello"
127.0.0.1:6379> linsert list after hello hi
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "hello"
3) "hi"
127.0.0.1:6379> linsert list before hi this
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "0"
2) "hello"
3) "this"
4) "hi"
```

### 3，Set

```bash
set里面添加元素(ssad)：！不能添加一样的元素
127.0.0.1:6379> sadd set hello
(integer) 1
127.0.0.1:6379> sadd set world
(integer) 1
127.0.0.1:6379> sadd set world
(integer) 0
127.0.0.1:6379> sadd set new
(integer) 1
------------------------------------------------
查看set里面所有的元素(smembers)及set里面的元素数量(scard)
127.0.0.1:6379> smembers set
1) "world"
2) "new"
3) "hello"
127.0.0.1:6379> scard set
(integer) 3
------------------------------------------------
判断set里面是否存在元素(sismember)
127.0.0.1:6379> sismember set this
(integer) 0
127.0.0.1:6379> sismember set hello
(integer) 1
------------------------------------------------
删除set中的元素(srem)
127.0.0.1:6379> srem set new
(integer) 1
127.0.0.1:6379> smembers set
1) "world"
2) "hello"
------------------------------------------------
随机获得set中的一个元素(srandmemerber)
127.0.0.1:6379> smembers set
1) "0"
2) "world"
3) "7"
4) "hello"
127.0.0.1:6379> srandmember set
"7"
127.0.0.1:6379> srandmember set
"0"
127.0.0.1:6379> srandmember set
"world"
------------------------------------------------
随机删除set中的元素(spop)
127.0.0.1:6379> smembers set
1) "0"
2) "world"
3) "7"
4) "hello"
127.0.0.1:6379> spop set
"hello"
127.0.0.1:6379> spop set
"0"
127.0.0.1:6379> smembers set
1) "world"
2) "7"
------------------------------------------------
移动set中的元素到另一个set(smove)
127.0.0.1:6379> smembers set
1) "world"
2) "7"
127.0.0.1:6379> sadd set2 hello
(integer) 1
127.0.0.1:6379> smove set set2 world
(integer) 1
127.0.0.1:6379> smembers set2
1) "world"
2) "hello"
------------------------------------------------
两个set的差(sdiff)
127.0.0.1:6379> smembers set1
1) "1"
2) "2"
3) "3"
127.0.0.1:6379> smembers set2
1) "2"
2) "3"
3) "4"
127.0.0.1:6379> sdiff set1 set2
1) "1"
127.0.0.1:6379> sdiff set2 set1
1) "4"
------------------------------------------------
两个set的交集(sinter)
127.0.0.1:6379> sinter set1 set2
1) "2"
2) "3"
------------------------------------------------
两个set的并集(sunion)
127.0.0.1:6379> sunion set1 set2
1) "1"
2) "2"
3) "3"
4) "4"
```

### 4，Hash

```bash
在hash中添加删除元素(hset, hget)
127.0.0.1:6379> hset myhash first 1
(integer) 1
127.0.0.1:6379> hget myhash first
"1"
------------------------------------------------
批量设置获取hash(hmset, hmget)
127.0.0.1:6379> hmset myhash second 2 third 3
OK
127.0.0.1:6379> hmget myhash first second third
1) "1"
2) "2"
3) "3"
------------------------------------------------
获取hash中的所有键值对(hgetall)
127.0.0.1:6379> hgetall myhash
1) "first"
2) "1"
3) "second"
4) "2"
5) "third"
6) "3"
------------------------------------------------
删除hash中指定的key(hdel)
127.0.0.1:6379> hgetall myhash
1) "first"
2) "1"
3) "second"
4) "2"
5) "third"
6) "3"
127.0.0.1:6379> hdel myhash second
(integer) 1
127.0.0.1:6379> hgetall myhash
1) "first"
2) "1"
3) "third"
4) "3"
------------------------------------------------
获取hash字符长度(hlen)
127.0.0.1:6379> hgetall myhash
1) "first"
2) "1"
3) "third"
4) "3"
127.0.0.1:6379> hlen myhash
(integer) 2
------------------------------------------------
判断hash中的key存不存在(hexists)
127.0.0.1:6379> hgetall myhash
1) "first"
2) "1"
3) "third"
4) "3"
127.0.0.1:6379> hexists myhash second
(integer) 0
127.0.0.1:6379> hexists myhash third
(integer) 1
------------------------------------------------
获取所有的keys和values(hkeys, hvals)
127.0.0.1:6379> hgetall myhash
1) "first"
2) "1"
3) "third"
4) "3"
127.0.0.1:6379> hkeys myhash
1) "first"
2) "third"
127.0.0.1:6379> hvals myhash
1) "1"
2) "3"
------------------------------------------------
设置hash中一个值步长(hincrby)：没有hdecrby，直接设置incr<0
127.0.0.1:6379> hgetall myhash
1) "first"
2) "1"
3) "third"
4) "3"
127.0.0.1:6379> hincrby myhash third 2
(integer) 5
127.0.0.1:6379> hget myhash third
"5"
------------------------------------------------
如果hash中的key不存在则设置(hsetnx)
127.0.0.1:6379> hsetnx myhash second 2
(integer) 1
127.0.0.1:6379> hsetnx myhash second 5
(integer) 0
```

### 5，Zset

```bash
添加一个或多个值(zadd)
127.0.0.1:6379> zadd zset 1 hello
(integer) 1
127.0.0.1:6379> zadd zset 2 world
(integer) 1
127.0.0.1:6379> zrange zset 0 -1
1) "hello"
2) "world"
------------------------------------------------
设置范围排序(zrangebyscore)
127.0.0.1:6379> zrangebyscore zset -inf +inf #-inf +inf为下限上限
1) "hello"
2) "world"
------------------------------------------------
带分数的后缀(withscores)
127.0.0.1:6379> zrange zset 0 -1 withscores
1) "hello"
2) "1"
3) "world"
4) "2"
------------------------------------------------
移除zset中的元素(zrem)
127.0.0.1:6379> zrange zset 0 -1 withscores
1) "hello"
2) "1"
3) "world"
4) "2"
127.0.0.1:6379> zrem zset hello
(integer) 1
127.0.0.1:6379> zrange zset 0 -1 withscores
1) "world"
2) "2"
------------------------------------------------
获取zset中元素的个数(zcard)
127.0.0.1:6379> zcard zset
(integer) 1
------------------------------------------------
获取指定区间的元素数量(zcount)
127.0.0.1:6379> zrange zset 0 -1 withscores
1) "this"
2) "2"
3) "world"
4) "2"
5) "hello"
6) "3"
127.0.0.1:6379> zcount zset 2 3
(integer) 3
127.0.0.1:6379> zcount zset (2 3 # (2是开区间
(integer) 1

```

## 三、特殊数据类型

### 1，Geospatial

```bash
设置一个地理位置(geoadd)
127.0.0.1:6379> geoadd china 116.405285 39.904989 Beijing
(integer) 1
------------------------------------------------
获取一个地理位置(geopos)
127.0.0.1:6379> geopos china Shenzhen
1) 1) "114.08594459295273"
   2) "22.546999937739663"
------------------------------------------------
两个地理位置的距离(geodist)
127.0.0.1:6379> geodist china Shenzhen Beijing km
"1943.0240"
------------------------------------------------
以指定半径距离找到附近的地址(georadius)
127.0.0.1:6379> georadius china 110 40 2000 km withcoord
1) 1) "Shenzhen"
   2) 1) "114.08594459295273"
      2) "22.546999937739663"
2) 1) "Shanghai"
   2) 1) "121.47264629602432"
      2) "31.23170490709807"
3) 1) "Beijing"
   2) 1) "116.40528291463852"
      2) "39.904988422912503"
------------------------------------------------
以指定元素和半径找到其他元素(georadiusbymember)
127.0.0.1:6379> georadiusbymember china Beijing 2000 km withdist
1) 1) "Shenzhen"
   2) "1943.0240"
2) 1) "Shanghai"
   2) "1067.5980"
3) 1) "Beijing"
   2) "0.0000"
------------------------------------------------
将经纬度坐标转换为字符串(geohash)
127.0.0.1:6379> geohash china Beijing Shenzhen
1) "wx4g0b7xrt0"
2) "ws10k0dcg10"
```

**因为Geospatial数据类型底层为Zset，所以可以用Zset查询删除Geospatial**

```bash
127.0.0.1:6379> zrange china 0 -1
1) "Shenzhen"
2) "Shanghai"
3) "Beijing"
127.0.0.1:6379> zrem china Beijing
(integer) 1
127.0.0.1:6379> zrange china 0 -1
1) "Shenzhen"
2) "Shanghai"
```

### 2，Hyperloglog

```bash
数据添加(pfadd)
127.0.0.1:6379> pfadd myset 1 2 3 4 5
(integer) 1
------------------------------------------------
查询数据长度(pfcount)
127.0.0.1:6379> pfadd myset2 6 7 8 9 10
(integer) 1
127.0.0.1:6379> pfcount myset2
(integer) 5
------------------------------------------------
合并两个set在一起(pfmerge)
127.0.0.1:6379> pfmerge myset3 myset myset2
OK
127.0.0.1:6379> pfcount myset3
(integer) 10
```

### 3，Bitmap

```bash
添加元素到bit(setbit)
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 1
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
------------------------------------------------
获取元素(getbit)
127.0.0.1:6379> getbit sign 4
(integer) 1
127.0.0.1:6379> getbit sign 5
(integer) 0
------------------------------------------------
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 1
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> bitcount sign #总共有四天是1(打卡成功)
(integer) 4
```

## 四、事务操作

```bash
开始事务操作(multi)
127.0.0.1:6379> multi
OK
------------------------------------------------
输入命令
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
------------------------------------------------
执行命令(exec)
127.0.0.1:6379> exec
1) OK
2) OK
3) "v2"
------------------------------------------------
取消事务执行(discard)
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> discard
OK
127.0.0.1:6379> get k2
(nil)
```

**若在一次事务执行中发生编译失误（代码报错），则所有的命令都不会执行**

**若在一次事务执行中运行错误（1/0）， 其他命令可以正常执行**

## 五、监视

### 1，悲观锁

### 2，乐观锁(watch)

监视数据，若在另一台服务器执行修改操作则通过redis监视的乐观锁取消当前服务器命令执行

#unwatch：取消监视

## 六、Jedis

