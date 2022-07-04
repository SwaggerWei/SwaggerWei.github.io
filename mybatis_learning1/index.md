# MyBatis学习1


## 什么是 MyBatis
* MyBatis 是一款优秀的持久层框架
* 它支持自定义 SQL、存储过程以及高级映射。
* MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
* MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

## 如何获得MyBatis
* maven仓库
```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>

```
* [Github：release 地址](https://github.com/mybatis/mybatis-3/releases/tag/mybatis-3.5.2)
* [中文文档](https://mybatis.org/mybatis-3/zh/index.html)

## 第一个MyBatis 程序
* 思路：搭建环境 -》 导入MyBatis -》编写代码 -》测试

### 搭建环境
#### 搭建数据库
```SQL
create database `mybatis`;

use `mybatis`;

create table `user`(
    `id` int(20) not null,
    `name` varchar(30) default null,
    `pwd` varchar(30) default null,
    primary key (`id`)
)engine = InnoDB default charset = utf8;


insert into `user`(`id`, `name`, `pwd`) values
(1, 'swagger', '123456'),
(2, 'boyan', '123456'),
(3, 'lisi', '123456');
```

#### 新建一个maven项目
* 新建一个普通的maven项目
* 删除src目录（父工程）
* 导入maven依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--  父工程  -->

    <groupId>org.example</groupId>
    <artifactId>MyBatis_learning</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>16</maven.compiler.source>
        <maven.compiler.target>16</maven.compiler.target>
    </properties>

    <!-- 导入依赖 -->
    <dependencies>
        <!--mysql 驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.45</version>
        </dependency>

        <!--mybatis-->
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
        </dependency>

        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>


    </dependencies>
    
</project>
```


#### 创建一个模块，编写配置文件和工具类
* 编写MyBatis核心配置文件
```xml
<configuration>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">

                <!--数据库相关配置-->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT"/>
                <property name="username" value="root"/>
                <property name="password" value="Wby785403310"/>
            </dataSource>
        </environment>
    </environments>

</configuration>


```

* 编写MyBatis工具类
```Java
package com.swagger.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;


// sqlSessionFactory --> sqlSession
public class MyBatisUtils {

    private static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            // 设计模式：工厂模式
            // 1 获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);


        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    // 2 从中获取SqlSession 实例
    // 既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }

}

 
```



#### 编写代码
* 实体类：和数据库的字段严格对应
```Java
package com.swagger.pojo;

// 和数据库对应的实体类
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setId(int id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}

```

* Dao接口
```Java
package com.swagger.dao;

import com.swagger.pojo.User;

import java.util.List;

// Data Access object
// 等价于mapper
public interface UserDao {
    List<User> getUserList();
}

```

* 接口实现类由原来的UserDaoImpl转化为一个`mapper.xml`配置文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.swagger.dao.UserDao">
    <!-- select 查询语句
    id 相当于UserDao中提取操作接口的名字-->
    <select id="getUserList" resultType="com.swagger.pojo.User">
        select * from mybatis.user
    </select>
</mapper>
```

#### 测试
**tip1**：org.apache.ibatis.binding.BindingException: Type interface com.swagger.dao.UserDao **is not known to the MapperRegistry.**  需要在Mybatis的核心配置文件中注册mappers
**tip2**：maven资源过滤问题，maven默认只会读取resources目录下的配置文件，如果想要导入其他目录下的需要在maven的配置文件`pom.xml`中加入相应的代码  
```xml
<!--在build中配置配置resources，来防止我们资源导出失败问题-->
    <build>
        <resources>

            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>

            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>

        </resources>
    </build>
```
* junit 测试
```Java
package com.swagger.dao;

import com.swagger.pojo.User;
import com.swagger.utils.MyBatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {

    @Test
    public void test(){

        // 1 获得sqlSession对象
        SqlSession sqlSession = MyBatisUtils.getSqlSession();

        // 2 方式1： 执行sql
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> userList = userDao.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }

        // 关闭sqlSession
        sqlSession.close();

    }

}

```

* 可能出现的问题：配置文件mapper没有注册；绑定接口错误；方法名不对；返回类型不对；Maven导出资源问题






