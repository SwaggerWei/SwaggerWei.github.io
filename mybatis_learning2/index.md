# MyBatis学习2


## 核心配置文件
* 官方推荐名称：mybatis-config.xml
* MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息

### 核心配置点 configuration
* properties（属性）
* settings（设置）
* typeAliases（类型别名）
* typeHandlers（类型处理器）
* objectFactory（对象工厂）
* plugins（插件）
* environments（环境配置）
* environment（环境变量）
* transactionManager（事务管理器）
* dataSource（数据源）
* databaseIdProvider（数据库厂商标识）
* mappers（映射器）

### 环境配置 environments
* MyBatis 可以配置成适应多种环境
* 尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境
* 如果你想连接两个数据库，就需要创建两个 SqlSessionFactory 实例，每个数据库对应一个
* 如果需要切换环境，则将 `<environments default="development">` 中的default对应的环境更换
* datasource：数据源，进行数据库连接 （没有连接池unpooled/有数据池pooled/直接连接jndi）

### 属性 properties
_可以通过properties属性，引用配置文件_
* 编写一个配置文件 `db.properties`
```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=UTF-8&serverTimezone=GMT
username=root
password=Wby785403310
```

* 在mybatis配置文件`mybatis-config.xml`中引入 `db.properties`，并在environment中设置为properties中的value
```xml
<environment id="development">
    <transactionManager type="JDBC"/>
    <dataSource type="POOLED">
        <!--数据库相关配置-->
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
    </dataSource>
</environment>
```

### 类型别名 typeAliases
* 类型别名是为**Java类型设置一个短的名字**
* 存在的意义仅在于用来**减少类的完全限定名的冗余**


#### 具体方法
* 方法一：为一个类取一个固定的别名 （实体类比较少的时候可以使用，但是这种方法可以自定义名称）
```xml
<typeAliases>
    <typeAlias type="com.swagger.pojo.User" alias="User"/>
</typeAliases>
```

* 方法二：指定一个包名，会搜索改包下的所有java文件 （实体类很多的时候，则用这种方法）
```xml
<typeAliases>
    <package name="com.swagger.pojo"/>
</typeAliases>
```

* 或者用注解取别名，实体类上增加注解 `@Alias("user")`
* **数据类型的默认别名，不区分大小写**

### 设置 settings
MyBatis中极为重要的调整设置，可以改变MyBatis运行时的行为  
* cacheEnabled：缓存是否开启
* useGeneratedKeys：运行JDBC自动生成主键
* mapUnderscoreToCamelCase：数据库下划线字段命名到驼峰命名之间的转换 **last_name -> lastName**
* loglmpl：指定MyBatis所用日志的实现
* lazyLoadingEnabled：延迟加载是否开启

### 其他配置
* typeHandlers 类型处理器
* objectFactory 对象工厂
* plugins插件：MyBatis-generator-core（代码生成器）；MyBatis-plus；通用mapper

### 映射器Mapper
* MapperRegistry：注册Dao层的mapper文件
* Dao层中的接口文件和mapper文件需要使用相同的命名，以便管理
```xml
<!--  每一个Dao层中的 Mapper.xml 都需要在的核心配置文件当中注册  -->
<mappers>
    <mapper resource="com/swagger/dao/UserMapper.xml"/>
</mappers>
```

## 生命周期和作用域
![](/image_MyBatis/pic1.png)
* 作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的并发问题

### SqlSessionFactoryBuilder
* 一旦创建了 SqlSessionFactory，就不再需要他了
* 局部变量

### SqlSessionFactory
* 可以想象为数据库的连接池
* 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例
* 最佳作用域是应用作用域，程序开始就开始，程序结束就结束了
* 最简单的就是使用**单例模式**或者静态单例模式

### SqlSession
* 连接到连接池的一个请求，请求结束需要关闭
* 每个线程都应该有它自己的 SqlSession 实例
* SqlSession 的实例不是线程安全的，因此是不能被共享的
* 它的最佳的作用域是**请求或方法作用域**， 用完之后需要赶紧关闭，否则资源则被占用
![](/image_MyBatis/pic2.png)
* 上图中的每一个mapper就代表一个业务

### 实体类属性名和数据库字段名不一样的问题
* 方法一：在sql中使用`as`关键字取别名，别名与实体类的名字一致

* 方法二：结果集映射，将数据库的名称映射成实体类的名称（column代表数据库字段名，property代表实体类属性名）
![](/image_MyBatis/pic3.png)


## 日志
### 日志工厂
* 如果一个数据库操作出现了异常，我们需要排错，日志就是最好的助手（sout，debug）
* 更好的方式是使用MyBatis的日志工厂 `logImpl`。
* 其中SLF4J；Apache Commons Logging；**Log4j** 2；Log4j （3.5.9 起废弃）；JDK logging
* 具体使用哪一个日志实现在setting 中设定

#### 手动配置为STDOUT_LOGGING
* 设置日志格式为 `STDOUT_LOGGING`
```xml
<settings>
<!--使用默认的控制台日志-->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```
#### 手动配置为LOG4J
* 设置日志格式为 `LOG4J`
* 一个apache的开源项目，日志信息的输送地为控制台，文件和GUI组件，
* 可以控制输出格式，定义信息级别，
* 通过配置文件来进行灵活修改

* maven中导入依赖
```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

* log4j.properties 配置文件
```properties
# 将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

# 控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

# 文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/swagger.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

# 日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

* log4j的使用，直接测试运行刚才的查询
![](/image_MyBatis/pic4.png)


#### log4j 的简单使用
* 要使用log4j中导入相应的包（apache那个包） `import org.apache.log4j.Logger;`
* 日志对象生成，加载参数为当前类的class，反射获得 `public static Logger logger = Logger.getLogger(UserMapperTest.class);`
* 测试代码
```Java
@Test
public void testLog4J(){
    logger.info("info:进入了testLog4J");
    logger.debug("debug:进入了testLog4J");
    logger.error("error:进入了testLog4J");

}
```












## 分页
为什么进行分页：减少数据的处理量


### 使用MyBatis实现分页，核心为SQL代码
`select * from user limit 0, 2;`
* 接口
```Java
// 查询 分页
List<User> getUserByLimit(Map<String, Integer> map);
```
* Mapper.xml
```xml
<!--分页实现查询-->
    <select id="getUserByLimit" parameterType="map" resultType="User">
        select * from mybatis.user limit #{startIndex}, #{pageSize}
    </select>
```
* 测试
```Java
@Test
public void testGetByLimit(){
    SqlSession sqlSession = MyBatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

    HashMap<String, Integer> map = new HashMap<>();
    map.put("startIndex", 0);
    map.put("pageSize", 2);

    List<User> users = userMapper.getUserByLimit(map);
    for (User user : users) {
        System.out.println(user);
    }

    sqlSession.close();
}
```

### 使用RowBounds进行分页
* 接口
```Java
// 分页实现查询 使用rowBounds
List<User> getUserByRowBounds();
```

* Mapper.xml
```xml
<!--分页实现查询 使用rowBounds-->
<select id="getUserByRowBounds" resultType="User">
    select * from mybatis.user
</select>
```

* 代码测试
```Java
@Test
public void testGetUserByRowBounds(){
    SqlSession sqlSession = MyBatisUtils.getSqlSession();

    // RouBounds实现分页
    RowBounds rowBounds = new RowBounds(1, 2);

    // 通过Java代码层面，实现分页
    List<Object> users = sqlSession.selectList("com.swagger.dao.UserMapper.getUserByRowBounds", null, rowBounds);
    for (Object user : users) {
        System.out.println(user);
    }

    sqlSession.close();
}
```








### 分页插件
使用MyBatis 分页插件 PageHelper


## 使用注解开发
实际上除了MyBatis框架是使用**配置文件进行开发**，**大部分框架都是使用注解来进行开发**








