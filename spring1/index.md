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

## Spring参数配置
### 别名 
```xml
<alias name="user_you3" alias="user"/>
```

### bean的配置
```xml
<bean id="user_you3" class="com.swagger.pojo.User">
    <constructor-arg name="name" value="swagger_you3"/>
</bean>
```

* id：bean的唯一标识符号，相当于对象名
* class：bean对象对象对应的全限定名：包名 + 类名
* name：对象的别名，name更高级，name可以同时取多个别名

### import导入
* 一般用于团队开发，可以使用多个配置文件，导入合并为一个配置文件
* 每个开发人员都独立管理一个`beans.xml`文件，最后使用import导入合并为一个`applicationContext.xml`文件

#### 实际操作
* 在resources中，新建一个`applicationContext.xml`文件，并将其并入到已有的beans中
* 将`applicationContext.xml`文件作为多个beans文件的汇总文件
![](/images_Spring/pic3.png)
* import导入其他的beans文件
```xml
<import resource="beans.xml"/>
```




## DI(Dependency Injection) 依赖注入
### 构造器注入
* 参考前文
* 构造器注入是有弊端的

### set方式注入（重点）
* 依赖注入，set注入
* 依赖：bean对象的创建依赖于容器
* 注入：bean对象中的所有属性由容器来注入

#### 实际场景运用
* 复杂类型
```Java
package com.swagger.pojo;

public class Address {
    private String address;

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Address{" +
                "address='" + address + '\'' +
                '}';
    }
}
```

* 真实测试对象
```java
package com.swagger.pojo;

import java.util.*;

public class Student {
    private String name;
    private Address address;
    private String[] books;
    private List<String> hobbys;
    private Map<String, String> card;
    private Set<String> games;
    private String wife;
    private Properties info;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public String[] getBooks() {
        return books;
    }

    public void setBooks(String[] books) {
        this.books = books;
    }

    public List<String> getHobbys() {
        return hobbys;
    }

    public void setHobbys(List<String> hobbys) {
        this.hobbys = hobbys;
    }

    public Map<String, String> getCard() {
        return card;
    }

    public void setCard(Map<String, String> card) {
        this.card = card;
    }

    public Set<String> getGames() {
        return games;
    }

    public void setGames(Set<String> games) {
        this.games = games;
    }

    public String getWife() {
        return wife;
    }

    public void setWife(String wife) {
        this.wife = wife;
    }

    public Properties getInfo() {
        return info;
    }

    public void setInfo(Properties info) {
        this.info = info;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", address=" + address.toString() +
                ", books=" + Arrays.toString(books) +
                ", hobbys=" + hobbys +
                ", card=" + card +
                ", games=" + games +
                ", wife='" + wife + '\'' +
                ", info=" + info +
                '}';
    }
}
```

* beans.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="address" class="com.swagger.pojo.Address"/>

    <bean id="student" class="com.swagger.pojo.Student">

        <!--   普通值注入 value    -->
        <property name="name" value="swagger"/>

        <!--   bean引用类型注入 ref    -->
        <property name="address" ref="address"/>

        <!--   array类型注入 value    -->
        <property name="books">
            <array>
                <value>红楼梦</value>
                <value>西游记</value>
                <value>水浒传</value>
                <value>三国演义</value>
            </array>
        </property>

        <!--   list类型注入 value    -->
        <property name="hobbys">
            <list>
                <value>听歌</value>
                <value>敲代码</value>
                <value>看电影</value>
            </list>
        </property>


        <!--   map类型注入 key value    -->
        <property name="card">
            <map>
                <entry key="身份证" value="2412414341"/>
                <entry key="银行卡" value="3513531513513"/>
            </map>
        </property>

        <!--   set类型注入 value    -->
        <property name="games">
            <set>
                <value>lol</value>
                <value>coc</value>
                <value>bob</value>
            </set>
        </property>

        <!--   null   -->
        <property name="wife">
            <null/>
        </property>

        <!--   Properties类型   -->
        <property name="info">
            <props>
                <prop key="学号">32009193</prop>
                <prop key="性别">男</prop>
                <prop key="姓名">swagger</prop>
            </props>
        </property>
    </bean>
</beans>
```

* 测试类
```Java
import com.swagger.pojo.Student;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student student = (Student) context.getBean("student");

        System.out.println(student.toString());
    }
}

```



### 拓展方式注入 
* p命名空间注入，实际上就是简化版本的set方式注入
* c命名空间注入，实际上就是简化版本的有参构造器注入
* 不能直接使用，需要导入xml约束 `xmlns:p="http://www.springframework.org/schema/p"`
* 如果使用p，则需要无参数构造器和**getter和setter**
* 如果使用c，则需要**有参构造器**

#### 代码实战
* 实体类
```Java
package com.swagger.pojo;

public class User {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

* userBeans.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--  p命名空间注入, 可以直接进行简单注入，实际上就是set方式注入-->
    <bean id="user" class="com.swagger.pojo.User" p:age="18" p:name="swagger"/>
    

</beans>
```

* 测试类
```Java
@Test
    public void test1(){
        ApplicationContext context = new ClassPathXmlApplicationContext("userBeans.xml");
        User user = (User) context.getBean("user");

        System.out.println(user.toString());
    }
```


### beans scope 作用域
* 在bean定义的最后添加scope表示作用域 `<bean id="user" class="com.swagger.pojo.User" p:age="18" p:name="swagger" scope="singleton"/>`
* singleton 单例模式作用域（默认机制），单线程中使用
* prototype 原型模式作用域，多线程中使用
* 其余的（request、session、application）只能在web开发中使用



## bean的自动装配（xml配置）
* 自动装配是Spring满足bean依赖的一种方式
* Spring会在上下文中自动寻找，并自动给bean转配属性

### 装配方式
* xml中显式配置
* java中显式配置（Spring新特性，老版本没有）
* 隐式的自动装配（重要）

### 测试
* 环境搭建：一个人有两个宠物

### ByName自动装配
* 会自动在上下文中查找，和自己对象set方法后面的值对应的 bean id 
* 名字必须要相同
```xml
<bean id="cat" class="com.swagger.pojo.Cat"/>
<bean id="dog" class="com.swagger.pojo.Dog"/>

<!--
  byName：会自动在上下文中查找，和自己对象set方法后面的值对应的 bean id （名字必须要相同）
  byType：会自动在上下文中查找，和对象属性类型相同的bean （保证类型全局唯一，甚至不需要id都可以跑）
  -->
<bean id="people" class="com.swagger.pojo.People" autowire="byName">
    <property name="name" value="swagger"/>
</bean>
```

### ByType自动装配
* 会自动在上下文中查找，和对象属性类型相同的bean
* 保证类型全局唯一，甚至不需要id都可以跑
```xml
<bean id="cat" class="com.swagger.pojo.Cat"/>
<bean id="dog" class="com.swagger.pojo.Dog"/>

<!--
  byName：会自动在上下文中查找，和自己对象set方法后面的值对应的 bean id （名字必须要相同）
  byType：会自动在上下文中查找，和对象属性类型相同的bean （保证类型全局唯一，甚至不需要id都可以跑）
  -->
<bean id="people" class="com.swagger.pojo.People" autowire="byType">
    <property name="name" value="swagger"/>
</bean>
```

## 注解实现自动装配
* jdk1.5开始支持注解，spring2.5开始支持注解
* 比xml配置更为简洁

### xml准备
* 导入xml约束 `xmlns:context="http://www.springframework.org/schema/context"`
* 增加注解的支持 `<context:annotation-config/>`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

### @Autowired
* 直接在属性上使用即可，或者放在set方法上
* 如果放在引用属性上，该属性可以不需要set方法，前提是这个属性在IOC容器中存在，且符合类型byType
* @Nullable 声明该属性可以为空，不报空指针的错误
* 如果显式定义了@Autowired的required属性为false，则说明这个对象可以为null，否则不允许为空
* 如果@Autowired自动装配的环境比较复杂，有多个相同类型但是不同id的bean，自动装配无法通过一个注解 @Autowired完成的时候，可以使用@Qualifier(value="dog222") 配合指定bean的id名字 

### @Resource
* JDK自带的注解方式，也可以放在属性上使用
* 默认通过byName方式实现，如果找不到名字则通过byType方式





