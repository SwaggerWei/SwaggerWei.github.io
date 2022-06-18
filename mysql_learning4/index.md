# MySQL学习4


## DQL 查询数据（最重点）
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









