# MySQL学习3


## 外键（了解即可）
* 一半不这样使用
* 定义外键key
* 要去外键添加约束，执行引用
* 被引用的表（主表）不能直接删除，必须先删除引用表（从表）

### 代码实战 (方式1)
* 定义一个年级表
```SQL
CREATE TABLE `grade` (
	`gradeid` INT(10) NOT NULL auto_increment COMMENT '年级id',
	`gradename` VARCHAR(50) not null COMMENT '年纪名称',
	PRIMARY KEY (`gradeid`)
)ENGINE=INNODB DEFAULT CHARSET=utf8
```
* 定一个一个学生表，并将其中的gradeid设置为外键，绑定到年级表
```SQL
-- 学生表的gradeid 字段 要去引用年级表的gradeid
-- 定义外键key
-- 要去外键添加约束，执行引用
CREATE TABLE if not EXISTS `student` (
	`id` int(4) NOT NULL auto_increment COMMENT '学号',
	`name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名', 
	`pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex` VARCHAR(2) NOT NULL DEFAULT '女' COMMENT '性别',
	`gradeid` INT(10) NOT NULL COMMENT '年级id',
	`birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
	`address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (`id`),
	
	-- 添加外键
	KEY `FK_gradeid` (`gradeid`),
	CONSTRAINT `FK_gradeid` FOREIGN KEY (`gradeid`) REFERENCES `grade`(gradeid)
)ENGINE=INNODB DEFAULT CHARSET=utf8
```

* 效果 
![](/images_sql/pic4.png)








### 通过alter添加外键关系
```SQL
-- 创建表的时候没有外键关系
CREATE TABLE if not EXISTS `student` (
	`id` int(4) NOT NULL auto_increment COMMENT '学号',
	`name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名', 
	`pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex` VARCHAR(2) NOT NULL DEFAULT '女' COMMENT '性别',
	`gradeid` INT(10) NOT NULL COMMENT '年级id',
	`birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
	`address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

-- 通过alter添加外键关系
ALTER TABLE `student` ADD CONSTRAINT `FK_gradeid` FOREIGN KEY(gradeid) REFERENCES `grade`(`gradeid`)

```


### tips
* 上述添加外键操作都为物理外键，为数据库级别的外键，不推荐使用，删除表的时候会造成麻烦（避免表过多造成困扰）
* 数据库均为单纯的表，只用来存数据，只有行和列
* 想使用外键，之后使用程序级别来实现
* 阿里规范：不得使用外键和级联，一切外键概念必须在应用层解决

## DML语言 （全部记住）
* 数据库的意义：数据存储，数据管理

### DML语言
* insert
* update
* delete

### 插入 insert
* 如果主键自增，则可以省略插入主键
* 一半写插入语句，一定要数据和字段一一对应

#### 代码实战
```SQL
-- 插入语句（添加）
-- 如果主键自增，则可以省略插入主键
-- 一半写插入语句，一定要数据和字段一一对应
insert into `grade` (`gradename`) VALUES ('大四')

-- 一个字段插入多个记录
insert into `grade` (`gradename`) VALUES ('大二'),('大一')
-- 多个字段插入一个数据
insert into `student` (`name`, `gradeid`) VALUES ('swagger', 1)
insert into `student` (`name`, `gradeid`, `pwd`, `sex`) VALUES ('Boyan', 1, '1234567', '男')
-- 多个字段插入多个数据
insert into `student` (`name`, `gradeid`, `pwd`, `sex`) 
VALUES ('Boyan', 1, '1234567', '男'),('Xinyi', 2, '12345678', '女')
```

### 修改 update
* 需要带where 条件
* 条件，筛选条件，如果不指定条件，会改动所有的表
* 多个修改的字段之间使用英文逗号隔开

#### 条件判断 where
![](/images_sql/pic5.png)

#### 代码实战
```SQL

-- 修改学员名字, 带了where 条件，
update `student` set `name`='swaggerwei' where `id` = 1;
-- 如果不指定条件，会改动表中所有的记录
update `student` set `name`='swaggerwei'

-- 修改一条记录的多个字段
update `student` set `name`='swaggerwei', `email`='785403@qq.com' where `id` = 1;

```


### 删除 delete/truncate
#### delete
```SQL
-- DELETE 命令
-- 删除数据（最好避免这样写，会全部删除,有其他专门的方法）
DELETE FROM `student` 

-- 删除指定数据
DELETE FROM `student` WHERE id = 1;
```

#### truncate
```SQL
-- TRUNCATE 命令
-- 完全清空一个数据库表，表的结构和索引约束不会变
-- 清空strudent表
TRUNCATE `student` 
```

#### delete/truncate 异同
* 相同点：都能删除数据，都不会删除表的结构
* 不同点：truncate 清零自增列，计数器归零；

```SQL
-- 测试truncate和delete的区别
CREATE TABLE `test_delete`(
	`id` INT(4) not null AUTO_INCREMENT,
	`name` VARCHAR(10) not null,
	PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO `test_delete`(`name`) VALUES('1'),('2'),('3')

DELETE FROM `test_delete`
TRUNCATE TABLE `test_delete` 
```
**删除之后如果重启数据库（InnoDB）**

* delete 自增列也会清零（存在内存当中，会丢失）
* MyISAM 继续从上一个自增量开始（存在文件当中，不会丢失）

