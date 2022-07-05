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









## CRUD
### UserMapper.xml文件
* namespace：namespace中的包名要和Dao/Mapper中的接口名称一致
* select：选择，查询语句
* id：就是对应namespace中的方法名称
* resultType：sql语句执行的返回值类型
* parameterType：参数类型

### 代码实战
* `UserMapper.java`接口中添加抽象方法，返回类型和参数类型需要注意
```Java
package com.swagger.dao;

import com.swagger.pojo.User;

import java.util.List;

// Data Access object
// 等价于mapper
public interface UserMapper {

    // 获取全部用户
    List<User> getUserList();

    // 根据id查询用户
    User getUserById(int id);

    // insert 一个用户
    int addUser(User user);

    // update 修改用户
    int updateUser(User user);

    // delete 删除一个用户
    int deleteUser(int id);
}

```

* `UserMapper.xml` 中添加对应的标签和sql语句，返回类型和参数类型需要对应
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.swagger.dao.UserMapper">
    <!-- select 查询语句
    id 相当于UserDao中提取操作接口的名字-->
    <select id="getUserList" resultType="com.swagger.pojo.User">
        select * from mybatis.user
    </select>

    <!--  根据id查询用户  -->
    <select id="getUserById" parameterType="int" resultType="com.swagger.pojo.User">
        select * from mybatis.user where id = #{id}
    </select>

    <!-- 对象中的属性，可以直接取出来
    插入一个用户-->
    <insert id="addUser" parameterType="com.swagger.pojo.User">
        insert into mybatis.user (id, name, pwd) values (#{id}, #{name}, #{pwd})
    </insert>

    <!--update 修改用户-->
    <update id="updateUser" parameterType="com.swagger.pojo.User">
        update mybatis.user set name = #{name}, pwd = #{pwd} where id = #{id}
    </update>
    
    <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id = #{id}
    </delete>
    
</mapper>
```

* 测试类中进行测试
```java
package com.swagger.dao;

import com.swagger.pojo.User;
import com.swagger.utils.MyBatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserMapperTest {

    @Test
    public void test(){

        // 1 获得sqlSession对象
        SqlSession sqlSession = MyBatisUtils.getSqlSession();

        // 2 方式1： 执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = userMapper.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }

        // 关闭sqlSession
        sqlSession.close();

    }

    // 增删改需要提交事务
    // 查
    @Test
    public void test_getUserById(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();

        int id = 1;

        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User userById = userMapper.getUserById(1);
        System.out.println(userById);


        sqlSession.close();
    }

    //
    @Test
    public void test_addUser(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int i = mapper.addUser(new User(4, "haha", "123456"));
        if (i>0){
            System.out.println("插入成功");
            // 提交事务
            sqlSession.commit();
        }

        sqlSession.close();
    }

    @Test
    public void test_updateUser(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        int i = mapper.updateUser(new User(4, "hehe", "1234"));
        if (i>0){
            System.out.println("修改成功");
            sqlSession.commit();
        }

        sqlSession.close();
    }

    @Test
    public void test_deleteUser(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        int i = mapper.deleteUser(3);
        if (i>0){
            System.out.println("删除成功");
            sqlSession.commit();
        }

        sqlSession.close();
    }

}

```

* tips：增删改需要对事务进行提交 `sqlSession.commit();`

### 错误分析
* 标签名字不能和具体的sql关键词相冲突
* resources 注册mapper的路径不能使用java 点的格式来表示路径，要用斜杠/
* 程序配置文件必须规范
* 空指针异常，没有注册到资源
* maven 资源没有导出到target问题











## 万能的map
* 将Dao接口中方法的输入参数换成map类型 `int addUser2(Map<String, Object> map);`
* 对应mapper.xml中的参数换成map中的key，即可取到具体的对应的value
* 编写测试类
```Java
@Test
    public void test_addUser2(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        Map<String, Object> map = new HashMap<>();
        map.put("userid", 5);
        map.put("username", "boyanwei");
        map.put("passWord", "12345");

        int i = mapper.addUser2(map);
        if (i > 0){
            System.out.println("插入成功");
            sqlSession.commit();
        }


        sqlSession.close();

    }
```

### 传递参数关键点
* Map传递参数，直接在SQL中取出key即可：`parameterType="map"`
* 对象传递参数，直接在SQL中取出对象的属性即可： `parameterType="Object"`
* 只有一个基本类型参数的情况下，可以直接在sql中取到，参数类型可以不写
* **tips** ： 多个参数可以用**map**，或者**注解**

### 模糊查询 like
* 在java代码层面使用通配符
* 在SQL拼接中加入通配符，但是有可能存在SQL注入问题，不安全







