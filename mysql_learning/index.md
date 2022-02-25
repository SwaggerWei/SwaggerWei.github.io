# Mysql学习1-基础命令行操作

## 基础结构
* 用户连接 connection 
* 数据库 database
* 表格 table 视图 view 等等  
![](/images_sql/pic2.png)

## 连接数据库
* navicat 双击数据库，创建连接
* 命令行连接
```shell
mysql -uroot -pWby785403310 
```
* 修改密码
```sql
update mysql.user set authentication_string=password('123456') where user='root' and Host='localhost';
```
* 刷新权限
```sql
flush privileges;
``` 

## 常用基础命令
* 查看所有数据库
```sql
show databases;
```
![](/images_sql/pic1.png)

* 使用数据库 use + database名称
```sql
use mysql;
```

* 查看所有table
```sql
show tables; 
```

* 查看指定表结构
```sql
describe user;
```

* 创建一个数据库
```sql
create database westos;
```

* 退出连接和注视
```sql
exit; --退出连接
--单行注释
/*
sql 多行注释
*/
```

## mysql 语言种类 CRUD 增删改查
* DDL database define language
* DML database manipulate language
* DQL database query language
* DCL database control language 

CV 程序员
API 程序员
CRUD 程序员（业务）

## 操作数据库
* 操作数据库>操作数据库中的表>操作数据库中的表数据  
* _关键字不区分大小写_
### 操作数据库 （了解）
* 创建数据库 `CREATE DATABASE IF NOT EXISTS westos;`
* 删除数据库 `DROP DATABASE IF EXISTS hello;`
* 使用数据库 ``use `school` `` (如果表明和字段名是一个保留字，应该加上反引号)
* 查看数据库 `show databases`

### 数据库的数据（列）类型
#### 1 数值
* tinyint 十分小的数据  1个字节
* smallint 较小的数据   2个字节
* mediumint 中等大小   3个字节
* **int      标准的整数 4个字节**
* bigint   较大的数据   8个字节
* float    单精度      4个字节
* **double   双精度    8个字节（精度问题）**
* decimal  字符串形式的浮点数（金融计算，一般是decimal）  

#### 2 字符串
* char     字符固定大小  0～255
* **varchar  可变字符串    0～65535**  常用变量
* tinytext 微型文本     2^8-1 
* **text     文本类型     一般用于大型文章** 

#### 3 时间日期
* date  YYYY-MM-DD    日期格式
* time   HH：mm：ss    时间格式
* **datetime  YYYY-MM-DD HH：mm：ss  最常用的时间格式**
* **timestamp  时间戳 1970.1.1 到现在的毫秒数**
* year 年份表示

#### 4 null
* 没有值，未知
* 不要使用null进行运算
