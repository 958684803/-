[[FLASK]]

==需要在配置文件配置==
```
# 采用Redis保存数据，默认是内存，需要安装flask-redis
RATELIMIT_STORAGE_URL = 'redis://127.0.0.1:6379/0'

# 限制策略：移动窗口：时间窗口会自动变化
RATELIMIT_STRATEGY = 'moving-window'
```

#Redis数据库介绍
Redis是一个开源的使用ANSI  C语言编写、支持网络、可基于内存亦可持久化的日志型、key-value数据库，并提供多中语言api
#Redis数据库介绍/NoSQl 
## 1. Nosql: 一类新出现的数据库
- 泛指非关系型的数据库
- 不支持sql语法
- 存储结构跟传统的关系型数据库表完全不同，nosql存储类型都是key-value形式
- NoSQL的世界中没有一种通用的语言，每种nosql数据库都有自己的api跟语法，以及擅长的业务场景
- NoSQL中的产品种类相当多：
		-  Redis
		- Mongodb
		- Hbase hadoop
		- Cassandra hadoop
		- 
### 1.1 NoSQL 和SQL数据库的比较：
- 适用场景不同：sql数据库适用于关系特别复杂的数据查询场景，nosq反之
- 饰物特性的支持：sql对于事物的支持非常完善，而nosql基本不支持事务
- 两者在不断的取长补短，呈现融合趋势

### 1.2 Redis特性
 ==Redis 与其他 key-value缓存产品有以下三个特点==:
 - Redis 支持数据的持久化，可以将内存中的数据保存到磁盘中，重启的时候可以再次的加载使用
 - Redis 不仅仅支持简单的key-value数据库 还支持 set , list , zset, hash 等数据结构的存储
 - Redis 支持数据的备份，即master-slave模式的数据备份

### 1.3 Redis应用场景
- 用来做缓存(ehcache/memcached)——redis的所有数据是放在内存中的（内存数据库）
- 可以在某些特定应用场景下替代传统数据库——比如社交类的应用
- 在一些大型系统中，巧妙地实现一些特定的功能：session共享、购物车

### 1.4 Redis 安装

- step1: 下载
>wget http://download.redis.io/releases/redis-4.0.9.tar.gz

- step2: 解压
> 在Linux操作系统中
> tar -zxvf reids-4.0.9.tar.gz

- step3: 移动，放到usr/local目录中
>mv ./redis-4.0.9 /usr/local/redis/

- step4: 进入redis目录
> cd /usr/local/redis

- step5: 生成
> make

![[Pasted image 20221214184343.png]]

- step6: 测试这段运行时间会较长
![[Pasted image 20221214184445.png]]

- step7: 安装，将redis的命令安装到/usr/local/bin 目录
> make install

- step8: 安装完成后，我们进入到目录/usr/local/bin 中查看
>cd /usr/local/bin ls -all
	-redis-server redis服务器
	-redis-cli redis命令行客户端
	-redis-benchmark redis性能测试工具
	-redis-check-aof AOF文件修复工具
	-redis-check-rdb RDB文件检索工具

- step9: 配置文件，移动到/etc/目录下
	- 配置文件目录为 /usr/local/redis/redis.conf 
	>先复制一份redis.conf
	>cp /usr/local/redis/redis.conf /etc/redis/
	
### 1.5 Redis 配置
- 绑定 ip：如果需要远程访问，可将此行注释掉，或绑定一个真ip
>bind 192.168.168.3

- 端口，默认为6379 不用动

- 是否以守护进程运行
	- 如果以守护进程运行，则不会在命令行阻塞，类似于服务
	- 如果以非守护进程运行，则当前终端被阻塞
	- 设置为yes表示守护进程，设置为no表示非守护进程
	- 推荐设置为yes
>  daemonize yes

- 数据文件
>dbfilename dump.rdb

- 数据文件存储路径
> div/var/lib/redis

- 日志文件
>logfile "/var/log/redis/redis-server.log"

- 数据库，默认有16个
> database 16

### 1.6  服务器命令

==服务器端的命令为redis-server + 当前配置文件的目录/etc/redis/redis.conf==
帮助 : redis-server --help
ps aux | grep redis-4.0.9 查看redis服务器进程
		kill -9 pid 杀死redis服务器
		==**redis-server /etc/redis/redis.conf 指定加载的配置文件**==

### 1.7 数据操作命令

- 客户端的命令为redis-cli -h 192.168.180.3
- 数据库没有名称，默认有16个，通过0-15来选择，连接redis默认选择第一个数据库
> select 0

### 1.8 数据结构

- redis 是 key-value 的数据结构，每一条数据都是一个键值对
- 键的类型是字符串
-== 注意：键不能重复==
![[Pasted image 20221214194720.png]]
- 键的类型分为5种：
	- 字符串：string
	- 哈希 ：hash
	- 列表 ：list
	- 集合 ： set
	- 有序集合 ：zset

## 2. Redis各种数据类型操作命令

### 2.1 string类型

- 字符串数据类型是redis数据库中最基础的数据存储类型，它在redis中是二进制安全的，这便意味着该类型可以接受任何格式的数据，如JPEG图像数据或Json对象描述信息等。在Redis中字符串类型的Value最多可以容纳的数据长度是512M。
**保存**
如果设置的键不存在则为添加，如果设置的键已经存在则修改
>设置键值
>>			set key value
			
- 例1：设置键为name值为 yuzai 的数据
			`set name yuzai`

>设置键值及过期时间，以秒为单位
>>		setex key seconds value

- 例2： 设置键为aa，值为aa，过期时间为3秒的数据
		`setex aa 3 aa`
	![[Pasted image 20221214195815.png]]
>设置多个键值
>> 			mset key1 value1 key2 value2

- 例3： 设置键为a1  值为python、键为a2 值为java 、键为a3 值为c
			`mset a1 python a2 java a3 c`
![[Pasted image 20221214200904.png]]
>追加值
>> 			append key value

- 例4： 向键为a1中追加haha
		`append a1 haha`
	![[Pasted image 20221214201039.png]]
**获取**
> 获取： 根据键获取值，如果不存在则返回null
>>		get key
>> 例5：获取键name的值
>>> get name

>>> 根据多个键获取多个值
>>> >mget key1 key2 ...
>>例6： 	获取键a1、a2、a3的值
>>> mget a1 a2 a3
![[Pasted image 20221214201835.png]]
### 2.2 键命令
>查找键，参数支持正则表达式
>>  keys pattern

- 例1: 查看所有的键
		`key *`
		![[Pasted image 20221214202033.png]]
- 例2：查看名称中包含a的键
		`keys a*`
		![[Pasted image 20221214202135.png]]
>判断键是否存在，如果存在返回1，不存在返回0
>>	exists key1
- 例3： 判断a1是否存在
		`exists a1`
	![[Pasted image 20221214202325.png]]
>查看键对应的value的类型
>> type key
- 例4： 查看a1 的值类型，为redis支持的五种类型中的一种
		`type a1`
		![[Pasted image 20221214202459.png]]
>删除键对应的值
>>	del key1 key2 ..
- 例5：删除a2、a3
		`del a2 a3`
		![[Pasted image 20221214202608.png]]
>设置过期时间，以秒为单位
>>如果没有指定过期时间则一直存在，直到使用DEL移除
>>>	expire key seconds
- 例6： 设置键a1 的过期时间为3秒
		`expire a1 3`
		![[Pasted image 20221214202812.png]]
>查看有效时间,以秒为单位
>> 	ttl key
- 例7：查看键bb 的有效时间
		`ttl bb`
		![[Pasted image 20221214202932.png]]
### 2.3 hash类型
- hash用于存储对象，对象的结构为属性、值
- 值的类型为string

==增加、修改==

>设置单个属性
>> hset key field value
- 例1： 设置键user的属性name为itheima
		`hset user name itheima`

>设置多个属性
>> hmset key field1 value1 field2 value2 ...
- 例2： 设置键u2的属性name为laoxiao、属性age为11
		`hmset u2 name laoxiao age 11`

>获取指定键所有的属性
>>	hkeys key
- 例3： 获取键u2的所有属性
		`hkeys u2`
		
>获取一个属性的值
>>	hget key field
- 例4： 获取u2属性name的值
		`hget u2 name`

>获取多个属性的值
>>	hget key field1 field2...
- 例5: 获取键u2属性，name、age的值
		`hget u2 name age`

>获取所有属性的值
>>	hvals key
- 例6： 获取键u2所有属性的值
		`hvals u2`

**删除**
>删除整个hash键及值，使⽤del命令
>>删除属性，属性对应的值会被⼀起删除
		hdel key fifield1 fifield2 ...

- 例7：删除键 u2 的属性 age
		`hdel u2 age`
**可能出现的错误**
![[Pasted image 20221214204348.png]]
MISCONF Redis is confifigured to save RDB snapshots, but is currently not able to persist on disk.

Commands that may modify the data set are disabled. Please check Redis logs for details about theerror.

Redis被配置为保存数据库快照，但它目前不能持久化到硬盘。用来修改集合数据的命令不能用
>原因：
>>强制关闭Redis快照导致不能持久化。
>>解决方案：
>>>运行confifig set stop-writes-on-bgsave-error no 命令后，闭配置项stop-writes-on-bgsave-error解决该问题。

### 2.4 list类型

- 列表的元素类型为string
- 按照插⼊顺序排序

**增加**

>在左侧插⼊数据
>>	lpush key value1 value2 ...
- 例1：从键为 a1 的列表左侧加⼊数据 a 、 b 、c
		`lpush a1 a b c`

>在右侧插⼊数据
>>rpush key value1 value2 ...
- 例2：从键为 a1 的列表右侧加⼊数据 0、1
		`rpush a1 0 1`

>在指定元素的前或后插⼊新元素
>>linsert key before或after 现有元素 新元素
- 例3：在键为 a1 的列表中元素 b 前加⼊ 3
		`linsert a1 before b 3`

>返回列表⾥指定范围内的元素
>>>start 、 stop 为元素的下标索引
>>>索引从左侧开始，第⼀个元素为0
>>>索引可以是负数，表示从尾部开始计数，如 -1 表示最后⼀个元素
>>>	lrange key start stop
- 例4：获取键为 a1 的列表所有元素
		`lrange a1 0 -1`

>设置指定索引位置的元素值
>>索引从左侧开始，第⼀个元素为0
>>>索引可以是负数，表示尾部开始计数，如 -1 表示最后⼀个元素
>>>>	lset key index value
- 例5：修改键为 a1 的列表中下标为 1 的元素值为z
		`lset a 1 z`

**删除**

- 删除指定元素
	- 将列表中前 count 次出现的值为 value 的元素移除
	- count > 0: 从头往尾移除
	- count < 0: 从尾往头移除
	- count = 0: 移除所有
>>>	lrem key count value

- 例6.1：向列表 a2 中加⼊元素 a、b、a、b、a、b
		`lpush a2 a b a b a b`

- 例6.2：从 a2 列表右侧开始删除2个 b
		`lrem a2 -2 b`

- 例6.3：查看列表 a2 的所有元素
		`lrange a2 0 -1`

### 2.5 set类型

- ⽆序集合
- 元素为string类型
- 元素具有唯⼀性，不重复
- 说明：对于集合没有修改操作

**增加**

>添加元素
>>	sadd key member1 member2 ...
- 例1：向键 a3 的集合中添加元素 zhangsan 、 lisi 、 wangwu
		`sadd a3 zhangsan sili wangwu`
		
**获取**

>返回所有的元素
>>smembers key
- 例2：获取键 a3 的集合中所有元素
		`smembers a3`

**删除**

>删除指定元素
>> srem key
- 例3：删除键 a3 的集合中元素 wangwu
		`srem a3 wangwu`

### 2.6 zset类型

- sorted set，有序集合
- 元素为string类型
- 元素具有唯⼀性，不重复
- 每个元素都会关联⼀个double类型的score，表示权重，通过权重将元素从⼩到⼤排序
- 说明：没有修改操作

**增加**

>添加
>>	zadd key score1 member1 score2 member2 ...
- 例1：向键 a4 的集合中添加元素 lisi 、 wangwu 、 zhaoliu 、 zhangsan ，权重分别为 4、5、6、3
		`zadd a4 4 lisi 5 wangwu 6 zhaoliu 3 zhangsan`

**获取**

> 返回指定范围内的元素
>>start、stop为元素的下标索引
>>> 索引从左侧开始，第⼀个元素为0
>>>>索引可以是负数，表示从尾部开始计数，如 -1 表示最后⼀个元素
>>>>>	zrange key start stop
- 例2：获取键 a4 的集合中所有元素
		`zrange a4 0 -1`

>返回 score 值在 min 和 max 之间的成员
>>	zrangebyscore key min max
- 例3：获取键 a4 的集合中权限值在 5和6之间 的成员
		`zrangebyscore a4 5 6`

>返回成员 member 的 score 值
>>zscore key member
- 例4：获取键 a4 的集合中元素 zhangsan 的权重
		`zscore a4 zhangsan`
		
**删除**

>删除指定元素
>>	zrem key member1 member2 ...
- 例5：删除集合 a4 中元素 zhangsan
		`zrem a4 zhangsan`

>删除权重在指定范围的元素
>>	zremrangebyscore key min max
- 例6：删除集合 a4 中权限在 5、6之间 的元素
		`zremrangebyscore a4 5 6`

### 2.7 与python交互

**安装包**
> pip install redis

- 引入模块
> from redis import StrictRedis

- 这个模块中提供了strictRedis对象，用于连接redis服务器，并按照不同类型提供了不同方法，进行交互操作

==StrictRedis对象方法==

- 通过init创建对象，指定参数host、post与指定的服务器和端口连接，host默认为localhost，port默认为6379，db默认为0

```python
sr = StrictRedis(host='localhost', port=6379, db=0)

简写

sr=StrictRedis()
```
- 根据不同的数据类型，拥有不同的实例方法可以调用，与前面学的redis命令对应，方法需要的参数与命令的参数一致
>string
>>	set
>>	setex
>>	mset
>>	append
>>	get
>>	mget
>>	key
>keys
>>	exists
>>	type
>>	delete
>>	expire
>>	getrange
>>	ttl
>hash
>>	hset
>>	hmset
>>	hkeys
>>	hget
>>	hmget
>>	hvals
>>	hdel
>list
>>	lpush
>>	rpush
>>	linsert
>>	lrange
>>	lset
>>	lrem
>set
>>	sadd
>>	smembers
>>	srem
>zset
>>	zadd
>>	zrange
>>	zrangebyscore
>>	zscore
>>	zrem
>>	zremrangebyscore

### 2.8 flask-redis

flask-redis 是对 redis的封装扩展 ，使用起来非常方便

```
pip istall flask-redis
from flask_redis import FlaskRedis  
flask-redis 的配置非常方便，只需要在配置文件中增加REDIS_URL的配置即可

REDIS_URL = "redis://:password@localhost:6379/0"
flask-redis 初始化非常简单，只需要两行代码即可

reids_client = FlaskRedis()
...
redis_cline.init_app(app)
```


![[Pasted image 20221214211055.png]]
