# SSM整合学习1


## 环境要求
* IDEA
* MySQL 8 
* Tomcat 9
* Maven 3.8

## 数据库建立
```SQL
CREATE TABLE `books`(
    bookID INT(10) NOT NULL AUTO_INCREMENT comment '书id',
    `bookName` VARCHAR(100) NOT NULL comment '书名',
    `bookCounts` INT(11) NOT NULL comment '数量',
    `detail` VARCHAR(200) NOT Null comment '描述',
    KEY `bookID` (bookID)
) ENGINE=INNODB DEFAULT CHARSET=utf8


insert into `books` (bookID, bookName, bookCounts, detail) VALUES
(1, 'Java', 1, '从入门到放弃'),
(2, 'MySQL', 10, '从删库到跑路'),
(3, 'Linux', 5, '从进门进牢');
```






## maven pom.xml文件配置
* 依赖
* 静态资源导出问题
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.swagger</groupId>
    <artifactId>SSMBuild</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>16</maven.compiler.source>
        <maven.compiler.target>16</maven.compiler.target>
    </properties>

    <!--  依赖: junit 数据库驱动 连接池 servlet jsp mybatis mybatis-spring spring  -->
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.25</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.3</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2.1-b03</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.6</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.19</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.19</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.6</version>
        </dependency>

    </dependencies>


    <!--  资源过滤问题  -->
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


</project>
```

## 建立数据库配置文件database.properties
```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT
jdbc.username=root
jdbc.password=Wby785403310
```

## IDEA连接数据库
![](/images_SSMBuild/pic1.png)

## 项目结构搭建
![](/images_SSMBuild/pic2.png)

## 建立数据库对应的实体类 pojo层
* @Data @AllArgsConstructor @NoArgsConstructor 加上构造函数 getter setter
```Java
package com.swagger.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Books {
    private int bookID;
    private String bookName;
    private int bookCounts;
    private String detail;
}
```

## crud接口和对应的mapper.xml dao层
* BookMapper.java
```Java
package com.swagger.dao;

import com.swagger.pojo.Books;

import java.util.List;

public interface BookMapper {
    // 增加一本书
    int addBook(Books books);

    // 删除一本书
    int deleteBookById(int id);

    // 更新一本书
    int updateBook(Books books);

    // 查询一本书
    Books queryBookById(int id);

    // 查询全部书
    List<Books> queryAllBook();
}
```

* BookMapper.xml 尽量使用xml统一管理 SQL语句，这里不要分开用注解
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.swagger.dao.BookMapper">
    <insert id="addBook" parameterType="Books">
        insert into ssmbuild.books (bookName, bookCounts, detail)
        values (#{bookName},#{bookCounts},#{detail});
    </insert>
    
    <delete id="deleteBookById" parameterType="int">
        delete from ssmbuild.books
        where bookID = #{bookID};
    </delete>

    <update id="updateBook" parameterType="Books">
        update ssmbuild.books
        set bookName=#{bookName}, bookCounts=#{bookCounts}, detail=#{detail}
        where bookID=#{bookID};
    </update>

    <select id="queryBookById" resultType="Books">
        select * from ssmbuild.books
        where bookID = #{bookID}
    </select>

    <select id="queryAllBook" resultType="Books">
        select * from ssmbuild.books
    </select>
</mapper>

```

* 在mybatis-config.xml中注册上述mapper.xml
```xml
<mappers>
    <mapper class="com.swagger.dao.BookMapper"/>
</mappers>
```






## 建立BookService.java
```Java
package com.swagger.service;

import com.swagger.pojo.Books;
import org.apache.ibatis.annotations.Param;

import java.util.List;

public interface BookService {
    // 增加一本书
    int addBook(Books books);

    // 删除一本书
    int deleteBookById(int id);

    // 更新一本书
    int updateBook(Books books);

    // 查询一本书
    Books queryBookById(int id);

    // 查询全部书
    List<Books> queryAllBook();
}
```

## BookService.java接口的实现类







