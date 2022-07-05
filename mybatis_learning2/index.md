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







