# 第一章——什么是Redis

## 类型

NoSQL数据库（Not OnlySQL）（**非关系型数据库**）

## 作用

存储任意内容

## 使用场景

作为数据缓存，因为其读取速度快，能够大大的提高运行效率，在大多数情况下也被称作缓存



![](D:\编程日志\d4-1-2-1.png)

## 支持数据类型

Set、ZSet、List、Hash、String

## **持久化储存**

把数据从内存保存在磁盘上而保证计算机掉电不丢失

Redis利用RDB和AOF做数据的持久化存储。主从数据同时生成rdb文件，并利用缓冲区添加新的数据更新操作做对应的同步。

1. rdb：rdb在指定的时间间隔内将内存中的数据集快照写入磁盘
2. aof：aof以日志的形式记录服务器所处理的每一个写、删除操作，查询操作不会记录，以文本的方式记录，可以打开文件看到详细的操作记录

频率：10w/s

## 缺点

1. 短时间内大量增加数据，可能导致内存不够用
2. 持久性存储的性能损耗问题。生成的rdb文件会占用主机的cpu
3. 重启速度可能慢

# 第二章——高并发用户注册

## cookie&session

​	cookie保存在客户端（浏览器中）用户可以看到且可以修改且有大小限制，session保存在服务器端，无大小限制，取决于服务器的硬件条件。出现cookie技术主要是因为http是无状态的协议

### 	无状态

​		浏览器发出的每次请求都是独立，相互完全没有关联的

### 	实现有状态的Web

​	原因：在实际使用场景中，像先登录再下单购物这些方面都需要实现有状态的web

1. 升级http协议，变成有状态的设计
2. http协议不变，使用其他技术辅助（session、cookie、各种headers）

### Session需要解决的问题

缺点

1. 服务器一旦出现问题比如重启，所有的Session都会丢失，所有用户都需要登录。
2. 中大型网站往往是分布式架构，有很多服务器，Session放到一台服务器上，其他服务器就读不到登录状态了。

解决方法

​	利用Redis缓存Session，因为Redis相对稳定，且可以供多台服务器共享

## SpringBoot集成Redis

### pom依赖

​	

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



### 配置

```java
# Redis服务器地址
spring.redis.host=192.168.0.1
# Redis 服务器端口号
spring.redis.port=6379
# Redis 服务器密码 （请换成自己机器密码）
spring.redis.password=
```

## 注册性能优化

### 问题：

有些人恶意攻击，频繁注册，服务器在短时间内收到了大量的用户注册，导致数据库处理变慢。应用处理请求过慢，会导致请求大量积压堵塞，大量堵塞的请求消耗大量内存，最终导致服务器崩溃。

### 解决方法：

​	先访问Redis内存，再访问数据库

### 模型序列化接口

​	要实现将用户数据缓存在Redis里的功能，用户模型必须实现序列化接口

```java
import java.io.Serializable;

public class UserDO implements Serializable {

}
```

### 读写缓存Value

```java
UserDO userDO = (UserDO)redisTemplate.opsForValue().get(userName);
```

```java
redisTemplate.opsForValue().set(userName, userDO1);
```

### 删除缓存

```java
redisTemplate.delete(userName);
```

## 用户Session

### pom依赖

```java
<!-- spring session 支持 -->
<dependency>
  <groupId>org.springframework.session</groupId>
  <artifactId>spring-session-data-redis</artifactId>
</dependency>
<dependency>
  <groupId>org.redisson</groupId>
  <artifactId>redisson-spring-boot-starter</artifactId>
  <version>3.13.0</version>
</dependency>
```

spring-session-data-redis是核心依赖库，会自动变成Session同步到Redis等操作

redisson-spring-boot-starter是需要用到的Redis客户端，提供很多分布式相关操作服务，操作Session数据的过程中，与核心依赖库搭配使用

### 修改Session配置类

```java
@Configuration
@EnableSpringHttpSession
public class SpringHttpSessionConfig {
    @Bean
    public CookieSerializer cookieSerializer() {
        ... ...
        ... ...
    }

    @Bean
    public MapSessionRepository sessionRepository() {
        return new MapSessionRepository(new ConcurrentHashMap<>());
    }
}
```

系统未提供默认的存储Session数据的仓库，所有在sessionRepository()中new一个仓库对象

#### ConcurrentHashMap

 	是一个支持高并发更新与查询的哈希表(基于HashMap)

##### 如何高效的执行并发操作

![](D:\编程日志\70d75c961cb94d43ab500bd9b95c4955.png)

​	根据上面hashMap的数据结构可以直观的看到，如果以整个容器为一个资源进行锁定，那么就变为了串行操作。而根据hash表的特性，具有冲突的操作只会出现在同一槽位，而与其它槽位的操作互不影响。
​	基于此种判断，那么就可以将资源锁粒度缩小到槽位上，这样热点一分散，冲突的概率就大大降低，并发性能就能得到很好的增强。

#### cookieSerializer()

​	作用：定制cookie中的session信息内容如何写

#### 核心

删除@EnableSpringHttpSession，改为使用@EnableRedisHttpSession。然后删除sessionRepository()方法，不再需要创建仓库了

#### 过期时间

`@EnableRedisHttpSession` 时，可以通过 maxInactiveIntervalInSeconds注解设置session数据的过期失效时间

`@EnableRedisHttpSession(maxInactiveIntervalInSeconds = 300)`

## 缓存穿透

### 问题出现

​	有人用了工具软件，大量、密集、持续登录请求，但都是请求输入错误的，不存在的账号

![](D:\编程日志\d4-2-5-1.png)

### 主要原因

​	只考虑到了数据正确时的情况，没有考虑到错误数据带来的风险

### 解决漏洞

​	第一次从数据库查询不到数据时，仍然把这个空结果进行缓存。不过，要设置过期时间，注意过期时间不要太长，推荐不过五分钟。

```java
// 只 new 实例但不设置任何属性，相当于一个空对象
userDO = new UserDO();
redisTemplate.opsForValue().set(userName, userDO, 5, TimeUnit.MINUTES);
```

### 攻击的后续处理

​	并不能代表彻底解决攻击，持续攻击也有可能造成Redis撑满。

## 乱码问题

​	增加一个config类

```java
@Autowired
private RedisTemplate redisTemplate;

@Bean
public RedisTemplate redisTemplateInit() {
  //设置序列化Key的工具
  redisTemplate.setKeySerializer(new StringRedisSerializer());
  //设置序列化Value的工具
  redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
  // 设置 hash 的 key
  redisTemplate.setHashKeySerializer(new GenericJackson2JsonRedisSerializer());
  // 设置 hash 的 value
  redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
  redisTemplate.afterPropertiesSet();
  return redisTemplate;
}
```

Key用字符串StringRedisSerializer的序列化/反序列化方式，Value用JSON（GenericJackson2JsonRedisSerializer）的序列化/反序列化方式。

因为Hash是一种Redis的数据类型，`setHashKeySerializer()` 和 setHashValueSerializer()就是支持hash的。

之后便可以利用可视化工具查看。

### 时间类型的序列化

public class Category implements Serializable {

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd HH:mm:ss")
    @JsonDeserialize(using = LocalDateTimeDeserializer.class)
    @JsonSerialize(using = LocalDateTimeSerializer.class)
    private LocalDateTime gmtCreated;

}

# 第三章——商品类目系统

## 什么是类目

### 	显著特点：有着明确的层级关系，范围从大到小、由粗到细、越来越精确

## 类目设计

### 	核心步骤

1. 模型设计
2. 服务设计

常见的延伸步骤：

1. 技术选型
   * 框架选型：基本就是SpringBoot
   * 存储方案选型：从Mongodb和MySQL选择其一，再研究是否需要搭配Redis缓存
2. 完成web层，包括完成control和页面

## 技术选型

如果按找普通的实现类目的方式，每访问一次类目就需要调用一次数据库。数据库的压力太大，这个场景很适合Redis的数据结构**list**

## 插入类目数据

概念：是简单的字符串列表，按照插入顺序，可以添加到头部（左边）或者尾部（右边）。一个列表最多可以包含2^32-1个元素（超过40亿个元素）

### 添加List数据

leftPush（）

```java
// 第一个对象，演示代码省略 set 各属性值
Category category = new Category();
redisTemplate.opsForList().leftPush("categoryList", category);

// 第二个对象，演示代码省略 set 各属性值
category = new Category();
redisTemplate.opsForList().leftPush("categoryList", category);
```

rightPush（）

```java
// 第一个对象，演示代码省略 set 各属性值
Category category = new Category();
redisTemplate.opsForList().rightPush("categoryList", category);

// 第二个对象，演示代码省略 set 各属性值
category = new Category();
redisTemplate.opsForList().rightPush("categoryList", category);
```

### 返回值

无论是`leftPush（）``还是rightPush（）`，都有返回值，返回值类型是Long，返回的含义是执行完添加操作以后列表的长度

## 查询类目数据

### 查询列表长度

```java
Long size = redisTemplate.opsForList().size("categoryList");
```

### 根据索引查询

```java
Category category = (Category)redisTemplate.opsForList().index("categoryList", index);
```

第一个索引是0

### 范围查询

```java
List<Category> categoryDatas = redisTemplate.opsForList().range("categoryList", 0, 1);
```

首尾范围皆包含

如果想查询整个表列表的所有数据，第三个参数填-1

```java
range("categoryList", 0, -1)
```

## 修改类目数据

### 基本语法

```java
// 演示代码省略 set 各属性值
Category category = new Category();
redisTemplate.opsForList().set("categoryList", 0, category);
```

## 删除类目数据

### 从头部删除数据：

​	

```java
Category cat = (Category)redisTemplate.opsForList().leftPop("categoryList");
```

### 从尾部删除数据：

```java
Category cat = (Category)redisTemplate.opsForList().rightPop("categoryList");
```

# 第四章——高并发产品抢购

## Reids事务

将一个业务逻辑作为一个整体一起执行。事务其实就是打包一组操作作为一个整体，在事务处理时将顺序执行这些操作，并返回结果，如果其中任何一个环节出错，所有的操作将被取消。

### 事务基础框架代码

```java
try {
    redisTemplate.execute(new SessionCallback<List<Object>>() {
        @Override
        public List<Object> execute(RedisOperations operations) throws DataAccessException {

        }
    });
} catch (Exception e) {
    LOG.error("redisTemplate.execute() error. ", e);
}
```

在开启事务前，要选择一个要监听的对象，即Redis中的某个key

分为三个阶段：开启事务->命令入列->执行事务

```java
redisTemplate.execute(new SessionCallback<List<Object>>() {
    @Override
    public List<Object> execute(RedisOperations operations) throws DataAccessException {
        //监听商品的ID
        operations.watch(id);

        //开启事务
        operations.multi();

        // 插入一条订单数据。
        // 缓存库的存减 1
        // 数据库的库存减 1

        // 执行事务
        List exec = operations.exec();
    }
});
```

取消事务

```java
operations.discard();
```

## 分布式锁

### 分布式结构

就是将一个完整的系统，按照业务功能，拆分成一个个独立的子系统，在分布式结构中，每个子系统被称为”服务“

### 分布式锁

synchronized为本地锁只能锁一个服务器，分布式锁能锁多个服务器

### pom依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.redisson</groupId>
        <artifactId>redisson-spring-boot-starter</artifactId>
        <version>3.13.0</version>
    </dependency>
</dependencies>
```

### 分布锁实现

```java
@Autowired
private RedissonClient redissonClient;
```

### 取得锁

```java
RLock rLock = redissonClient.getLock("CUSTOM_NAME");
```

名称没有规定，但一般使用容易理解的、业务相关的名称

上锁

```java
rLock.lock();
```

并发情况下，每个线程都会竞争锁

* 竞争成功的线程会继续运行
* 竞争失败的线程会被禁用，并且重新获取锁之前，该线程将一直处于休眠状态

### 解锁

```java
rLock.unlock();
```

### 总流程

```java
try {
  rLock.lock();
  // 抢购业务逻辑
} catch (Exception e) {
    LOG.error("some error. ", e);
} finally {
    rLock.unlock();
}
```

## 过期处理

将验证码缓存到Redis里，但是想要在1000毫秒之后过期

```java
redisTemplate.opsForValue().set("code", "78987", 1000, TimeUnit.MILLISECONDS);
```

后面的单位代表毫秒，还有其他的单位，如TimeUnit.MILLMINUTES(分钟)

### 设置Redis List的过期时间

```java
redisTemplate.expire("category", 60, TimeUnit.MINUTES);
```

redisTemplate.expire()是一个通用方法，可以为Redis的任何数据结构设置过期时间

### 删除策略

1. 惰性删除：每次查询或写键时，都会检查取得的键是否过期。对CPU友好，对内存不好有些键一直没被访问到就一直占着存储空间
2. 定期删除：每隔一段时间就对数据库进行检查，删除里面的过期键，由算法决定
3. 定时删除：在定时器在键的过期时间来临时，立即执行对键的删除操作，对CPU不好，对过期键的删除会占用CPU的一部分时间

## 分布式ID

### 背景

​	随着业务的不断增长，数据量会越来越大，有时候就需要将一张表分为多张表，此时各张表就会根据自己的节奏来进行自增，这样就会造成ID冲突

过程代码

```java
//格式化格式为年月日
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyyMMdd");
//获取当前时间
String now = LocalDate.now().format(dateTimeFormatter);
//通过redis的自增获取序号
RAtomicLong atomicLong = redissonClient.getAtomicLong(now);
atomicLong.expire(1, TimeUnit.DAYS);
long number = atomicLong.incrementAndGet();
//拼装订单号
String orderId = now + "" + number;
```

先获取一个自增的实例对象

```java
RAtomicLong atomicLong = redissonClient.getAtomicLong(now);
```

再取得实际子增值

```java
long number = atomicLong.incrementAndGet();
```

### 整数格式转化为字符串

```java
String.format("%04d", 12);
```

生成0012的字符串

### 依赖

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.redisson</groupId>
            <artifactId>redisson-spring-boot-starter</artifactId>
            <version>3.13.0</version>
        </dependency>

```

### 注意

要在配置文件里加上Redis数据库的相关信息

# 第五章——玩家排行榜

## 战绩模型设计

![](D:\编程日志\PersonalRecord.svg)

注意此模型既有用户id又有用户模型，虽然有些冗余，主要是用于不同的功能。

## 查询个人战绩排行榜

### 根据排序值升序查询

```Java
//0和-1代表查询该键的所有值默认是按升序排序
Set tv = redisTemplate.opsForZSet().rangeWithScores("TV", 0, -1)
```

### 根据排序值降序查询

```java
//reverseRangeWithScores是根据score降序排序
Set tv = redisTemplate.opsForZSet().reverseRangeWithScores("TV", 0, -1);
```

### 遍历返回值Set集合

```java
Set<TypedTuple<PersonalRecord>> datas = redisTemplate.opsForZSet().rangeWithScores("integralRank", 0, -1);

// 遍历
datas.forEach(data -> {
    // 存入的对象
    PersonalRecord pr = data.getValue();
    // 对应的分数
    Double score = data.getScore();
    System.out.println(pr.getId() + " - " + score);
});
```

返回Set集合中的元素类型是TypedTuple，用getValue（）可以取得放入ZSet的元素对象

## 缓存排行榜的用户数据

### 什么是Hash

Redis Hash是一个字符串类型的field和value的映射表，Hash特别适合用于存储对象，就是key-value的嵌套，value其实还是一个key-value。

![](D:\编程日志\hash.jpeg)

### 新增和修改

```java
redisTemplate.opsForHash().put("integralRankUser", userDO.getUserName(), userDO);
```

### 读取

可以根据Key和field精确查询

```java
UserDO userDO = (UserDO)redisTemplate.opsForHash().get("integralRankUser", userName);
```

也可以批量查询

```java
List<String> userNames = new ArrayList<>();
userNames.add(userName);
List<UserDO> users = redisTemplate.opsForHash().multiGet("integralRankUser", userNames);
```

### 删除

```java
redisTemplate.opsForHash().delete("integralRankUser", userName);
```

delete支持多参数，想删除多个往后加即可

```java
redisTemplate.opsForHash().delete("integralRankUser", userName, "zhangsan", "lisi");
```

在方法的参数后面加三个点...就表示变长参数

```java
Long delete(H key, Object... hashKeys)
```

## Set

### 特点

1. 集合中的元素是无序的
2. 集合中的元素不能重复，是唯一的

### 新增数据操作

```java
redisTemplate.opsForSet().add("ranks", personalRecord1, personalRecord2);
```

删除数据操作

```java
redisTemplate.opsForSet().remove("ranks", personalRecord);
```

### 基本查询

```java
Set<PersonalRecord> datas = redisTemplate.opsForSet().members("ranks");
```

### 多集合操作

#### 求并集

```java
List<String> keys = new ArrayList<>();
keys.add("ranks1");
keys.add("ranks2");
keys.add("ranks3");
Set<Long> unionDatas = redisTemplate.opsForSet().union(keys);
```

求交集

```java
List<String> keys = new ArrayList<>();
keys.add("ranks1");
keys.add("ranks2");
keys.add("ranks3");
Set<Long> interDatas = redisTemplate.opsForSet().intersect(keys);
```

求差集

```java
List<String> otherkeys = new ArrayList<>();
otherkeys.add("ranks2");
otherkeys.add("ranks3");
Set<Long> diffDatas = redisTemplate.opsForSet().difference("ranks1", otherkeys);
```

# 第六章——常见面试题

## Redis为什么这么快

1. 完全基于内存
2. 数据结构简单
3. 采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多线程切换消耗CPU资源
4. 使用多路I/O复用模型，非阻塞IO
5. 使用底层模型不同，他们之间底层实现方式以及与客户端之间通信的应用协议不一样，Redis直接自己构建了VM机制

### 常见数据结构

1. String
2. List
   * 字符串列表
   * 按照插入顺序排序
3. ZSet
   * 字符串集合
   * 不允许重复
   * 按score（double浮点数）排序
4. Hash
5. Set（适合多集合运算）

## 缓存穿透、雪崩与击穿

### 缓存穿透

key对应的数据在数据源并不存在，每次针对Key的请求从缓存获取不到。

#### 解决方案

1. 简单解决法：缓存空对象，但注意超时时间不能过长
2. 系统解决发：采用布隆过滤器，将所有可能存在的数据都缓存，那么一个一定不存在的数据会被拦截

### 缓存雪崩

当缓存服务器重启或者大量缓存集中在某一个时间段失效。

缓存失效瞬间示意图如下：

![](D:\编程日志\xuebeng2.png)

#### 解决方案

1. 简单解决法：缓存失效时间分散开，比如在我们原有的失效时间上增加一个随机值，比如1-5分钟随机
2. 严谨解决法：用加锁或者队列的方式保证不会有大量的线程对数据库进行一次性读写，从而避免失效时大量的并发请求落到底层存储系统上

### 缓存击穿

Key对应的数据存在，但在Redis中过期

#### 区别

雪崩强调的是大量的数据缓存出现过期的问题，击穿侧重于描述少量Key（热点数据）过期时遇到大量并发请求时出现的问题

#### 解决方案

使用锁机制，在缓存失效的时候，不是立即去数据库中查询，而是先加锁。目的就是防止大并发请求在短时间内请求数据库。

## Key值过大要如何处理

### 单个key存储的value很大

可以把每个属性作为一个file存入hash结构的缓存中，而不是整个对象序列化成字符串存入Redis

### hash、set、zset、list中存储过多的元素

拆分。把大容器拆成小容器

固定每个小容器的数量，例如10000.每个小桶的key带一个后缀。

如果一台Redis不够，可以考虑对Redis扩容。实际上多台Redis可以组成缓存集群。

那么可以在java程序中计算field的hash值，再根据Redis机器的数量模除，算出field存在哪台Redis的哪个key中。