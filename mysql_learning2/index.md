# MySQL学习2


## 数据库的字段属性（重点）
### options 设置
![](/images_sql/pic3.png)
* engine 数据库引擎设置
* default character set 默认字符集
* default collation 默认核对 

### Unsigned
* 无符号整数
* 声明了该列不能为负数

### Zerofill
* 0 填充
* 不足位数 使用0来填充 int（3） 5----005

### auto Increment
* 自增，自动在上一条的基础上**+1**
* 通常设置为唯一的主键 index 并且**必须为整数类型**
* 可以自定义设置主键自增的**起始值**和**步长**

### 非空 null 和 not null
* 假设为非空，如果不赋值则会报错
* 如果不填充，默认就是null

### default value 
* 设置默认值
* sex，如果不设置，则为默认值

### 拓展-做项目代码规范（学习阶段暂时不用）
表示一个记录存在的意义  
* id 主键
* `version` 乐观锁
* is_delete 伪删除
* gmt_create 创建时间 ---- 更新操作
* gmt_update 修改时间

### 建表格式
```sql
CREATE TEBLE [IF NOT EXISTS] `表名`(
    `字段名` 列类型 [属性] [索引] [注释],
    ... 
    `字段名` 列类型 [属性] [索引] [注释], 
    `字段名` 列类型 [属性] [索引] [注释]
)[表引擎] [字符集设置] [注释]
```

* _tips_：`charset='utf8'` 必须要加，如果不加，使用mysql默认的不支持中文

### SQL 代码实战创建表
```SQL
-- 目标：创建一个school 数据库
-- 创建学生表（列， 字段） 使用SQL创建
-- 学号int；登录密码 varchar（20）；姓名；性别varchar（2）；出生日期（datetime）；家庭住址；email

-- 注意点：使用英文括号， 表的名称和字段 尽量使用 `` 括起来
-- auto_increment 自增
-- 字符串使用单引号 括起来
-- 所有的语句后加上英文逗号，最后一个不用加

CREATE TABLE if not EXISTS `student` (
	`id` int(4) NOT NULL auto_increment COMMENT '学号',
	`name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名', 
	`pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex` VARCHAR(2) NOT NULL DEFAULT '女' COMMENT '性别',
	`birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
	`address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8
```


### 简单的常用命令
* 查看创建数据库语句 `SHOW CREATE DATABASE school`
* 查看student数据表定义语句 `SHOW CREATE TABLE student`
* 显式student表的结构 `DECS student`

## MyISAM 和 InnoDB的区别
* **事务**：InnoDB 支持事务，MyISAM不支持
* **外键**：InnoDB 支持外键，MyISAM不支持
* **聚集索引**：InnoDB是聚集索引，索引和数据文件绑定在一起，MyISAM是非聚集索引
* **具体行数**：InnoDB不保存具体行数，MyISAM保存
* **全文索引**：InnoDB不支持全文索引，而MyISAM支持全文索引，查询效率上MyISAM更高
* **行级锁**：InnoDB支持，MyISAM不支持
* **表空间**：InnoDB较大，约为MyISAM的2倍

## 修改和删除表字段
### 修改
```sql
-- 修改表
-- 修改表名
ALTER TABLE student RENAME AS student1
-- 增加表的字段
ALTER TABLE teacher ADD age INT(11)
-- 修改表的字段(修改约束)
ALTER TABLE teacher MODIFY age varchar(11)
-- 修改表的字段(字段重命名)
ALTER TABLE teacher CHANGE age age1 int(10)
```
### 删除
```sql
-- 删除表
-- 删除表的字段
ALTER TABLE teacher DROP age1
-- 删除表
DROP TABLE IF EXISTS teacher
```

## MySQL数据管理


