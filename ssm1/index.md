# SSM整合学习1


## 环境要求
* IDEA
* MySQL 8 
* Tomcat 9
* Maven 3.8

## MyBatis 层

### 数据库建立
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






### maven pom.xml文件配置
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

### 建立数据库配置文件database.properties
```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT
jdbc.username=root
jdbc.password=Wby785403310
```

### IDEA连接数据库
![](/images_SSMBuild/pic1.png)

### 项目结构搭建
![](/images_SSMBuild/pic2.png)

### 建立数据库对应的实体类 pojo层
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

### crud接口和对应的mapper.xml dao层
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






### 建立BookService.java
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

### BookService.java接口的实现类
* service层调用dao层就可以了，组合dao层
* service层可以在调用dao层之前加入其他的操作形成事务
* 加入的操作可以直接用Spring aop横切进去就行
```Java
package com.swagger.service;

import com.swagger.dao.BookMapper;
import com.swagger.pojo.Books;

import java.util.List;

public class BookServiceImpl implements BookService{

    // service层调用dao层就可以了，组合dao层
    // service层可以在调用dao层之前加入其他的操作形成事务，
    // 加入的操作可以直接用Spring aop横切进去就行
    private BookMapper bookMapper;
    public void setBookMapper(BookMapper bookMapper) {
        // 如果要加入其他的操作，直接用aop横切进去就行
        // 真实项目的其他操作可能很复杂，
        this.bookMapper = bookMapper;
    }

    @Override
    public int addBook(Books books) {
        return bookMapper.addBook(books);
    }

    @Override
    public int deleteBookById(int id) {
        return bookMapper.deleteBookById(id);
    }

    @Override
    public int updateBook(Books books) {
        return bookMapper.updateBook(books);
    }

    @Override
    public Books queryBookById(int id) {
        return bookMapper.queryBookById(id);
    }

    @Override
    public List<Books> queryAllBook() {
        return bookMapper.queryAllBook();
    }
}
```


## Spring 层
### spring-dao.xml 托管dao层所用到的bean
* 1 关联数据库配置文件database.properties
* 2 连接池配置
* 3 sqlSessionFactory
* 4 配置dao接口扫描包，动态实现了dao接口可以注入到spring容器中（反射）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
    <!--  1 关联数据库配置文件database.properties  -->
    <context:property-placeholder location="classpath:database.properties"/>

    <!--  2 连接池
            dbcp 半自动化操作 不能自动连接
            c3p0 自动化操作（自动化的加载配置文件，自动设置到对象当中）
            druid 阿里的
            hikari spring 默认集成
            -->
    <!--使用的是c3p0的数据库连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">

        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}}"/>
        <property name="user" value="${jdbc.username}}"/>
        <property name="password" value="${jdbc.password}"/>

        <!--  c3p0的独有属性  -->
        <!--最大最小连接数量-->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!--  关闭连接之后不自动commit  -->
        <property name="autoCommitOnClose" value="false"/>
        <!--获取连接超时时间-->
        <property name="checkoutTimeout" value="10000"/>
        <!--当获取连接失败重试次数-->
        <property name="acquireRetryAttempts" value="2"/>

    </bean>

    <!--  3 sqlSessionFactory  -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--  绑定mybatis配置文件  -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>

    <!--  4 配置dao接口扫描包，动态实现了dao接口可以注入到spring容器中（反射）  -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--注入sqlSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>

        <!--  扫描要扫描的包  -->
        <property name="basePackage" value="com.swagger.dao"/>
    </bean>

</beans>
```


### spring-service.xml 托管service层所有的bean
* 1 扫描service下的包
* 2 将所有的service业务类，注入到spring 或者使用注解@Service和@Autowired
* 3 声明式事务配置
* 4 aop事务的支持 （暂时没有使用）
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!--  1 扫描service下的包  -->
    <context:component-scan base-package="com.swagger.service"/>

    <!--  2 将所有的service业务类，注入到spring 或者使用注解@Service和@Autowired  -->
    <bean id="BookServiceImpl" class="com.swagger.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>

    <!--  3 声明式事务配置  -->
    <bean id="TransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--  4 aop事务的支持-->


</beans>
```

### applicationContext.xml 整合dao和service
* 整合dao和service
* 实际上不写这两个import也可以，因为idea可以自动整合
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--整合dao和service，实际上不写这个也可以，因为idea可以自动整合-->
    <import resource="classpath:spring-dao.xml"/>
    <import resource="classpath:spring-service.xml"/>


</beans>
```








## Spring MVC 层
* 增加web支持 （add framework support）
* web.xml文件添加servlet支持
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">


    <!--  DispatcherServlet  -->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <!--  乱码过滤  -->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--  Session  -->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
</web-app>
```

* applicationContext.xml中加入spring-mvc的文件支持 `<import resource="classpath:spring-mvc.xml"/>`

### spring-mvc.xml 文件配置
* 注解驱动
* 静态资源过滤
* 扫描包：controller
* 视图解析器
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!--  1 注解驱动  -->
    <mvc:annotation-driven/>

    <!--  2 静态资源过滤  -->
    <mvc:default-servlet-handler/>

    <!--  3 扫描包：controller  -->
    <context:component-scan base-package="com.swagger.controller"/>

    <!--  4 视图解析器  -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>


</beans>
```


## 查询所有书籍功能（加业务）
* tips：如果使用tomcat部署网页无法打开，在project structure中添加lib依赖包，详情参考SpringMVC1.md笔记

### 首页页面优化 index.jsp
```jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>首页</title>

    <style>
      a{
        text-decoration: none;
        color: black;
        font-size: 18px;
      }

      h3{
        width: 180px;
        height: 38px;
        margin: 100px auto;
        text-align: center;
        line-height: 38px;
        background: deepskyblue;
        border-radius: 5px;
      }
    </style>

  </head>
  <body>
  <h3>
    <a href="${pageContext.request.contextPath}/book/allBook">进入书籍页面</a>
  </h3>
  </body>
</html>

```

### 查询页面优化 allBook.jsp
```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>书籍展示页面</title>

    <%--  使用bootStrap美化界面  --%>
    <%--  导入在线cdn  --%>
    <link href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">


</head>
<body>

<div class="container">
    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>书籍列表 ----- 显示所有书籍</small>
                </h1>
            </div>
        </div>
    </div>
</div>

<div class="row clearfix">
    <div class="col-md-12 column">
        <table class="table table-hover table-striped">
            <thead>
                <tr>
                    <th>书籍编号</th>
                    <th>书籍名称</th>
                    <th>书籍数量</th>
                    <th>书籍详情</th>
                </tr>
            </thead>

            <%--      书籍是从数据库查出来，封装在了model中的list，遍历出来 foreach      --%>
            <tbody>
                <c:forEach var="book" items="${list}">
                    <tr>
                        <td>${book.bookID}</td>
                        <td>${book.bookName}</td>
                        <td>${book.bookCounts}</td>
                        <td>${book.detail}</td>
                    </tr>
                </c:forEach>
            </tbody>
        </table>
    </div>
</div>


</body>
</html>

```

### controller层
```Java
package com.swagger.controller;

import com.swagger.pojo.Books;
import com.swagger.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;

@Controller
@RequestMapping("/book")
public class bookController {
    // controller层调用service层
    @Autowired
    @Qualifier("BookServiceImpl")
    private BookService bookService;

    // 查询全部的书籍，并且返回到一个书籍展示页面
    @RequestMapping("/allBook")
    public String list(Model model){

        List<Books> list = bookService.queryAllBook();

        // 结果封装在model当中
        model.addAttribute("list", list);

        // 返回一个视图
        return "allBook";
    }
}
```

### 测试效果
![](/images_SSMBuild/pic3.png)
![](/images_SSMBuild/pic4.png)











## 增加书籍功能
### allBook.jsp增加 添加书籍 按钮
```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>书籍展示页面</title>

    <%--  使用bootStrap美化界面  --%>
    <%--  导入在线cdn  --%>
    <link href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">


</head>
<body>

<div class="container">
    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>书籍列表 ----- 显示所有书籍</small>
                </h1>
            </div>
        </div>
    </div>

    <div class="row">
        <div class="col-md-4 column">
            <%--      toAddBook      --%>
            <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增书籍</a>
        </div>
    </div>

    <div class="row clearfix">
        <div class="col-md-12 column">
            <table class="table table-hover table-striped">
                <thead>
                <tr>
                    <th>书籍编号</th>
                    <th>书籍名称</th>
                    <th>书籍数量</th>
                    <th>书籍详情</th>
                </tr>
                </thead>

                <%--      书籍是从数据库查出来，封装在了model中的list，遍历出来 foreach      --%>
                <tbody>
                <c:forEach var="book" items="${list}">
                    <tr>
                        <td>${book.bookID}</td>
                        <td>${book.bookName}</td>
                        <td>${book.bookCounts}</td>
                        <td>${book.detail}</td>
                    </tr>
                </c:forEach>
                </tbody>
            </table>
        </div>
    </div>

</div>

</body>
</html>
```


### addBook.jsp 填写书籍信息（text），提交按钮（submit）
```jsp
<%--
  Created by IntelliJ IDEA.
  User: swaggerwei
  Date: 2022/7/21
  Time: 13:39
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>

    <%--  使用bootStrap美化界面  --%>
    <%--  导入在线cdn  --%>
    <link href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">

</head>
<body>

<div class="container">
    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>书籍列表 ----- 新增书籍</small>
                </h1>
            </div>
        </div>
    </div>

    <form action="${pageContext.request.contextPath}/book/addBook" method="post">
        <div class="form-group">
            <label>书籍名称</label>
            <input type="text" name="bookName" class="form-control" required>
        </div>
        <div class="form-group">
            <label>书籍数量</label>
            <input type="text" name="bookCounts" class="form-control" required>
        </div>
        <div class="form-group">
            <label>书籍描述</label>
            <input type="text" name="detail" class="form-control" required>
        </div>
        <div class="form-group">
            <input type="submit" class="form-control" value="添加" required>
        </div>
    </form>





</div>


</body>
</html>
```


### bookController.java 
* 增加书籍按钮跳转
* 从addBook.jsp中获取填写的数据
* 调用service层，增加书籍
* 重定向到查询所有书籍页面

```Java
package com.swagger.controller;

import com.swagger.pojo.Books;
import com.swagger.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;

@Controller
@RequestMapping("/book")
public class bookController {
    // controller层调用service层
    @Autowired
    @Qualifier("BookServiceImpl")
    private BookService bookService;

    // 查询全部的书籍，并且返回到一个书籍展示页面
    @RequestMapping("/allBook")
    public String list(Model model){

        List<Books> list = bookService.queryAllBook();

        // 结果封装在model当中
        model.addAttribute("list", list);

        // 返回一个视图
        return "allBook";
    }

    @RequestMapping("/toAddBook")
    // 跳转到增加书籍页面
    public String toAddPaper(){
        return "addBook";
    }

    // 添加书籍的请求
    @RequestMapping("/addBook")
    public String addBook(Books books){
        System.out.println("addBook:" + books);
        bookService.addBook(books);

        // 添加完之后应该是重定向
        return "redirect:/book/allBook"; // 重定向到@RequestMapping("/allBook")
    }


}
```




## 修改数据功能
### allBook.jsp增加 修改 按钮
```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>书籍展示页面</title>

    <%--  使用bootStrap美化界面  --%>
    <%--  导入在线cdn  --%>
    <link href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">


</head>
<body>

<div class="container">
    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>书籍列表 ----- 显示所有书籍</small>
                </h1>
            </div>
        </div>
    </div>

    <div class="row">
        <div class="col-md-4 column">
            <%--      toAddBook      --%>
            <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增书籍</a>
        </div>
    </div>

    <div class="row clearfix">
        <div class="col-md-12 column">
            <table class="table table-hover table-striped">
                <thead>
                <tr>
                    <th>书籍编号</th>
                    <th>书籍名称</th>
                    <th>书籍数量</th>
                    <th>书籍详情</th>
                    <th>操作</th>
                </tr>
                </thead>

                <%--      书籍是从数据库查出来，封装在了model中的list，遍历出来 foreach      --%>
                <tbody>
                <c:forEach var="book" items="${list}">
                    <tr>
                        <td>${book.bookID}</td>
                        <td>${book.bookName}</td>
                        <td>${book.bookCounts}</td>
                        <td>${book.detail}</td>
                        <td>
                            <a href="${pageContext.request.contextPath}/book/toUpdate?id=${book.bookID}">修改</a>
                            &nbsp; | &nbsp;
                            <a href="#">删除</a>
                        </td>
                    </tr>
                </c:forEach>
                </tbody>
            </table>
        </div>
    </div>

</div>

</body>
</html>
```

### updateBook.jsp 增加书籍信息（text），修改按钮（submit）
* 注意修改数据的时候sql语句需要根据id来修改
* jsp文件中应该传入一个隐藏域为bookID
```jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>修改书籍</title>

    <%--  使用bootStrap美化界面  --%>
    <%--  导入在线cdn  --%>
    <link href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">

</head>
<body>

<div class="container">
    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>书籍列表 ----- 修改书籍</small>
                </h1>
            </div>
        </div>
    </div>

    <form action="${pageContext.request.contextPath}/book/updateBook" method="post">
        <%--    前端传入隐藏域 bookID    --%>
        <input type="hidden" name="bookID" value="${QBooks.bookID}">
        <div class="form-group">
            <label>书籍名称</label>
            <input type="text" name="bookName" class="form-control" value="${QBooks.bookName}" required>
        </div>
        <div class="form-group">
            <label>书籍数量</label>
            <input type="text" name="bookCounts" class="form-control" value="${QBooks.bookCounts}" required>
        </div>
        <div class="form-group">
            <label>书籍描述</label>
            <input type="text" name="detail" class="form-control" value="${QBooks.detail}" required>
        </div>
        <div class="form-group">
            <input type="submit" class="form-control" value="修改" required>
        </div>
    </form>





</div>


</body>
</html>
```

### bookController.java
* 跳转到修改页面
* 修改页面修改信息
* 重定向到所有书籍页面
```Java
package com.swagger.controller;

import com.swagger.pojo.Books;
import com.swagger.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;

@Controller
@RequestMapping("/book")
public class bookController {
    // controller层调用service层
    @Autowired
    @Qualifier("BookServiceImpl")
    private BookService bookService;

    // 查询全部的书籍，并且返回到一个书籍展示页面
    @RequestMapping("/allBook")
    public String list(Model model){

        List<Books> list = bookService.queryAllBook();

        // 结果封装在model当中
        model.addAttribute("list", list);

        // 返回一个视图
        return "allBook";
    }

    @RequestMapping("/toAddBook")
    // 跳转到增加书籍页面
    public String toAddPaper(){
        return "addBook";
    }

    // 添加书籍的请求
    @RequestMapping("/addBook")
    public String addBook(Books books){
        System.out.println("addBook:" + books);
        bookService.addBook(books);

        // 添加完之后重定向到allBook页面
        return "redirect:/book/allBook"; // 重定向到@RequestMapping("/allBook"), 进入这个页面会重新查询一次所有的书
    }


    // 跳转到修改页面
    @RequestMapping("/toUpdate")
    public String toUpdatePaper(int id, Model model){
        Books books = bookService.queryBookById(id);
        model.addAttribute("QBooks", books);
        return "updateBook";
    }

    // 修改书籍请求
    @RequestMapping("/updateBook")
    public String updateBook(Books books){
        System.out.println("updateBook:" + books);
        bookService.updateBook(books);
        return "redirect:/book/allBook";
    }


}
```





## 删除书籍功能
* 根据id删除数据监听`<a href="${pageContext.request.contextPath}/book/deleteBook/${book.bookID}">删除</a>`
* controller层删除书籍
```Java
// 删除书籍
@RequestMapping("/deleteBook/{bookID}")
public String deleteBook(@PathVariable("bookID") int id){
    bookService.deleteBookById(id);
    return "redirect:/book/allBook";
}
```







