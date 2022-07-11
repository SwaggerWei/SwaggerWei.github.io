# Spring学习2



## AOP 面向切面编程
* 通过预编译和运行期动态代理实现程序功能的统一维护技术
* AOP是OOP的延续

## 使用Spring来实现AOP
* 导入依赖包
```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
    <scope>runtime</scope>
</dependency>
```

### 实现方式一 使用Spring的API接口
* UserService接口
```Java
package com.swagger.service;

public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void select();
}

```

* UserServiceImpl实现类
```Java
package com.swagger.service;

public class UserServiceImpl implements UserService{
    @Override
    public void add() {
        System.out.println("增加了一个用户");
    }

    @Override
    public void delete() {
        System.out.println("删除了一个用户");
    }

    @Override
    public void update() {
        System.out.println("修改了一个用户");
    }

    @Override
    public void select() {
        System.out.println("查询了一个用户");
    }
}

```

* AfterLog类 环绕插入，后置
```Java
package com.swagger.log;

import org.springframework.aop.AfterReturningAdvice;

import java.lang.reflect.Method;

public class AfterLog implements AfterReturningAdvice {

    // returnValue: 返回值
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了" + method.getName() + "返回的结果为" + returnValue);
    }
}

```

* BeforeLog类 环绕插入，前置
```Java
package com.swagger.log;

import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class BeforeLog implements MethodBeforeAdvice {

    // 前置增强
    // method： 要执行的目标对象的方法
    // args：参数
    // target：目标对象
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + "的" + method.getName() + "方法被执行了");
    }
}

```

* 测试
```Java
import com.swagger.service.UserService;
import com.swagger.service.UserServiceImpl;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {

    @Test
    public void test1(){

        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        // 注意，动态代理的一定是接口，不能是其他的东西
        UserService userService = context.getBean("userService", UserService.class);

        userService.select();
    }
}

```

* spring 配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">


    <!--  注册bean  -->
    <bean id="userService" class="com.swagger.service.UserServiceImpl"/>
    <bean id="beforeLog" class="com.swagger.log.BeforeLog"/>
    <bean id="afterLog" class="com.swagger.log.AfterLog"/>

    <!--  使用原生的Spring API接口  -->
    <!--  配置aop 需要导入aop的约束 -->
    <aop:config>
        <!--切入点 expression表达式 execution(要执行的位置！)-->
        <aop:pointcut id="pointcut" expression="execution(* com.swagger.service.UserServiceImpl.*(..))"/>

        <!--   advisor 执行环绕    -->
        <aop:advisor advice-ref="beforeLog" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>

    </aop:config>


</beans>
```

## Spring与MyBatis的整合
* 导入相关的jar包：junit,mybatis,mysql,spring,aop,mybatis-spring
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>Spring-study</artifactId>
        <groupId>com.swagger</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>spring-10-mybatis</artifactId>

    <properties>
        <maven.compiler.source>16</maven.compiler.source>
        <maven.compiler.target>16</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.25</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.19</version>
        </dependency>

        <!--Spring 操作数据库，还需要Spring-jdbc这个包-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.19</version>
        </dependency>

        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>

        <!--mybatis 和 spring 整合的包-->
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.6</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.6</version>
        </dependency>
    </dependencies>

</project>
```

* 编写配置文件
* 测试

### mybatis-spring使用
* 编写数据源配置
* sqlSessionFactory配置
* sqlSessionTemplate配置
* 需要给接口增加实现类
* 将自己写的实现类注入到Spring当中
* 测试

#### 代码实战
* UserMapper接口
```Java
package com.swagger.mapper;

import com.swagger.pojo.User;

import java.util.List;

public interface UserMapper {
    public List<User> selectUser();
}

```
* UserMapper.xml 接口对应的sql语句
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.swagger.mapper.UserMapper">
    <select id="selectUser" resultType="user">
        select * from mybatis.user
    </select>

</mapper>



```

* UserMapperImpl 实现类
```Java
package com.swagger.mapper;

import com.swagger.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;

import java.util.List;

public class UserMapperImpl implements UserMapper{

    // 我们的所有操作原来都使用sqlSession来执行，现在使用sqlSessionTemplate
    private SqlSessionTemplate sqlSession;

    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
        this.sqlSession = sqlSessionTemplate;
    }

    @Override
    public List<User> selectUser() {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.selectUser();
    }
}

```

* User 与数据库对应的实体类
```Java
package com.swagger.pojo;

import lombok.Data;

@Data
public class User {
    private int id;
    private String name;
    private String pwd;
}

```

* mybatis-config.xml 配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <import resource="spring-dao.xml"/>

    <!--  这个总的文件单独用来注册bean
          spring-dao.xml就不用动了
    -->

    <!--  注册UserMapperImpl  -->
    <bean id="userMapperImpl" class="com.swagger.mapper.UserMapperImpl">
        <property name="sqlSessionTemplate" ref="sqlSession"/>
    </bean>

</beans>
```

* spring-dao.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--  别名管理  -->
    <typeAliases>
        <package name="com.swagger.pojo"/>
    </typeAliases>

</configuration>



```

* applicationContext.xml整合配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--  DataSource:使用spring的数据源替代mybatis的配置  c3p0 dbcp druid
    我们这里使用Spring提供的JDBC：org.springframework.jdbc.datasource
    -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT"/>
        <property name="username" value="root"/>
        <property name="password" value="Wby785403310"/>
    </bean>

    <!--  配置sqlSessionFactory  -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>

        <!--    绑定Mybatis配置文件    -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/swagger/mapper/*.xml"/>
    </bean>

    <!--  配置SqlSession，使用SqlSessionTemplate  -->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <!--    只能使用构造器注入，因为没有set方法    -->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>


</beans>
```


## Spring事务管理
* 声明式事务（AOP），应用
* 编程式事务，需要在代码中进行事务管理
* 实际上用得最多的是声明式事务

### 代码实战
* UserMapperImpl
```Java
package com.swagger.mapper;

import com.swagger.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.support.SqlSessionDaoSupport;

import java.util.List;

public class UserMapperImpl extends SqlSessionDaoSupport implements UserMapper{

//    // 我们的所有操作原来都使用sqlSession来执行，现在使用sqlSessionTemplate
//    private SqlSessionTemplate sqlSession;
//
//    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
//        this.sqlSession = sqlSessionTemplate;
//    }

    @Override
    public List<User> selectUser() {

        User user = new User(9, "swagger8", "1232451");

        UserMapper mapper = getSqlSession().getMapper(UserMapper.class);

        mapper.addUser(user);
        mapper.deleteUser(9);

        return mapper.selectUser();
    }

    @Override
    public int addUser(User user) {
        return getSqlSession().getMapper(UserMapper.class).addUser(user);
    }

    @Override
    public int deleteUser(int id) {
        return getSqlSession().getMapper(UserMapper.class).deleteUser(1);
    }
}
```

* UserMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.swagger.mapper.UserMapper">
    <select id="selectUser" resultType="user">
        select * from mybatis.user
    </select>

    <insert id="addUser" parameterType="user">
        insert into mybatis.user (id, name, pwd) values (#{id}, #{name}, #{pwd})
    </insert>

    <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id=#{id}
    </delete>

</mapper>



```

* UserMapper 接口
```Java
package com.swagger.mapper;

import com.swagger.pojo.User;

import java.util.List;

public interface UserMapper {
    // 查询用户
    public List<User> selectUser();

    // 添加一个用户
    public int addUser(User user);

    // 删除一个用户
    public int deleteUser(int id);



}

```

* spring-dao.xml文件中配置事务 和 aop切入
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--  DataSource:使用spring的数据源替代mybatis的配置  c3p0 dbcp druid
    我们这里使用Spring提供的JDBC：org.springframework.jdbc.datasource
    -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT"/>
        <property name="username" value="root"/>
        <property name="password" value="Wby785403310"/>
    </bean>

    <!--  配置sqlSessionFactory  -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>

        <!--    绑定Mybatis配置文件    -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/swagger/mapper/*.xml"/>
    </bean>

    <!--  配置SqlSession，使用SqlSessionTemplate  -->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <!--    只能使用构造器注入，因为没有set方法    -->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>

    <!--  配置声明式事务  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg ref="dataSource" />
    </bean>

    <!--  结合aop，实行事务的织入  -->
    <!--  配置事务的通知  -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--    给哪些方法配置事务    -->
        <!--    配置事务的传播特性    -->
        <tx:attributes>
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete" propagation="REQUIRED"/>
            <tx:method name="update" propagation="REQUIRED"/>
            <tx:method name="query" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!--  配置事务切入  -->
    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(* com.swagger.mapper.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>


</beans>
```

* 测试
```Java
@Test
public void test2(){
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    UserMapper userMapper = context.getBean("userMapper", UserMapper.class);

    List<User> users = userMapper.selectUser();
    for (User user : users) {
        System.out.println(user);
    }
}
```






