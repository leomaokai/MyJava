

或在 /usr/bin	目录下

访问redis		redis-cli     redis-cli -h ip -p passwd       redis-cli -p  port

配置文件 `/etc/redis/redis.conf`

```bash
redis-server  /etc/redis/redis.conf  
#开启服务(一个配置文件代表一个服务)
redis-cli	#连接默认的6379服务
redis-cli -p port -a password	#连接其它端口号服务
```

[Redis入门笔记](../进阶提高/Redis/Redis入门.md)

# 服务器相关命令

```bash
config set requirepass 12345  #将密码设置为12345
auth password		#验证密码
ping PONG			#返回响应是否连接成功
echo 				#在命令行打印一些内容
select 0~15 		#打开编号的数据库
quit/exit 			#退出客户端
dbsize 				#返回当前数据库中所有key的数量
info 				#返回redis的相关信息
config get dir/* 	#实时传储收到的请求
flushdb 			#删除当前选择数据库中的所有keybash
flushall			#删除所有数据库中的数据库
shutdown 			#退出服务
info replication	#查看相关信息
```

# key基本操作

```shell
exists key      # 确认一个key是否存在
set key value   # 设置key和value
get key         # 获取key的value
del key         # 删除一个key
type key        # 返回值的类型
keys pattern    # 返回满足给定pattern的所有key
random key      # 随机返回key空间的一个
key rename oldname newname    # 重命名key
db size         # 返回当前数据库中key的数目
select index    # 选择第0~15中的库
move key dbindex      # 移动当前数据库中的key到dbindex数据库
expire key seconds    #设置key的有效时间  单位为秒
ttl key               
			#获取key的剩余有效时间，持久key返回-1，key不存在返回-2，具体时间返回秒数
persist key           #设置有时效性的key为持久key
```


# value基本操作

## String

```shell
INCR key    				# 递增数字，仅仅对数字类型的键有用
INCRBY key increment    	# key自增increment，increment可以为负数，表示减少。
DECR key    				# 递减数字，仅仅对数字类型的键有用
DECRBY key decrement    	# key自减decrement，decrement可以为正数，表示增加。
INCRBYFLOAT key increment   # 增加指定浮点数，仅仅对数字类型的键有用
APPEND key value    		# 向尾部追加值,相当于append方法
STRLEN key    				# 获取字符串长度
MSET key1 value1 [key2 value2 ...]    # 同时设置多个key的值
MGET key1 [key2 ...]                  #  同时获取多个key的值
```

## List

```shell
rpush key value    			# 在名称为key的list尾添加一个值为value的元素
lpush key value    			# 在名称为key的list头添加一个值为value的 元素
llen key    				# 返回名称为key的list的长度
lrange key start end   		# 返回名称为key的list中start至end之间的元素
ltrim key start end   		# 截取名称为key的list
lindex key index    		# 返回名称为key的list中index位置的元素
lset key index value   	 	# 给名称为key的list中index位置的元素赋值
lrem key count value    	# 删除count个key的list中值为value的元素
lpop key    				# 返回并删除名称为key的list中的首元素
rpop key    				# 返回并删除名称为key的list中的尾元素
rpoplpush srckey dstkey    	
# 返回并删除名称为srckey的list的尾元素，并将该元素添加到名称为dstkey的list的头部
```

## Set

```shell
sadd key member    		# 向名称为key的set中添加元素member
srem key member    		# 删除名称为key的set中的元素member
spop key    			# 随机返回并删除名称为key的set中一个元素
smove srckey dstkey member  # 移到集合元素
scard key    				# 返回名称为key的set的基数
sismember key member    	# member是否是名称为key的set的元素
sinter key1 key2 …key   	# 求交集
sinterstore dstkey keys    	# 求交集并将交集保存到dstkey的集合
sunion key1 keys    		# 求并集
sunionstore dstkey keys    	# 求并集并将并集保存到dstkey的集合
sdiff key1 keys    			# 求差集
sdiffstore dstkey keys    	# 求差集并将差集保存到dstkey的集合
smembers key    			# 返回名称为key的set的所有元素
srandmember key    			# 随机返回名称为key的set的一个元素
```

## Zset

```shell
ZADD key score1 value1 [score2 value2 score3 value3 ...]    #  添加元素
ZSCORE key value    					# 获取元素的分数
ZRANGE key start stop [WITHSCORE]  		
# 获取排名在某个范围的元素，按照元素从小到大的顺序排序，从0开始编号，包含start和stop对应的元素，WITHSCORE选项表示是否返回元素分数
ZREVRANGE key start stop [WITHSCORE] 	
# 获取排名在某个范围的元素，和上一个命令用法一样，只是这个倒序排序的。
ZRANGEBYSCORE key min max    			
# 获取指定分数范围内的元素，包含min和max，(min表示不包含min，(max表示不包含max，+inf表示无穷大
ZINCRBY key increment value    			# 增加某个元素的分数
ZCARD key    							# 获取集合中元素的个数
ZCOUNT key min max   	 # 获取指定分数范围内的元素个数，min和max的用法和5中的一样
ZREM key value1 [value2 ...]    # 删除一个或多个元素
ZREMRANGEBYRANK key start stop  # 按照排名范围删除元素
ZREMRANGEBYSCORE key min max    # 按照分数范围删除元素，min和max的用法和4中的一样
ZRANK key value    				# 获取正序排序的元素的排名
ZREVRANK key value    			# 获取逆序排序的元素的排名
```

## Hash

```shell
HSET key field value    					# 赋值
HMSET key field1 value1 [field2 values]    	# 一次赋值多个字段
HSET key field    							# 取值
HMSET key field1 [field2] 		# 一次取多个字段的值
HGETALL key    					# 一次取所有字段的值
HEXISTS key field    			# 判断字段是否存在
HSETNX key field value    		# 当字段不存在时赋值
HDEL key field    				# 删除字段
HKEYS key    					# 获取所有字段名
HVALS key    					# 获取所有字段值
HLEN key    					# 获取字段数量
```



