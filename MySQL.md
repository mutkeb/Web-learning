# 第一章——为什么选择MySQL

## 数据库的分类

* 层次式数据库
* 网络式数据库
* 关系型数据库

# 第二章——CRUD

## 创建表格

```SQL
CREATE TABLE `user`(
`id` INT(10)NOT NULL,
`mobile` VARCHAR(11) NOT NULL,
`nickname` VARCHAR(40) NOT NULL,
`gmt_created` datetime ,
`gmt_modified` datetime NOT NULL,
PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

#### 设置存储引擎和编码格式

```SQL
ENGINE=InnoDB DEFAULT CHARSET=utf8
```

#### 删除表格

```SQL
drop table table_name;
```

有时候我们这么写

```SQL
DROP TABLE IF EXISTS table_name;
```

只有存在才会删除

自增主键

```SQL
`id` INT UNSIGNED AUTO_INCREMENT
```

## 插入语句

### 语法

```SQL
INSERT INTO table_name(field1,field2,...fieldN)
VALUES
(value1,value2,...valueN);
```

### 简化

```SQL
INSERT INTO table_name
VALUES
(value1,value2,...valueN);
```

1. 如果主键设置为自增，则可以不插入主键和对应的数据
2. 如果插入的是所有的字段，可以省略字段名，直接插入值，但是类型必须全部一致

### 批量插入数据

```SQL
INSERT INTO table_name
VALUES
(value1,value2,...valueN),
(value1,value2,...valueN);
```

## 查询语句

```SQL
SELECT
  *
FROM
  timi_adc
where
  win_rate > 0.5;
```

### Limit子句

### 查询x-y行

```SQL
SELECT
  *
FROM
  timi_adc
LIMIT
  5, 6;
```

> 该语句代表查询6-11行

### 查询第x行

```SQL
SELECT
  *
FROM
  timi_adc
LIMIT
  4, 1;
```

> 该语句查询第5行

### 查询所有数据库

show databases

### 查看表的结构

desc 表名

### 查看所有表

show tables

### 返回整数类型

```xml
<select id="selectCounts" resultType="java.lang.Integer">
    select count(*) from order;
</select>
```

## 排序

### 语法

```SQL
SELECT
  *
FROM
  timi_adc
ORDER BY
  win_rate;
```

### 降序排序

```SQL
SELECT
  *
FROM
  timi_adc
ORDER BY
  win_rate DESC;
```

### 与其他子句连用

```SQL
SELECT
  *
FROM
  timi_adc
ORDER BY
  win_rate DESC
LIMIT
  3;
```

## 更新/删除

```SQL
UPDATE
  timi_adc
SET
  ban_rate = 0.01
WHERE
  hero_name = '艾琳';
```

```SQL
delete from `user` where id<20;
```

### 删除所有数据

```SQL
delete from user;
```

# 第三章——查询优化

## like查询

```SQL
SELECT
  *
FROM
  timi_adc
WHERE
  hero_name LIKE '%孙%';
```

> %表示任意字符，_表示一个字符

## AND&OR

```SQL
SELECT
  *
FROM
  timi_adc
WHERE
  win_rate > 0.5
  AND win_rate < 0.51
  OR win_rate < 0.47;
```

## IN/NOT IN

```SQL
SELECT
  *
FROM
  timi_adc
WHERE
  fever IN ('T0', 'T3');
```

```SELECT
SELECT
  *
FROM
  timi_adc
WHERE
  fever NOT IN ('T0');
```

```SQL
SELECT
  *
FROM
  timi_adc
WHERE
  hero_name NOT LIKE '%孙%';
```

## NULL值的处理

```SQL
SELECT field_name1,field_name2
FROM table_name
WHERE field_name2 IS NOT NULL/IS NULL;
```

# 第四章——MySQL字符串处理

## CONCAT函数

* CONCAT函数可以拼接列名，也可以拼接字符串
* 在使用CAT函数的时候也可以同时查询其他的列
* CONCAT函数之间用英文  `,` 分隔

```SQL
SELECT
  id,
  concat(hero_name, '的胜率是', win_rate)
FROM
  timi_adc;
```

## TRIM函数

> 用于去掉我们不需要的字符（空格等）

```SQL
SELECT
  trim(hero_name),
  trim(fever)
FROM
  timi_adc
WHERE
  id = 20;
```

trim()函数可以去掉查询结果中的空格，但是不会修改原数据，修改原数据需要配合UPDATE/DELETE语句使用

### 语法拓展

trim()函数也可以精准的去掉前面或者后面的空格，或者其他的字符，语法如下：

```SQL
TRIM( BOTH|LEADING|TRAILING removed_str FROM str);
```

* LEADING只除去前面的空格，或者加上TRAILING只除去后面的空格，如果都不加，就是默认BOTH
* TRIM函数可以删除指定的字符串内容，如果不加，则默认删除空格

```SQL
SELECT
  TRIM(
    TRAILING 'Q'
    FROM
      fever
  )
FROM
  timi_adc
WHERE
  id = 21;
```

删除fever尾部的Q

## REPLACE函数

> 用于去掉字符串中间的值，但一般实用性不高，可以用UPDATE语句修改

它可以把找到的某个字符串替换成另一个字符串

```SQL
UPDATE table_name 
SET colunm_name = 
REPLACE(column_name,string_find,string_to_replace) 
WHERE conditions;
```

# 第五章——MySQL关联查询

## 左连接

```SQL
SELECT
  *
FROM
  TableA LEFT  JOIN
  TableB
  ON condition;
```

即使右表没有匹配的数据，右表会以NULL的形式匹配数据

```SQL
SELECT
  *
FROM
  ykd_teacher
  LEFT JOIN ykd_course ON ykd_teacher.id = ykd_course.teacher_id;
```

让相应的字段匹配

### 表结构设计规范

![image-20220829113549262](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220829113549262.png)

## 右连接

```SQL
SELECT
  *
FROM
  ykd_teacher
  RIGHT JOIN ykd_course ON ykd_teacher.id = ykd_course.teacher_id;
```

## 多表关联查询

```SQL
SELECT
  *
FROM
  TableA 
  LEFT JOIN TableB ON conditionA
  LEFT JOIN TableC ON conditionB;
```

> 有时候我们会对三张表以上进行关联查询，我们一般会选中一张表作为主表

比如：

```SQL
SELECT
  *
FROM
  ykd_course
  LEFT JOIN ykd_student ON ykd_student.course_id = ykd_course.id
  LEFT JOIN ykd_teacher on ykd_course.teacher_id = ykd_teacher.id;
```

## 内连接

![](D:\编程日志\img\A&B.jpg)

```SQL
SELECT
  *
FROM
  Table_A
  INNER JOIN Table_B
ON
  Table_A.id = Table_B.student_id;
```

外连接示意图

![](D:\编程日志\img\v2-e492481e964541f2764fa5b529790bae_1440w.jpg)

想要得到如下的图：

![](D:\编程日志\img\v2-dd0093c24ed334d24de6498247d22e6a_1440w.jpg)

```SQL
SELECT
  *
FROM
  Table_A
  LEFT JOIN Table_B ON Table_A.id = Table_B.student_id
WHERE
  Table_B.student_id IS NULL;
```