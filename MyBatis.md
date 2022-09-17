# 第一章——MyBatis入门

## MyBatis简介

### ORM

对象关系映射，实现不同类型系统的数据之间的转换

### 优势

更精细的掌控SQL，优化每一个SQL语句

### pom依赖

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.2</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

### mysql数据源配置

```properties
spring.datasource.url=jdbc:mysql://mysql数据库地址:数据库端口/数据库名称?serverTimezone=GMT%2B8
spring.datasource.username=用户名
spring.datasource.password=密码
```

## MyBatis映射对象

### DO对象规则

所有的ORM框架都需要有一个java对象来映射数据库的表，并且是一一对应的，一般我们把这类对象称为DO对象，对象的命名规范是表名+DO

对应的DO包一般放在xxx.xxx.dataobject中，如下图所示

```java
com.youkeda.comment.dataobject
```

### DO对象类型

常用的数据类型映射

| VARCHAR  | String |
| -------- | ------ |
| TEXT     | String |
| INTEGER  | int    |
| DOUBLE   | double |
| BIGINT   | long   |
| DATETIME | Dat    |

## MyBatis DAO

创造一个带Mapper注解的接口

```java
package com.youkeda.comment.dao;

import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface UserDAO {


}
```

### 引用 DAO

```java
package com.youkeda.comment.control;

import com.youkeda.comment.dao.UserDAO;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class UserController {

    @Autowired
    private UserDAO userDAO;

}
```

## MyBatis查询

1. 添加接口方法
2. 添加@Select注解
3. API测试

```java
@Controller
public class UserController {

  @Autowired
  private UserDAO userDAO;

  @GetMapping("/users")
  @ResponseBody
  public List<UserDO> getAll() {
    return userDAO.findAll();
  }

}
```

```java
@Select("SELECT id,user_name as userName,pwd,nick_name as nickName,avatar,gmt_created as gmtCreated,gmt_modified as gmtModified FROM user")
  List<UserDO> findAll();
```

## MyBatis插入

```java
 @Select("SELECT id,user_name as userName,pwd,nick_name as nickName,avatar,gmt_created as gmtCreated,gmt_modified as gmtModified FROM user")
  List<UserDO> findAll();
```

### 查看主键值

在添加了@Options主键后输出的内容就会包含主键，原先是没有包含的。

```java
 @Insert("INSERT INTO user (user_name, pwd, nick_name,avatar,gmt_created,gmt_modified) VALUES(#{userName}, #{pwd}, #{nickName}, #{avatar},now(),now())")
  @Options(useGeneratedKeys = true, keyColumn = "id", keyProperty = "id")
  int insert(UserDO userDO);
```

* useGeneratedKeys

  > 设置为true，代表允许数据库使用自增主键

* keyColumn

  > 设置表的主键字段名称，一般都是id

* keyProperty

  > 设置DO模型的主键字段，一般都是id

## MyBatis修改

```java
@Update("update user set nick_name=#{nickName},gmt_modified=now() where id=#{id}")
    int update(UserDO userDO);
```

## MyBatis删除

```java
@Delete("delete from user where id=#{id}")
    int delete(@Param("id") long id);
```

## MyBatis简单查询

可以通过@Param传递参数

```java
@Select("select id,user_name as userName,pwd,nick_name as nickName,avatar,gmt_created as gmtCreated,gmt_modified as gmtModified  from user  where user_name=#{userName} limit 1")
  UserDO findByUserName(@Param("userName") String name);
```

API测试

```java
@GetMapping("/user/findByUserName")
  @ResponseBody
  public UserDO findByUserName(@RequestParam("userName") String userName) {
    return userDAO.findByUserName(userName);
  }
```

# 第二章——XML语句

## 配置

```
mybatis.mapper-locations=classpath:com/youkeda/comment/dao/*.xml
```

### 文件目录

```
src/main/resources/com/youkeda/comment/dao/
```

当有多个路径，可以使用，隔开多个，比如

```
mybatis.mapper-locations=classpath:com/youkeda/dao/*.xml,classpath:com/youkeda/comment/dao/*.xml
```

## MyBatis XML Mapper‘

### 头信息

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

### mapper根节点

```xml
<mapper namespace="com.youkeda.comment.dao.UserDAO">


</mapper>
```

namespace是命名空间的含义

### resultMap

用于处理表和DO对象的属性映射，确保表中的每个字段都有属性可以匹配

```xml
<mapper namespace="com.youkeda.comment.dao.UserDAO">

  <resultMap id="userResultMap" type="com.youkeda.comment.dataobject.UserDO">
    <id column="id" property="id"/>
    <result column="user_name" property="userName"/>
  </resultMap>

</mapper>
```

* id

  > 唯一标示

* type

  > 对应的DO类完整路径。

### resultMap子节点

* id

  > 设置数据库主键字段信息，column对应的是表的字段名称，property对应的是DO属性名称

* result

  > 设置数据库其他字段信息，column属性对应的是表的字段名称，property对应的是DO属性名称

有了resultMap就不需要再写user_name as userName这种写法了

```java
select id,user_name as userName
```

## MyBatis XML Insert语句

```xml
<insert id="add" parameterType="com.youkeda.comment.dataobject.UserDO" >
    INSERT INTO user (user_name, pwd, nick_name,avatar,gmt_created,gmt_modified)
    VALUES(#{userName}, #{pwd}, #{nickName}, #{avatar},now(),now())
  </insert>
```

* id

  > 同DAO类的方法名

* parameterType

  > 用于传递参数类型，一般是和id方法返回的类型一致的

如果还想要获得插入的主键id值，还需要配置`useGeneratedKeys、keyProperty`，这和之前注解@Options一样

```xml
<insert id="add" parameterType="com.youkeda.comment.dataobject.UserDO" useGeneratedKeys="true" keyProperty="id">
    INSERT INTO user (user_name, pwd, nick_name,avatar,gmt_created,gmt_modified)
    VALUES(#{userName}, #{pwd}, #{nickName}, #{avatar},now(),now())
</insert>
```

## MyBatis XML Update/Deleter语句

### update节点

```xml
 <update id="update" parameterType="com.youkeda.comment.dataobject.UserDO">
        update user set nick_name=#{nickName},gmt_modified=now() where id=#{id}
    </update>
```

### delete节点

```xml
<delete id="delete">
        delete from user where id=#{id}
    </delete>
```

delete没有写parameterType，因为一般delete传输的参数由@Param组成，而这类一般通过Map数据类传递，而默认的就是Map类型，因此无需写该参数

## MyBatis XML Select

### XML模式的开发顺序

1. 创建DO对象
2. 创建DAO接口，配置@Mapper注解
3. 创建XML文件，并完成resultMap配置
4. 创建DAO接口方法
5. 创建对应的XML语句

```xml
<select id="findByUserName" resultMap="userResultMap">
  select * from user where user_name=#{userName} limit 1
</select>
```

这里多用了一个属性`resultMap`这个值我们一般配置为 该 XML 文件下的resultMap节点的id值

### bind标签

bind：可以用来在映射文件中定义变量，然后将输入参数中的值拼接一些其他[字符串](https://so.csdn.net/so/search?q=字符串&spm=1001.2101.3001.7020)后组成的字符串赋值给该变量。

```xml
<select id="pageQuery" resultMap="BaseResultMap">
    select id, name, price, purchase_num, product_intro,product_imgs,
    gmt_created, gmt_modified
    from product
    <bind name="currentIndex" value="pagination * pageSize"/>
    limit #{currentIndex},#{pageSize}
</select>
```

## 特别注意

像order这种表，可能其本身就是属于关键词，所以对其进行增删改查时使用反引号包裹

# 第三章——条件语句

## if语句

> 一般用于判断参数是否为null

```xml
<update id="update" parameterType="com.youkeda.comment.dataobject.UserDO">
  update user set
   <if test="nickName != null">
    nick_name=#{nickName},gmt_modified=now()
   </if>
   where id=#{id}
</update>
```

### 非空判断

```xml
<if test="@org.apache.commons.lang3.StringUtils@isNotBlank(productId)">
    prodcut_id = #{productId},
</if>
```

### jdbcType=InTEGER声明

作用

当Mybatis不能自动识别你传入对象的类型时。即你输入的是空值的时候

## Set语句

```xml
<update id="update" parameterType="com.youkeda.comment.dataobject.UserDO">
  update user
  <set>
    <if test="nickName != null">
      nick_name=#{nickName},
    </if>
    <if test="avatar != null">
      avatar=#{avatar},
    </if>
    gmt_modified=now()
  </set>
   where id=#{id}
</update>
```

注意这里有逗号互相隔开，最后一个语句无需逗号

\>=、<、<=、>、>=、&等表达式会导致MyBatis解析失败，所以需要用<![CDATA[ key ]]>包围住

```xml
<select id="search" resultMap="userResultMap">
  select * from user where
    <if test="keyWord != null">
      user_name like CONCAT('%',#{keyWord},'%')
        or nick_name like CONCAT('%',#{keyWord},'%')
    </if>
    <if test="time != null">
      and  gmt_created <![CDATA[ >= ]]> #{time}
    </if>
</select>
```

API调用

```java
 @GetMapping("/user/search")
    @ResponseBody
    public List<UserDO> search(@RequestParam("keyWord") String keyWord,
    @RequestParam("time")
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    LocalDateTime time) {
        return userDAO.search(keyWord, time);
    }
```

这里的时间参数要化为标准的格式

## where语句

常见问题：如果keyWord为null，就会变成下列情况

```sql
select * from user where
   and  gmt_created >= ?
```

MyBatis是有纠错能力的，利用where子句可以解决这种常见的问题

```xml
<select id="search" resultMap="userResultMap">
  select * from user
   <where>
      <if test="keyWord != null">
          user_name like CONCAT('%',#{keyWord},'%')
            or nick_name like CONCAT('%',#{keyWord},'%')
      </if>
      <if test="time != null">
        and  gmt_created <![CDATA[ >= ]]> #{time}
      </if>
   </where>
</select>
```

## 循环语句

```xml
<insert id="batchAdd" parameterType="java.util.List" useGeneratedKeys="true" keyProperty="id">
    INSERT INTO user (user_name, pwd, nick_name,avatar,gmt_created,gmt_modified)
    VALUES
    <foreach collection="list" item="it" index="index" separator =",">
        (#{it.userName}, #{it.pwd}, #{it.nickName}, #{it.avatar},now(),now())
    </foreach 
```

* collection

  > 是上下文集合的参数名称

* item

  > 是遍历的每一个数据的变量名称

* index

  > 集合的索引值

* separator

  > 遍历每条记录并添加分隔符

上述结果

```sql
INSERT INTO user (user_name, pwd, nick_name,avatar,gmt_created,gmt_modified)
    VALUES
    (?, ?, ?,?,now(),now()),
    (?, ?, ?,?,now(),now()),
    (?, ?, ?,?,now(),now())
```

#### 使用最多的场景：in

```xml
<select id="findByIds" resultMap="userResultMap">
    select * from user
    <where>
        id in
        <foreach item="item" index="index" collection="ids"
                    open="(" separator="," close=")">
            #{item}
        </foreach>
    </where>
</select>
```

* open

  > 表示节点开始时自定义的分隔符

* close

  > 表示节点结束时自定义的分隔符





# 第四章——进阶

## MyBatis 分页插件

利用pagehelper这个第三方插件

#### pom依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.13</version>
</dependency>
```

#### 使用

```java
@GetMapping("/users")
    @ResponseBody
    public List<UserDO> getAll() {
        // 设置当前页数为1，以及每页3条记录
        Page<UserDO> page = PageHelper.startPage(1, 3).doSelectPage(() -> userDAO.findAll());
        return page.getResult();
    }
```

> startPage第一个参数指定页数，第二个参数指定每页的记录数

Page对象我们可以得到

* getResult（）获取分数数据
* getPages（）获取总页数
* getTotal（）获取总记录数
* getPageNum（）获取当前页面数

在企业开发中，我们一般都会额外封装一个通用的分页模型Paging用于处理返回值

```java
package com.youkeda.comment.model;

import java.io.Serializable;
import java.util.List;

/**
 * 分页模型
 */
public class Paging<R> implements Serializable {

    private static final long serialVersionUID = 522660448543880825L;
    /**
     * 页数
     */
    private int pageNum;

    /**
     * 每页数量
     */
    private int pageSize = 15;
    /**
     * 总页数
     */
    private int totalPage;

    /**
     * 总记录数
     */
    private long totalCount;

    /**
     * 集合数据
     */
    private List<R> data;

    public Paging() {

    }

    public Paging(int pageNum, int pageSize, int totalPage, long totalCount, List<R> data) {
        this.pageNum = pageNum;
        this.pageSize = pageSize;
        this.totalPage = totalPage;
        this.totalCount = totalCount;
        this.data = data;
    }

    // 省略 getter、setter

}
```

API测试

```java
 @GetMapping("/users")
    @ResponseBody
    public Paging<UserDO> getAll() {
        // 设置当前页数为1，以及每页3条记录
        Page<UserDO> page = PageHelper.startPage(1, 3).doSelectPage(() -> userDAO.findAll());

        return new Paging<>(page.getPageNum(), page.getPageSize(), page.getPages(), page.getTotal(), page
                .getResult());
    }
```

### 计算总的页数的方法

```
int totalPage = (int)Math.ceil(counts / (param.getPageSize() * 1.0));
```



## Druid连接池

### 优势

* 性能优越
* 扩展能力极强
* 具备SQL拦截功能
* 具备数据统计分析功能，比如慢SQL分析

Druid的可维护性极强，可以进行监控

### pom依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.23</version>
</dependency>
```

### 开启监控的配置项

```
spring.datasource.druid.stat-view-servlet.enabled=true
spring.datasource.druid.stat-view-servlet.url-pattern=/druid/*
spring.datasource.druid.stat-view-servlet.login-username=druid
spring.datasource.druid.stat-view-servlet.login-password=druid
spring.datasource.druid.stat-view-servlet.allow=
spring.datasource.druid.stat-view-servlet.deny=
```

#### 本地的访问地址

http://localhost:8080/druid/login.html

> 然后就可以通过SQL监控来进行监控了

# 第五章——评论服务开发（一）

## 用户注册服务

### 通用返回类型，利用泛型

```java
package com.youkeda.comment.model;

import com.fasterxml.jackson.annotation.JsonProperty;
import java.io.Serializable;

/**
 * JSON 返回模型
 */
public class Result<D> implements Serializable {

    // 表示执行成功或失败
    @JsonProperty("isSuccess")
    private boolean success = false;

    // 返回消息短码，一般用于出错时，简短描述错误
    private String code;
    // 返回消息具体信息，一般用于出错时，比较详细的描述错误
    private String message;

    // 返回的具体数据
    private D data;

    // 省略 getter、setter

}
```

@JsonProperty，当为true时输出的就是isSuccess

### 实例化

```java
Result<User> result = new Result<>();
```

### 过滤掉空的内容

```properties
spring.jackson.deserialization.fail-on-unknown-properties=false
spring.jackson.default-property-inclusion=non_null
```

#### 两个配置的作用

* 允许序列化未知的字段，可以兼容java模型和JSON数据不一致的情况
* 忽略null字段



## 优化DO和Model互转

将转换代码抽象成公共方法

```java
 public User toModel() {
        User user = new User();
        user.setId(getId());
        user.setUserName(getUserName());
        user.setNickName(getNickName());
        user.setAvatar(getAvatar());
        user.setGmtCreated(getGmtCreated());
        user.setGmtModified(getGmtModified());
        return user;
    }
```

Model转DO

```
public ProductDO(Product product) {

    BeanUtils.copyProperties(product, this);

}
```

可以自动将相同的属性赋值上去

# 第六章——评论服务开发（二）

## 发送评论

### 接口的注解书写方式

```java
/**
     * 发布评论
     *
     * @param refId    外部 ID
     * @param userId   用户 ID
     * @param parentId 父评论 ID
     * @param content  评论内容
     * @return
     */
    public Result<Comment> post(String refId, long userId,
                            long parentId, String content);
```

### 安全处理

禁止用户输入的文本为HTML标签，使用commons-text这个库进行转义

#### pom依赖

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-text</artifactId>
    <version>1.8</version>
</dependency>
```

#### 转义

```java
String body = StringEscapeUtils.escapeHtml4(content);
```

#### 路径

```
StringEscapeUtils` 的完整路径是 `org.apache.commons.text.StringEscapeUtils
```

#### 获取userId

```java
long userId = (long) session.getAttribute("userId");
```

## 查询评论

### 映射子对象

（某个属性是个对象）利用association节点

```xml
 <resultMap id="commentModelResultMap" type="com.youkeda.comment.model.Comment">
    <id column="id" property="id"/>
    <result column="ref_id" property="refId"/>
    <result column="content" property="content"/>
    <result column="parent_id" property="parentId"/>
    <result column="gmt_created" property="gmtCreated"/>
    <result column="gmt_modified" property="gmtModified"/>
    <association property="author" javaType="com.youkeda.comment.model.User">
        <id property="id" column="user_id"/>
        <result column="user_name" property="userName"/>
        <result column="nick_name" property="nickName"/>
        <result column="avatar" property="avatar"/>
    </association>
</resultMap>
```

* property对应的是表的字段名称
* javaType对应的是Java对象

### Tree数据结构

```java
//查询所有的评论记录包含回复的
List<Comment> comments = commentDAO.findByRefId(refId);
//构建 map 结构
Map<Long, Comment> commentMap = new HashMap<>();
//初始化一个虚拟根节点，0 可以对应的是所有一级评论的父亲
commentMap.put(0L, new Comment());
//把所有的评论转换为 map 数据
comments.forEach(comment -> commentMap.put(comment.getId(), comment));
// 再次遍历评论数据
comments.forEach(comment -> {
    //得到父评论
    Comment parent = commentMap.get(comment.getParentId());
    if (parent != null) {
        // 初始化 children 变量
        if (parent.getChildren() == null) {
            parent.setChildren(new ArrayList<>());
        }
        // 在父评论里添加回复数据
        parent.getChildren().add(comment);
    }
});
// 得到所有的一级评论
List<Comment> data = commentMap.get(0L).getChildren();
```

# 第七章——Spring安全

## Cors

> 是一个W3C标准，全称是跨域资源分享。它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制
>
> 比如说在taobao.com域名下请求baidu.com域名的服务，就被禁止了

#### 请求跨域错误信息

```java
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at $somesite
```

#### 解决方式：Filter

```java
package com.youkeda.comment;

import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

import java.time.Duration;

@Configuration
public class CorsConfig {

    @Bean
    public FilterRegistrationBean corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true);
        // 设置你要允许的网站域名，如果全允许则设为 *
        config.addAllowedOrigin("*");
        // 如果要限制 HEADER 或 METHOD 请自行更改
        config.addAllowedHeader("*");
        config.addAllowedMethod("*");
        config.setMaxAge(Duration.ofDays(5));
        source.registerCorsConfiguration("/**", config);
        FilterRegistrationBean bean = new FilterRegistrationBean(new CorsFilter(source));
        // 这个顺序很重要哦，为避免麻烦请设置在最前
        bean.setOrder(0);
        return bean;
    }
}
```

这个配置是一次性的，如果出现错误添加一下这个配置即可

