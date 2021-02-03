

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

# SpringBoot-Redis

## 使用

```xml
<!--Redis-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```yml
spring:
  # redis 配置
  redis:
    # 超时时间
    timeout: 10000ms
    # 服务地址
    host: 192.168.64.137
    # 服务端口
    port: 5002
    # 数据库
    database: 1
    lettuce:
      pool:
        # 最小空闲连接
        min-idle: 5
        # 最大连接数
        max-active: 1024
        # 最大连接阻塞时间
        max-wait: 10000ms
        # 最大空闲连接
        max-idle: 200
```

```java
@Autowired
private RedisTemplate redisTemplate;
```

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){

        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        // String类型的key序列化
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        // String类型的value序列化
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        // hash类型的序列化
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());

        redisTemplate.setConnectionFactory(redisConnectionFactory);
        return redisTemplate;
    }
}
```

## 直接方法

```java
//删除key
public void delete(String key){
    redisTemplate.delete(key);
}
//删除多个key
public void deleteKey (String ...keys){
    redisTemplate.delete(keys);
}
//指定key的失效时间
public void expire(String key,long time){
    redisTemplate.expire(key,time,TimeUnit.MINUTES);
}
//根据key获取过期时间
public long getExpire(String key){
    Long expire = redisTemplate.getExpire(key);
    return expire;
}
//判断key是否存在
public boolean hasKey(String key){
    return redisTemplate.hasKey(key);
}
```

## String


```java
//1、通过redisTemplate设置值
redisTemplate.boundValueOps("StringKey").set("StringValue");
redisTemplate.boundValueOps("StringKey").set("StringValue",1, TimeUnit.MINUTES);

//2、通过BoundValueOperations设置值
BoundValueOperations stringKey = redisTemplate.boundValueOps("StringKey");
stringKey.set("StringVaule");
stringKey.set("StringValue",1, TimeUnit.MINUTES);

//3、通过ValueOperations设置值
ValueOperations ops = redisTemplate.opsForValue();
ops.set("StringKey", "StringVaule");
ops.set("StringValue","StringVaule",1, TimeUnit.MINUTES);
```

```java
//设置过期时间
redisTemplate.boundValueOps("StringKey").expire(1,TimeUnit.MINUTES);
redisTemplate.expire("StringKey",1,TimeUnit.MINUTES);
```

```java
//1、通过redisTemplate设置值
String str1 = (String) redisTemplate.boundValueOps("StringKey").get();

//2、通过BoundValueOperations获取值
BoundValueOperations stringKey = redisTemplate.boundValueOps("StringKey");
String str2 = (String) stringKey.get();

//3、通过ValueOperations获取值
ValueOperations ops = redisTemplate.opsForValue();
String str3 = (String) ops.get("StringKey");
```

```java
//删除key
Boolean result = redisTemplate.delete("StringKey");
//顺序递增
redisTemplate.boundValueOps("StringKey").increment(3L);
//顺序递减
redisTemplate.boundValueOps("StringKey").increment(-3L);
```

## Hash

```java
//1、通过redisTemplate设置值
redisTemplate.boundHashOps("HashKey").put("SmallKey", "HashVaue");

//2、通过BoundValueOperations设置值
BoundHashOperations hashKey = redisTemplate.boundHashOps("HashKey");
hashKey.put("SmallKey", "HashVaue");

//3、通过ValueOperations设置值
HashOperations hashOps = redisTemplate.opsForHash();
hashOps.put("HashKey", "SmallKey", "HashVaue");
```

```java
//设置过期时间
redisTemplate.boundValueOps("HashKey").expire(1,TimeUnit.MINUTES);
redisTemplate.expire("HashKey",1,TimeUnit.MINUTES);
```

```java
//添加一个Map集合
HashMap<String, String> hashMap = new HashMap<>();
redisTemplate.boundHashOps("HashKey").putAll(hashMap );
```

```java
//提取所有的小key
//1、通过redisTemplate获取值
Set keys1 = redisTemplate.boundHashOps("HashKey").keys();

//2、通过BoundValueOperations获取值
BoundHashOperations hashKey = redisTemplate.boundHashOps("HashKey");
Set keys2 = hashKey.keys();

//3、通过ValueOperations获取值
HashOperations hashOps = redisTemplate.opsForHash();
Set keys3 = hashOps.keys("HashKey");
```

```java
//获取所有的值
//1、通过redisTemplate获取值
List values1 = redisTemplate.boundHashOps("HashKey").values();

//2、通过BoundValueOperations获取值
BoundHashOperations hashKey = redisTemplate.boundHashOps("HashKey");
List values2 = hashKey.values();

//3、通过ValueOperations获取值
HashOperations hashOps = redisTemplate.opsForHash();
List values3 = hashOps.values("HashKey");
```

```java
//根据小key获取value值
//1、通过redisTemplate获取
String value1 = (String) redisTemplate.boundHashOps("HashKey").get("SmallKey");

//2、通过BoundValueOperations获取值
BoundHashOperations hashKey = redisTemplate.boundHashOps("HashKey");
String value2 = (String) hashKey.get("SmallKey");

//3、通过ValueOperations获取值
HashOperations hashOps = redisTemplate.opsForHash();
String value3 = (String) hashOps.get("HashKey", "SmallKey");
```

```java
//获取所有键值对的集合
//1、通过redisTemplate获取
Map entries = redisTemplate.boundHashOps("HashKey").entries();

//2、通过BoundValueOperations获取值
BoundHashOperations hashKey = redisTemplate.boundHashOps("HashKey");
Map entries1 = hashKey.entries();

//3、通过ValueOperations获取值
HashOperations hashOps = redisTemplate.opsForHash();
Map entries2 = hashOps.entries("HashKey");
```

```java
//删除小key
redisTemplate.boundHashOps("HashKey").delete("SmallKey");
//删除大key
redisTemplate.delete("HashKey");
```

```java
//判断hash中是否有该值
Boolean isEmpty = redisTemplate.boundHashOps("HashKey").hasKey("SmallKey");
//判断hash是否还有该键
Boolean isEmpty = redisTemplate.boundHashOps("HashKey").containsKey("SmallKey");
```

## Set

```java
//1、通过redisTemplate设置值
redisTemplate.boundSetOps("setKey").add("setValue1", "setValue2", "setValue3");

//2、通过BoundValueOperations设置值
BoundSetOperations setKey = redisTemplate.boundSetOps("setKey");
setKey.add("setValue1", "setValue2", "setValue3");

//3、通过ValueOperations设置值
SetOperations setOps = redisTemplate.opsForSet();
setOps.add("setKey", "SetValue1", "setValue2", "setValue3");
```

```java
//设置过期时间
redisTemplate.boundValueOps("setKey").expire(1,TimeUnit.MINUTES);
redisTemplate.expire("setKey",1,TimeUnit.MINUTES);
```

```java
//1、通过redisTemplate获取值
Set set1 = redisTemplate.boundSetOps("setKey").members();

//2、通过BoundValueOperations获取值
BoundSetOperations setKey = redisTemplate.boundSetOps("setKey");
Set set2 = setKey.members();

//3、通过ValueOperations获取值
SetOperations setOps = redisTemplate.opsForSet();
Set set3 = setOps.members("setKey");
```

```java
//根据value从set中查询是否存在
Boolean isEmpty = redisTemplate.boundSetOps("setKey").isMember("setValue2");
//获取Set的长度
Long size = redisTemplate.boundSetOps("setKey").size();
//移除指定的值
Long result1 = redisTemplate.boundSetOps("setKey").remove("setValue1");
//移除指定的键
Boolean result2 = redisTemplate.delete("setKey");
```

## List

```java
//1、通过redisTemplate设置值
redisTemplate.boundListOps("listKey").leftPush("listLeftValue1");
redisTemplate.boundListOps("listKey").rightPush("listRightValue2");

//2、通过BoundValueOperations设置值
BoundListOperations listKey = redisTemplate.boundListOps("listKey");
listKey.leftPush("listLeftValue3");
listKey.rightPush("listRightValue4");

//3、通过ValueOperations设置值
ListOperations opsList = redisTemplate.opsForList();
opsList.leftPush("listKey", "listLeftValue5");
opsList.rightPush("listKey", "listRightValue6");
```

```java
//将Java中集合List加入Redis的List中
ArrayList<String> list = new ArrayList<>();
redisTemplate.boundListOps("listKey").rightPushAll(list);
redisTemplate.boundListOps("listKey").leftPushAll(list);
```

```java
//设置过期时间
ArrayList<String> list = new ArrayList<>();
redisTemplate.boundListOps("listKey").rightPushAll(list);
redisTemplate.boundListOps("listKey").leftPushAll(list);
```

```java
//获取List缓存全部内容(起始索引,结束索引)
List listKey1 = redisTemplate.boundListOps("listKey").range(0, 10); 
```

```java
//从左或右弹出一个值
String listKey2 = (String) redisTemplate.boundListOps("listKey").leftPop();  //从左侧弹出一个元素
String listKey3 = (String) redisTemplate.boundListOps("listKey").rightPop(); //从右侧弹出一个元素
```

```java
//根据索引查询元素
String listKey4 = (String) redisTemplate.boundListOps("listKey").index(1);
//获取List的长度
Long size = redisTemplate.boundListOps("listKey").size();
//根据索引修改List中某条数据(key,索引,值)
redisTemplate.boundListOps("listKey").set(3L,"listLeftValue3");
//移除N个值为value(key,移除个数,值)
redisTemplate.boundListOps("listKey").remove(3L,"value");
```

## Zset

```java
//向集合中插入元素,并设置分数
//1、通过redisTemplate设置值
redisTemplate.boundZSetOps("zSetKey").add("zSetVaule", 100D);

//2、通过BoundValueOperations设置值
BoundZSetOperations zSetKey = redisTemplate.boundZSetOps("zSetKey");
zSetKey.add("zSetVaule", 100D);

//3、通过ValueOperations设置值
ZSetOperations zSetOps = redisTemplate.opsForZSet();
zSetOps.add("zSetKey", "zSetVaule", 100D);
```

```java
//向集合中插入多个元素,并设置分数
DefaultTypedTuple<String> p1 = new DefaultTypedTuple<>("zSetVaule1", 2.1D);
DefaultTypedTuple<String> p2 = new DefaultTypedTuple<>("zSetVaule2", 3.3D);
redisTemplate.boundZSetOps("zSetKey").add(new HashSet<>(Arrays.asList(p1,p2)));
```

```java
//按照排名先后(从小到大)打印指定区间内的元素, -1为打印全部
Set<String> range = redisTemplate.boundZSetOps("zSetKey").range(key, 0, -1);
//获得指定元素的分数
Double score = redisTemplate.boundZSetOps("zSetKey").score("zSetVaule");
//返回集合内的成员个数
Long size = redisTemplate.boundZSetOps("zSetKey").size();
//返回集合内指定分数范围的成员个数（Double类型）
Long COUNT = redisTemplate.boundZSetOps("zSetKey").count(0D, 2.2D);
//返回集合内元素在指定分数范围内的排名（从小到大）
Set byScore = redisTemplate.boundZSetOps("zSetKey").rangeByScore(0D, 2.2D);
//带偏移量和个数(key,起始分数,最大分数,偏移量,个数)
Set<String> ranking2 = redisTemplate.opsForZSet().rangeByScore("zSetKey", 0D, 2.2D 1, 3);
//返回集合内元素的排名,以及分数（从小到大）
Set<TypedTuple<String>> tuples = redisTemplate.boundZSetOps("zSetKey").rangeWithScores(0L, 3L);
for (TypedTuple<String> tuple : tuples) {
    System.out.println(tuple.getValue() + " : " + tuple.getScore());
}
//返回指定成员的排名
//从小到大
Long startRank = redisTemplate.boundZSetOps("zSetKey").rank("zSetVaule");
//从大到小
Long endRank = redisTemplate.boundZSetOps("zSetKey").reverseRank("zSetVaule");
//从集合中删除指定元素
redisTemplate.boundZSetOps("zSetKey").remove("zSetVaule");
//删除指定索引范围的元素(Long类型)
redisTemplate.boundZSetOps("zSetKey").removeRange(0L,3L);
//删除指定分数范围内的元素(Double类型)
redisTemplate.boundZSetOps("zSetKey").removeRangeByScorssse(0D,2.2D);
//为指定元素加分(Double类型)
Double score = redisTemplate.boundZSetOps("zSetKey").incrementScore("zSetVaule",1.1D);
```







