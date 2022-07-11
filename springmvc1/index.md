# SpringMVC学习1


## SpringMVC
* **SpringMVC的执行流程，怎么运行的**
* SpringMVC是一个轻量级的web应用开发框架（Model View Controller软件设计规范）

### 总体设计架构
* pojo 数据库字段对应的实体类
* dao 连接数据库
* service 服务层：对数据的处理 crud
* servlet 层：转发（usl地址不会变）、重定向
* jsp/html

### 相关概念
前端、数据传输、实体类  
* 实体类：用户名，密码，生日，爱好，。。。20个 pojo---》user
* 前段：用户，密码 vo---》UserVo（少写几个属性，节省空间）

### MVC模式理解
* Model：包含数据（dao）和行为（service）
* View：用户界面
* Controller：接受用户的请求，委托给模型进行处理（状态改变），处理完毕后吧返回的模型数据返回给是视图，由视图负责展示，**也就是说控制器做了个调度员的工作，调度方式为转发、重定向）**
* 经典的MVC就是（JSP + servlet + javabean）的模式

### MVC具体处理流程
* 用户发送请求
* Servlet接受请求数据，并调用相应的业务逻辑方法
* 业务处理完毕，返回更新之后的数据给servlet
* servlet转向JSP，由JSP来渲染页面
* 相应之后给前端更新之后的页面

### MVC职责分析
Controller控制器  
* 取得表单数据
* 调用业务逻辑
* 转向指定的页面

Model模型  
* 业务逻辑
* 保存数据状态

View
* 显示页面

## 回顾servlet
### 将maven项目变成web项目
* 创建一个普通的maven项目，导入相应的依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.swagger</groupId>
    <artifactId>SpringMVC_learning</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>spingMVC-01-servlet</module>
    </modules>

    <properties>
        <maven.compiler.source>16</maven.compiler.source>
        <maven.compiler.target>16</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.19</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/javax.servlet/servlet-api -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
            <scope>provided</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/javax.servlet.jsp/jsp-api -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2.1-b03</version>
            <scope>provided</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

    </dependencies>

</project>
```
* 右键项目，添加项目框架支持，勾选Web Application  
![](/image_SpringMVC/pic1.png)
* 子项目中导入依赖（承接父项目的依赖）
* 编写一个servlet类，用来处理用户请求
```Java
package com.swagger.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1 接受前端的参数
        String method = req.getParameter("method");
        if (method.equals("add")){
            req.getSession().setAttribute("msg", "执行了add方法");
        }
        if (method.equals("delete")){
            req.getSession().setAttribute("msg", "执行了delete方法");
        }

        // 2 调用业务层
        // 3 试图重定向（转发到页面）
        req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req, resp); // 转发的时候继续携带当前servlet的参数
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}

```
* 在 web/WEB-INF目录下新建一个jsp文件夹，文件夹下新建一个test.jsp文件
```jsp
<%--
  Created by IntelliJ IDEA.
  User: swaggerwei
  Date: 2022/7/11
  Time: 20:18
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--用以取出sevlet中的消息--%>
${msg}
</body>
</html>
```

* 在web.xml文件中注册这个servlet
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--  hello就是一个页面的名称  -->
    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>com.swagger.servlet.HelloServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

* 跳转初始页面 
```xml
<%--
  Created by IntelliJ IDEA.
  User: swaggerwei
  Date: 2022/7/11
  Time: 20:30
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<form action="/hello" method="post">
    <input type="text" name="method">
    <input type="submit">
</form>

</body>
</html>

```

* 加入tomcat（web容器）: run/debug configuration 中配置
* 点击fix，然后点击apply、ok
* 进入初始页面之后
* 测试跳转到hello，后面连接参数?method=add，则回调用servlet中的代码，得到输出为给页面显示为"执行了add方法"，delete同理

### MVC框架要做的事情
* 将url请求映射到java类或者java类的方法之中
* 封装用户提交的数据
* 处理请求--调用相关的业务处理--封装相应数据
* 将相应数据进行渲染jsp/html视图层数据


## 初识SpringMVC
* 是一个Java实现的MVC的web框架
* 底层还是servlet

### 特点
* 轻量级，简单易学
* 与Spring 的兼容性好，所有的对象都以bean的形式呈现
* 基于请求相应和MVC框架
* 约定优于配置（约定好的东西不要改）
* 功能强大（RESTful，数据验证，格式化，本地化，主题等）
* 简洁灵活

Spring的web框架围绕**DispatcherServlet（调度servlet）**设计  ，以请求为驱动，围绕一个**中心Servlet分派请求**以及提供其他功能

###补充知识点
#### servlet 的两种方式（中转，调度）
* 转发：直接将结果，附件找到的页面，渲染好给用户
* 重定向：告诉用户结果在哪个页面中，给一个重定向的页面，打开之后包含渲染好的结果

#### doGet中的 request 和 response 
* request ：请求
* Response：响应，可以转发

### SpringMVC原理
![](/image_SpringMVC/pic2.png)















