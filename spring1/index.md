# Spring学习1


## Spring 简介
* **Spring是一个轻量级控制反转（IoC）和面向（AOP）容器框架**
* 以inteface21框架为基础，经过重新设计，才有的Spring1.0版本
* 使得现有技术更加容易使用，本身是一个大杂烩
* SSH框架：Struct2 + Spring + Hibernate
* SSM框架：SpringMVC + Spring + MyBatis

[官网](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/overview.html#overview)  
[下载地址](https://repo.spring.io/ui/native/release/org/springframework/spring)   
[github地址](https://github.com/spring-projects/spring-framework/releases)  

* maven xml配置文件，只用配置Spring Web MVC 和 spring-jdbc即可
```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>
```

### 优点
* 开源免费
* 轻量级、非入侵式（不改变代码的原始内容）的框架
* **控制反转（IoC），面向切面编程（AOP）**
* 支持事务的处理，对框架整合的支持

### Spring 的组成及扩展 
![](/images_Spring/pic1.png)
* Spring Boot：一个快速开发的脚手架；基于Spring Boot可以快速开发单个微服务；约定大于配置
* Spring Cloud：SpringCloud是基于SpringBoot实现的；
* 现在大多数公司都在使用SpringBoot进行快速开发，**学习SpringBoot的前提是掌握Spring和SpringMVC**
* 弊端：配置十分繁琐

## IOC理论推导
### 控制反转思想，主动权交给用户
* 在业务当中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改原代码！如果程序量十分大，修改一次的成本昂贵。
* 使用set接口实现的好处是，程序员不再是主导者，**以前的Dao层和业务层只是增量，都不需要再修改，决定权在用户手中**，只是如何使用的问题。
* 耦合性降低，更加专注于业务的实现，这个就是IOC的原型
![](/images_Spring/pic2.png)

  
* UserDao接口
* UserDaoImpl实现类
* UserService业务接口
* UserServiceImpl业务实现类
```Java
package com.swagger.service;

import com.swagger.dao.UserDao;
import com.swagger.dao.UserDaoImpl;
import com.swagger.dao.UserDaoMySQLImpl;
import com.swagger.dao.UserDaoOracleImpl;

// 业务层调用Dao层
public class UserServiceImpl implements UserService{

//    private UserDao userDao = new UserDaoOracleImpl();

    private UserDao userDao;

    // 利用set来动态实现值的注入！
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void getUser() {
        userDao.getUser();
    }
}

```
* 用户测试
```Java
import com.swagger.dao.UserDaoImpl;
import com.swagger.dao.UserDaoOracleImpl;
import com.swagger.service.UserServiceImpl;

public class MyTest01 {
    public static void main(String[] args) {

        // 用户实际调用的是业务层
        // 业务层做的事情实际上就是调用Dao层，然后去查询
        // 使用set的方式则只用在用户使用层面进行调整，不用调整以往的代码
        UserServiceImpl userService = new UserServiceImpl();

        userService.setUserDao(new UserDaoOracleImpl());

        userService.getUser();

    }
}

```

### IOC的本质
* 控制反转IOC（Inversion of control）是一种设计思想，DI（依赖注入）是实现IOC的一种方法
* 没有IOC的程序中，使用面向对象编程，对象的创建和对象之间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制
* IOC之后将对象的创建转移给了第三方，依赖对象的方式反转了
* 具体来说多加一层 IOC 容器，加入中间件，进行解耦
* **控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是 IOC 容器，其实现方法是依赖注（DI）**

## Hello Spring 
* 控制：谁来控制对象的创建，使用Spring之后，对象的创建有Spring来接管
* 反转：程序本身不创建对象，只从IOC容器中被动接受对象
* 依赖注入：就是利用set方法来进行注入
* IOC是一种编程思想，由主动的编程new，编程被动的接受对象
* **对象由Spring来创建，管理，装配**

### 代码实战
* pojo实体类
```Java
package com.swagger.pojo;

public class Hello {
    private String str;

    public String getStr() {
        return str;
    }

    // 如果set方法去掉，Spring会直接报错
    // 必须得有set方法，hui利用set进行注入
    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

* resources中的Spring注册配置文件 `beans.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--  使用spring来创建对象，在spring中，对象都称为bean
     -->

    <bean id="hello" class="com.swagger.pojo.Hello">
        <!--
        id则为变量名
        value则为属性设置值
        -->
        <property name="str" value="Spring"/>
    </bean>

</beans>
```

* 测试类
```Java
import com.swagger.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {

        // 获取spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");

        // 我们的对象都在spring中管理了，要使用直接在里面取出来就行了
        // 现在已经不用 new 关键字了，直接取
        Hello hello = (Hello) context.getBean("hello");

        System.out.println(hello.toString());


    }
}

```









## IOC创建对象的方式
* 默认使用无参构造创建对象，实际上是通过setter
* 只要是在beans.xml文件中注册的类，在程序跑起来的时候都会被构造出来，想要使用的话get就行

### 有参数构造方法
* 通过下标的序号
```xml
<bean id="user_you1" class="com.swagger.pojo.User">
    <constructor-arg index="0" value="swagger_you1"/>
</bean>
```

* 通过参数类型
```xml
<bean id="user_you2" class="com.swagger.pojo.User">
    <constructor-arg type="java.lang.String" value="swagger_you2"/>
</bean>
```

* 通过参数名
```xml
<bean id="user_you3" class="com.swagger.pojo.User">
    <constructor-arg name="name" value="swagger_you3"/>
</bean>
```











## 






