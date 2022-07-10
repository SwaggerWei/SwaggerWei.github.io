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
