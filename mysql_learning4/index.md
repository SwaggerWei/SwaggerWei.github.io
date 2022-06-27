# MySQL学习4


## DQL 查询数据（最重点）
![](/images_sql/pic10.png)
* 以上所有的顺序不能颠倒
* 数据库查询语言（Data Query Language：数据查询语言）
* 所有的查询语言都用：select
* 简单查询，复杂查询都可以做
* **数据库中最核心的语言，使用频率最高**

### 指定查询字段
```sql

-- 查询全部的学生  SELECT 字段 FROM 表
SELECT * FROM `student`
SELECT * FROM `result`


-- 查询指定字段
SELECT `StudentNo`,`StudentName` FROM `student`

-- 给结果字段或者表取一个名字 AS
SELECT `StudentNo` AS 学号,`StudentName` AS 学生姓名 FROM `student` as s

-- 函数 concat(a, b)
SELECT CONCAT('姓名：', `StudentName`) AS 新名字 FROM `student`

```

* 列名字意义不明确，可以用as 起别名





### 去重 distinct
* 去除select 查询结果中重复的数据，只显示一条
```sql
-- 查询一下哪些同学参加了考试，成绩
SELECT * FROM result -- 查询全部成绩
-- 查询哪些同学参加了考试
SELECT `StudentNo` FROM result -- 查询哪些同学参加了考试
SELECT DISTINCT `StudentNo` FROM result -- 去重 
```

### 数据库的列（表达式）
* 数据库中的表达式： 文本、列、null、函数、计算表达式、系统变量
```sql
SELECT VERSION() -- 查看系统版本-函数
SELECT 100*3 - 1 as 计算结果 -- 计算-表达式
select @@auto_increment_increment -- 查询自增步长-变量

-- 查询学员考试成绩加1
SELECT `StudentNo`, `StudentResult` FROM result
SELECT `StudentNo`, `StudentResult` + 1 AS 提分后 FROM result
```

### where 条件子句
* 检索数据中**符合条件**的值
* 搜索的条件有一个或者多个表达式组成！结果 布尔值

![](/images_sql/pic6.png)


```sql
-- ========= where =========
SELECT `StudentNo`, `StudentResult` FROM `result`
-- 查询考试成绩 95 - 100 之间
SELECT `StudentNo`, `StudentResult` FROM `result` 
WHERE `studentresult` >= 95 AND `studentresult` <= 100

-- 模糊查询（区间） BETWEEN
SELECT `StudentNo`, `StudentResult` FROM `result` 
WHERE `studentresult` BETWEEN 95 AND 100

-- 除了9999号学生之外的同学的成绩
SELECT `StudentNo`, `StudentResult` FROM `result` 
where `StudentNo` != 9999
```

### 模糊查询：比较运算符
![](/images_sql/pic7.png)

#### like
* 通配符只能在like 里用
* 通配符 `%` 代表任意个字符
* 通配符 `_` 代表一个字符

```sql
-- ====== 模糊查询 LIKE =======
SELECT `StudentNo`, `studentname` FROM `student` 
WHERE `studentname` LIKE '刘%'; -- 刘 开头

SELECT `StudentNo`, `studentname` FROM `student` 
WHERE `studentname` LIKE '刘_'; -- 刘 后面只有一个字

SELECT `StudentNo`, `studentname` FROM `student` 
WHERE `studentname` LIKE '刘__'; -- 刘 后面两个字

SELECT `StudentNo`, `studentname` FROM `student` 
WHERE `studentname` LIKE '%佳%'; -- 名字中间带有佳的，考虑4字名字
```


#### in
```sql
-- ======== in ========
-- 查询1001，1002，1003 号学员的信息
SELECT `StudentNo`, `studentname` FROM `student` 
WHERE `StudentNo` IN (1001,1002,1003);

-- 查询在北京的学生
SELECT `StudentNo`, `studentname` FROM `student` 
where `address` in ('安徽','河南洛阳');
```

#### null / not null
```sql
-- ======== NULL / NOT NULL =========
-- 查询地址为空的学生
SELECT `StudentNo`, `studentname` FROM `student` 
where `address` = '' OR `address` IS NULL 

-- 查询有出生日期的同学 不为空
SELECT `StudentNo`, `studentname` FROM `student` 
WHERE `borndate` is NOT NULL;
```

* 注意**字符串为空和null**是不同的

### 联表查询
![](/images_sql/pic8.png)  
![](/images_sql/pic9.png)
* leftJoin
* rightJoin
* innerJoin
* 分析查询字段来自哪些表
* 确定使用那种查询

#### 代码实战
```SQL
-- =========== 联表查询 join ==========
-- 查询学号参加了考试的同学（学号，姓名，科目编号，分数）
SELECT * FROM student
SELECT * FROM result

/*
1 分析查询字段来自哪些表
2 确定使用那种查询
3 确定交叉点（这两个表中，那个数据是相同的）
判断的条件 ： 学生表中的 studentNo = 成绩表 studentNo
*/

-- inner join 
SELECT s.studentNo, studentName, SubjectNo, StudentResult
FROM student AS s 
INNER join result AS r
WHERE s.studentno = r.studentno

-- right join
SELECT s.studentNo, studentName, SubjectNo, StudentResult
FROM student s 
RIGHT JOIN result r
ON s.studentno = r.studentno

-- left join
SELECT s.studentNo, studentName, SubjectNo, StudentResult
FROM student s 
LEFT JOIN result r
ON s.studentno = r.studentno

-- 查询了参加考试的同学信息：学号，姓名，科目名，分数(三表查询)
SELECT s.studentno, studentname, subjectname, studentresult
FROM student as s
RIGHT JOIN result as r
ON r.studentno = s.studentno
INNER JOIN `subject` as sub
ON r.subjectno = sub.subjectno
```










### 自连接
* 自己的表和自己的表连接，**核心：一张表拆为两张一样的表即可**

```sql
-- 自连接 查询父子信息(把一张表看成两张一摸一样的表)
SELECT a.`categoryname` as '父栏目', b.`categoryname` as '子栏目' 
FROM `category` as a, `category` as b 
WHERE a.categoryid = b.pid
```

### 分页和排序
#### 排序：升序 ASC 降序 DESC
```SQL
-- 排序：升序 ASC 降序 DESC
SELECT s.`studentno`, `studentname`, `subjectname`, studentresult
FROM student s
INNER JOIN `result` r
on s.studentno = r.studentno
INNER JOIN `subject` sub
on r.subjectno = sub.subjectno
WHERE subjectname = '数据库结构-1'
order by studentresult DESC
```

#### 分页
* 缓解数据库压力
* 给人的体验更好，一次只显示一页
* 也有不分页的（拉不到底）----瀑布流
-- 分页：每页只显示五条数据  
-- 语法：limit 当前页，页面的大小  
-- LIMIT 0,5 第一页  
-- LIMIT 5,5 第一页  
-- LIMIT 10,5 第一页  
-- （n-1）*pageSize，pageSize  

```SQL
-- 分页：每页只显示五条数据
-- 语法：limit 当前页，页面的大小
-- LIMIT 0,5 第一页
-- LIMIT 5,5 第一页
-- LIMIT 10,5 第一页
-- （n-1）*pageSize，pageSize
SELECT s.`studentno`, `studentname`, `subjectname`, studentresult
FROM student s
INNER JOIN `result` r
on s.studentno = r.studentno
INNER JOIN `subject` sub
on r.subjectno = sub.subjectno
WHERE subjectname = '数据库结构-1'
order by studentresult DESC
LIMIT 0,5


-- ========= 子查询和嵌套插叙 ==========
-- 查询 JAVA第一学年 课程成绩排名前十的学生， 并且分数要大于80的学生信息（学号，姓名，课程名称，分数）
SELECT s.studentno, studentname, subjectname, studentresult
FROM student s
INNER JOIN result r
ON s.studentno = r.studentno
INNER JOIN `subject` sub
on sub.subjectno = r.subjectno
WHERE subjectname = 'JAVA第一学年' AND studentresult >= 80
ORDER BY studentresult DESC
LIMIT 0,10
```










### 子查询
* where （这个值是计算出来的）
* 本质：在where 语句中嵌套一个子查询语句
* where (select * from s)

```SQL
-- 查询 数据库结构-1 课程所有的考试结果（学号，科目编号，成绩），降序排列
-- 方式一：使用连接查询
SELECT studentno, r.subjectno, studentresult
FROM result r
INNER JOIN `subject` sub
ON r.subjectno = sub.subjectno
WHERE subjectname = '数据库结构-1'
ORDER BY studentresult DESC

-- 方式二：使用子查询(先执行里面，后执行外面)
SELECT studentno, subjectno, studentresult
FROM result
WHERE subjectno = (
-- 查询所有 数据库结构-1 的学生的学号
	SELECT subjectno FROM `subject` 
	WHERE subjectname = '数据库结构-1'
)
```



### MySQL函数
#### 常用函数
* ![](/images_sql/pic11.png)
* ![](/images_sql/pic12.png)
* ![](/images_sql/pic13.png)
* ![](/images_sql/pic14.png)




### 聚合函数
* ![](/images_sql/pic15.png)

```SQL
-- ========= 聚合函数 ============
-- 都能够统计表中的数据，查询有多少条记录
SELECT COUNT(studentname) FROM student; -- count (指定列)，会忽略所有的null值
SELECT COUNT(*) FROM student; -- count (*)，不会忽略所有的null值，本质计算行数，
SELECT COUNT(1) FROM student; -- count (1)，不会忽略所有的null值

-- 提取总数，平均数，最大值，最小值
SELECT SUM(studentresult) as 总和 FROM result
SELECT AVG(studentresult) as 平均分 FROM result
SELECT MAX(studentresult) as 最高分 FROM result
SELECT MIN(studentresult) as 最低分 FROM result

-- 查询不同课程的平均分，最高分，最低分，平均分大于80的
-- 核心：根据不同的课程进行分组
SELECT subjectName, AVG(StudentResult) as 平均分, MAX(StudentResult) as 最高分, MIN(StudentResult) as 最低分
FROM result r
INNER JOIN `subject` sub
on r.subjectNo = sub.subjectNo
GROUP BY r.subjectno -- 通过 学科编号来分组
HAVING 平均分 >= 80

```


## 数据库级别的 MD5码加密
* MD5 主要强调算法的复杂度和不可逆性

```SQl
-- ========= 聚合函数 ============
-- 都能够统计表中的数据，查询有多少条记录
SELECT COUNT(studentname) FROM student; -- count (指定列)，会忽略所有的null值
SELECT COUNT(*) FROM student; -- count (*)，不会忽略所有的null值，本质计算行数，
SELECT COUNT(1) FROM student; -- count (1)，不会忽略所有的null值

-- 提取总数，平均数，最大值，最小值
SELECT SUM(studentresult) as 总和 FROM result
SELECT AVG(studentresult) as 平均分 FROM result
SELECT MAX(studentresult) as 最高分 FROM result
SELECT MIN(studentresult) as 最低分 FROM result

-- 查询不同课程的平均分，最高分，最低分，平均分大于80的
-- 核心：根据不同的课程进行分组
SELECT subjectName, AVG(StudentResult) as 平均分, MAX(StudentResult) as 最高分, MIN(StudentResult) as 最低分
FROM result r
INNER JOIN `subject` sub
on r.subjectNo = sub.subjectNo
GROUP BY r.subjectno -- 通过 学科编号来分组
HAVING 平均分 >= 80



-- ========== 测试MD5码加密效果 ===========
CREATE TABLE test_MD5(
	`id` int(4) not NULL,
	`name` VARCHAR(20) not null,
	`pwd` VARCHAR(50) not null,
	PRIMARY KEY(`id`)
)ENGINE = INNODB DEFAULT CHARSET=utf8


-- 明文密码 
INSERT into test_MD5 VALUES(1, 'zhangsan', '123456'), (2, 'lisi', '123456'), (3, 'wangwu', '123456')

-- 加密
UPDATE test_MD5 set pwd = MD5(pwd) 
WHERE id = 1

-- 插入的时候加密
INSERT into test_MD5 VALUES(4, 'zhangsan', MD5('123456'))
INSERT into test_MD5 VALUES(5, 'swagger', MD5('123456'))

-- 如何校验：将用户传递进来的密码，进行MD5加密，然后比对加密后值，因为是不可逆，所以没有解密
SELECT * FROM test_MD5 WHERE `name` = 'swagger' AND pwd = MD5('123456')



```






## 事务的处理过程
* 关闭事务自动提交
* 开启一个事务
* 执行成功则提交，然后开启事务自动提交；执行失败则回滚到出事状态

```SQL
-- == 手动处理事务 ==
SET autocommit = 0 -- 关闭
-- 事务开启
START TRANSACTION -- 标记一个事务的开始，从这个之后的SQL都在同一个事物内

INSERT xx
INSERT xx

-- 提交：持久化 （成功）
COMMIT

-- 回滚：回到原来的状态 (失败)
ROLLBACK

-- 事务的结束
SET autocommit = 1 -- 开启

-- 设置一个事务的保存点（了解）
SAVEPOINT 保存点名
ROLLBACK to SAVEPOINT 保存点名 -- 回滚到保存点
RELEASE SAVEPOINT 保存点名 -- 撤销保存点
```

### 模拟场景
* 测试事务实现转账

```SQL
-- ==== 测试事务实现转账 ======

-- 转账
CREATE DATABASE shop CHARACTER set utf8 COLLATE utf8_general_ci

use shop

CREATE TABLE `account`(
	`id` INT(3) NOT NULL auto_increment,
	`name` VARCHAR(30) not null,
	`money` DECIMAL(9, 2) not NULL,
	PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8


INSERT into account(`name`, `money`)
VALUES ('A', 2000.00), ('B', 10000.00)



-- ==== 模拟转账（事务操作） ====
set autocommit = 0; -- 关闭自动提交 
START TRANSACTION -- 开启一个事务 (一组事务)
update account SET money = money - 500 WHERE `name` = 'A'; -- A减500
update account SET money = money + 500 WHERE `name` = 'B'; -- B加500

COMMIT;  -- 提交事务，事务一旦提交，就会持久化, 无法再回滚
ROLLBACK; -- 回滚

set autocommit = 1; -- 恢复自动提交（默认值）


```

## 索引
帮助MySQL 高效获取数据的数据结构，相当于一本书的目录  
* 主键索引（primary key）：唯一的标识，逐渐不可重复，**只能有一个**列作为主键
* 唯一索引（unique key）：避免重复的列出现，唯一索引可以重复，**多个列都可以标识为唯一索引**
* 常规索引（normal key）：默认的，可以用key关键字来设置
* 全文索引（fullText）：在特定的数据库下才有，MyISAM，用于快速定位数据

### 基础语法
```SQL
-- ======= 索引的使用 ========
-- 1. 在创建表的时候给字段增加索引
-- 2. 创建完毕之后，增加索引

-- 显示所有的索引信息
SHOW INDEX FROM student

-- 增加一个全文索引
ALTER TABLE school.student ADD FULLTEXT INDEX `studentname`(`studentname`);

-- EXPLAIN 分析SQL执行情况
EXPLAIN SELECT * FROM student; -- 非全文索引

EXPLAIN SELECT * FROM student WHERE MATCH(studentname) against('刘') -- 发现通过一次索引就找到了数据，

```



### 测试索引
* 小数据量用处不大，大数据量的时候十分明显
```SQL
-- ====== 测试索引 ====== 
-- 测试一百万条索引的时间
CREATE TABLE `app_user`(
	`id` BIGINT(20) UNSIGNED not null auto_increment,
	`name` VARCHAR(50) DEFAULT '' comment '用户昵称',
	`email` VARCHAR(50) not NULL COMMENT '用户邮箱',
	`phone` VARCHAR(20) DEFAULT '' COMMENT '手机号',
	`gender` TINYINT(4) UNSIGNED DEFAULT '0' COMMENT '性别（0:男， 1:女）',
	`password` VARCHAR(100) not NULL COMMENT '密码',
	`age` TINYINT(4) DEFAULT '0' COMMENT '年龄',
	`create_time` datetime DEFAULT CURRENT_TIMESTAMP,
	`update_time` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP on UPDATE CURRENT_TIMESTAMP,
	PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT charset=utf8mb4 COMMENT='app用户表'


-- 写一个函数
delimiter $$ -- 写函数之前必须要写，标志

CREATE FUNCTION mock_data()
RETURNS int DETERMINISTIC
BEGIN 
	DECLARE num int DEFAULT 1000000;
	DECLARE i int DEFAULT 0;
	
	WHILE i < num DO
		-- 插入语句
		INSERT into app_user(`name`, `email`, `phone`, `gender`, `password`, `age`)
		VALUES(
			CONCAT('用户',i),
			'123141142@qq.com',
			CONCAT('18', FLOOR(RAND()*((9999999999 - 100000000) + 100000000))),
			FLOOR(RAND()*2),
			UUID(),
			FLOOR(RAND()*100)
		);
		set i = i + 1;
	END WHILE;
	RETURN i;
END;

SELECT mock_data();

SELECT * FROM app_user WHERE `name` = '用户90130';  -- 发现使用了0.3秒多
 
EXPLAIN SELECT * FROM app_user WHERE `name` = '用户90130';

-- 常规命名方式 id_表名_字段名
-- 方式三 使用create index 。。。 on 。。。添加索引
CREATE INDEX id_app_user_name on app_user(`name`);

SELECT * FROM app_user WHERE `name` = '用户90130';  -- 创建索引之后，查询非常快，直接定位了数据的位置
 
EXPLAIN SELECT * FROM app_user WHERE `name` = '用户90130'; -- 查询的行数为1
```








### 索引原则
* 索引不是越多越好
* 不要对进行变动数据加索引
* 小数据量的表不需要索引
* 索引一般加在用来查询的字段上

### 索引的数据结构
* hash类型的索引
* B-树：InnoDB默认数据结构




## 规范的数据库设计
### 糟糕的数据库设计
* 数据冗余，浪费空间
* 数据库插入和删除都会麻烦，异常（屏蔽使用物理外键）
* 程序性能差

### 良好的数据库设计
* 节省内存空间
* 保证数据库的完整性
* 方便我们开发系统

### 软件开发过程中，关于数据库的设计
* 分析需求：分析业务和需要处理的数据库需求
* 概要设计：设计关系图 E-R 图

### 设计数据库的步骤（个人博客为例）
#### 收集信息，分析需求
* 用户表
* 分类表
* 文章表
* 评论表
* 友情链接表
* 自定义表
* 说说表

#### 概要设计，E-R图
* 标识实体，将需求落地到每个字段
* 标识实体之间的关系







## JDBC 
### 数据库驱动
![](/images_sql/pic17.png)
* 程序会通过数据库驱动和数据库进行操作

### JDBC
* SUN公司简化开发人员对于数据库的统一操作，提供了一个Java操作规范，俗称JDBC，这些规范由具体的厂商去做
* 对于开发人员，只需要掌握JDBC的接口操作即可
![](/images_sql/pic18.png)
* java.sql
* javax.sql
* mysql-connector-java.jar
* 还需要导入一个数据库驱动包

### 第一个JDBC 程序
* 创建测试数据库
```SQL
CREATE DATABASE `jdbcStudy` CHARACTER SET utf8 COLLATE utf8_general_ci;

USE `jdbcStudy`;

CREATE TABLE `users`(
 `id` INT PRIMARY KEY,
 `NAME` VARCHAR(40),
 `PASSWORD` VARCHAR(40),
 `email` VARCHAR(60),
 birthday DATE
);

INSERT INTO `users`(`id`,`NAME`,`PASSWORD`,`email`,`birthday`)
VALUES('1','zhangsan','123456','zs@sina.com','1980-12-04'),
('2','lisi','123456','lisi@sina.com','1981-12-04'),
('3','wangwu','123456','wangwu@sina.com','1979-12-04')；
```
* 导入数据库驱动 
![](/images_sql/pic19.png)
* 编写测试代码


#### 代码实战
* 加载驱动
* 用户信息和URL
* 连接成功， 数据库对象，代表数据库拿到数据库对象
* 执行SQL对象
* 执行SQL对象，去执行SQL，可能存在结果，查看返回结果
* 释放连接

```Java
package com.swagger.lesson01;

import java.sql.*;

// 第一个JDBC程序
public class JdbcFirstDemo {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1 加载驱动
        Class.forName("com.mysql.jdbc.Driver"); // 固定写法，

        // 2 用户信息和URL
        String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username = "root";
        String password = "Wby785403310";

        // 3 连接成功， 数据库对象，代表数据库拿到数据库对象
        Connection connection = DriverManager.getConnection(url, username, password);

        // 4 执行SQL对象
        Statement statement = connection.createStatement();

        // 5 执行SQL对象，去执行SQL，可能存在结果，查看返回结果
        String sql = "SELECT * FROM users;";
        ResultSet resultSet = statement.executeQuery(sql); // 执行之后返回的结果集,结果集中封装了全部的查询结果

        while (resultSet.next()){
            System.out.println("id = " + resultSet.getObject("id"));
            System.out.println("name = " + resultSet.getObject("NAME"));
            System.out.println("pwd = " + resultSet.getObject("PASSWORD"));
            System.out.println("email = " + resultSet.getObject("email"));
            System.out.println("birth = " + resultSet.getObject("birthday"));
            System.out.println("======================");
        }

        // 6 释放连接
        resultSet.close();
        statement.close();
        connection.close();


    }
}

```



