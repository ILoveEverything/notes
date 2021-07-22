# Redis

##  连接redis:

- `redis-cli`

##  切换数据库

- 数据库没有名称，默认有16个，通过0-15来标识，连接redis默认选择第一个数据库
  `select 10`

##  数据操作
### string类型
#### 增加、修改

- 设置一个值

  `set key value`

- 设置键值及过期时间，以秒为单位

  `setex key seconds value`

- 设置多个键值
  `mset key1 value1 key2 value2 ...`

- 追加值
  `append key value`

####  删除
- 删除键及对应的值
  `del key1 key2 ...`
- 设置过期时间，以秒为单位
  `expire key seconds`
- 查看有效时间，以秒为单位
  `ttl key`

#### 查询
- 根据键获取值，如果不存在此键则返回nil
  `get key`

- 根据多个键获取多个值
  `mget key1 key2 ...`

- 查看所有键
  `keys *`
- 查看名称中包含a的键
  ` keys a*`
- 判断键是否存在，如果存在返回1，不存在返回0
  `exists key`
- 查看键对应的value的类型
  ``type key`

### hash类型
#### 增加、修改

- 设置单个属性

  `hset key field value`

- 设置多个属性

  `hmset key field1 value1 field2 value2 ...`

#### 删除

-  删除整个hash键及值
  `del key`
- 删除属性及对应的值
  `hdel key field1 field2 ...`

#### 查询
- 获取指定键所有的属性
  `hkeys key`
- 获取⼀个属性的值
  `hget key field`
- 获取多个属性的值
  `hmget key field1 field2 ...`
- 获取所有属性的值
  `hvals key`

### list类型
#### 增加
- 在左侧插⼊数据
  `lpush key value1 value2 ...`
- 在右侧插⼊数据
  `rpush key value1 value2 ...`
- 在指定元素的前或后插⼊新元素
  `linsert key before或after 现有元素 新元素`

#### 删除
- 删除指定元素
  - 将列表中前count次出现的值为value的元素移除
    - count > 0: 从头往尾移除
    - count < 0: 从尾往头移除
    - count = 0: 移除所有

  `lrem key count value`

#### 查询
- 返回列表⾥指定范围内的元素
  `lrange key start stop`
- 获取指定索引位置的元素值
  `lset key index value`

### set类型(⽆序集合)
#### 增加
- 添加元素
  `sadd key member1 member2 ...`

#### 删除
- 删除指定元素
  `srem key member`

#### 查询
- 返回所有的元素
  `smembers key`

### zset类型(有序集合)

- 每个元素都会关联⼀个double类型的score，表示权重，通过权重将元素从⼩到⼤排序

#### 增加
- 添加(score:权重)
  `zadd key score1 member1 score2 member2 ...`

#### 删除
- 删除指定元素
  `zrem key member1 member2 ...`

- 删除权重在指定范围的元素

  `zremrangebyscore key min max`

#### 查询
- 返回指定范围内的元素
  `zrange key start stop`
- 返回score值在min和max之间的成员
  `zrangebyscore key min max`
- 返回成员member的score值
  `zscore key member`