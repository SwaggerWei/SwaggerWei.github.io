# MySQL学习5


## JDBC 中对象的解释
### DriverManager
```Java
// 1 加载驱动
// DriverManager.registerDriver(new com.mysql.jdbc.Driver());
Class.forName("com.mysql.jdbc.Driver"); // 固定写法，加载驱动
Connection connection = DriverManager.getConnection(url, username, password);

// connection是代表数据库
// 数据库设置自动提交
connection.rollback();
connection.commit();
connection.setAutoCommit();
```

### URL
```Java
String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
// 协议://主机地址:端口号/数据库名?参数1&参数2&参数3
// mysql 默认 3306
// oracle 默认 1521
// jdbc:oracle:thin:@主机地址:端口号:sid
```

### Statement 执行SQL的对象 PrepareStatement执行SQL的对象
```Java
String sql = "SELECT * FROM users;";

statement.executeQuery(sql); // 查询操作，返回ResultSet
statement.execute(sql); // 执行任何SQL
statement.executeUpdate(sql); // 更新、插入、删除 都是这个，返回一个受影响的行数int
statement.executeBatch(sql); // 同时执行多个SQL
```

### ResultSet 查询的结果集：封装了所有的查询结果
```Java
resultSet.getObject(); // 不知道数据类型的时候
resultSet.getInt(); // 知道数据类型的时候
resultSet.getFloat();
resultSet.getDate();

resultSet.next() // 数据往后遍历
resultSet.previous() // 前一行
resultSet.absolute(row) // 移动到指定行数
resultSet.afterLast(); // 移动到最后面
resultSet.beforeFirst(); // 移动到最前面
```

### 释放资源 必须要完成（很耗资源）
```Java
// 6 释放连接
resultSet.close();
statement.close();
connection.close();
```


## Statement 对象详解
### 保存数据库所需参数
* src 建立一个 `db.properties`文件，用于保存连接数据库所需要的参数
```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true
username=root
password=Wby785403310
```

### 编写工具类
* 编写工具类中读取 `db.properties`文件中的参数
* 将静态代码块中集成获取驱动
* 实现获取连接和释放资源方法
```Java
package com.swagger.lesson02.utils;

import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

// 数据库连接 工具类
public class JdbcUtils {

    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;

    static {
        try {
            InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
            Properties properties = new Properties();
            properties.load(in);

            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

            // 1 加载驱动 只用加载一次
            Class.forName(driver);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 获取连接
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, username, password);
    }

    // 释放连接资源
    public static void releaseResource(Connection conn, Statement st, ResultSet rs){
        if (rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (st != null){
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

}

```

### 增
```Java
package com.swagger.lesson02;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestInsert {
    public static void main(String[] args) {

        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection(); // 获取数据库连接
            st = conn.createStatement(); // 获取SQL的执行对象
            String sql = "INSERT into users(`id`, `NAME`, `PASSWORD`, `email`, `birthday`)" +
                    "VALUES(5, 'swagger', '123456', 'fdafdsa@qq.com', '2020-01-01');";
            int i = st.executeUpdate(sql);
            if (i > 0){
                System.out.println("插入成功！");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.releaseResource(conn, st, rs);
        }
    }
}

```

### 删
```Java
package com.swagger.lesson02;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestDelete {
    public static void main(String[] args) {

        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection(); // 获取数据库连接
            st = conn.createStatement(); // 获取SQL的执行对象

            String sql = "DELETE FROM users WHERE id = 4;";
            int i = st.executeUpdate(sql);
            if (i > 0){
                System.out.println("删除成功！");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.releaseResource(conn, st, rs);
        }


    }
}

```

### 改
```Java
package com.swagger.lesson02;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestUpdate {
    public static void main(String[] args) {

        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection(); // 获取数据库连接
            st = conn.createStatement(); // 获取SQL的执行对象
            String sql = "UPDATE users SET `NAME` = 'swagger', `email` = '12345@qqq.com' WHERE id = 1;";
            int i = st.executeUpdate(sql);
            if (i > 0){
                System.out.println("更新成功！");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.releaseResource(conn, st, rs);
        }


    }
}

```

### 查
```Java
package com.swagger.lesson02;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestSelect {
    public static void main(String[] args) {

        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();
            st = conn.createStatement();

            String sql = "select * from `users` where id = 1;";

            rs = st.executeQuery(sql);
            while (rs.next()){
                System.out.println(rs.getString("NAME"));
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.releaseResource(conn, st, rs);
        }

    }
}

```


## SQL 注入问题
* SQL 存在 拼接情况，可能会受到攻击
```Java
package com.swagger.lesson02;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class QuestionOfSQLDrilling {

    public static void main(String[] args) {
        // 正常使用
//        login("lisi", "123456");

        // 非正常使用 SQL非法注入情况
        login("'or '1=1", "'or'1=1");
    }

    public static void login(String username, String password){
        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();
            st = conn.createStatement();

            // SELECT * FROM users WHERE `NAME` = 'lisi' and `PASSWORD` = '123456';
            // SELECT * FROM users WHERE `NAME` = '' or '1=1' and `PASSWORD` = ''or'1=1'; // 非法注入sql ，导致意思改变
            String sql = "SELECT * FROM users WHERE `NAME` = '" + username + "' and `PASSWORD` = '" + password + "';";

            rs = st.executeQuery(sql);
            while (rs.next()){
                System.out.println(rs.getString("NAME"));
                System.out.println(rs.getString("password"));
                System.out.println("==============");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.releaseResource(conn, st, rs);
        }
    }
}
 
```




## PreparedStatement 对象
* 避免了SQL注入问题
* 效率更高

### 增
```Java
package com.swagger.lesson03;

import com.swagger.lesson02.utils.JdbcUtils;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.Date;

public class TestInsert {
    public static void main(String[] args) {

        Connection connection = null;
        PreparedStatement pst = null;

        try {
            connection = JdbcUtils.getConnection();

            // 区别
            // 使用 ? 占位符替代参数
            String sql = "INSERT into users(`id`, `NAME`, `PASSWORD`, `email`, `birthday`)" +
                    "VALUES(?, ?, ?, ?, ?);";

            // 预编译SQL
            pst = connection.prepareStatement(sql);

            // 手动为参数赋值
            pst.setInt(1, 6);
            pst.setString(2 ,"Sinyi");
            pst.setString(3, "467879");
            pst.setString(4, "21431421@qq.com");
            // 注意点：sql.Date 数据库
            //        util.Date Java new Date().getTime() 获得时间戳
            pst.setDate(5, new java.sql.Date(new Date().getTime()));

            // 执行
            int i = pst.executeUpdate();
            if (i > 0){
                System.out.println("插入成功");
            }


        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.releaseResource(connection, pst, null);
        }
    }
}

```

### 删
```Java
package com.swagger.lesson03;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.util.Date;

public class TestDelete {

    public static void main(String[] args) {

        Connection connection = null;
        PreparedStatement pst = null;

        try {
            connection = JdbcUtils.getConnection();

            // 区别
            // 使用 ? 占位符替代参数
            String sql = "DELETE from users where id = ?";

            // 预编译SQL
            pst = connection.prepareStatement(sql);

            // 手动为参数赋值
            pst.setInt(1, 3);

            // 执行
            int i = pst.executeUpdate();
            if (i > 0){
                System.out.println("删除成功");
            }


        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.releaseResource(connection, pst, null);
        }

    }


}

```
### 改
```Java
package com.swagger.lesson03;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;

public class TestUpdate {

    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement pst = null;

        try {
            connection = JdbcUtils.getConnection();

            // 区别
            // 使用 ? 占位符替代参数
            String sql = "UPDATE users SET `NAME` = ? WHERE id = ?;";

            // 预编译SQL
            pst = connection.prepareStatement(sql);

            // 手动为参数赋值
            pst.setString(1, "swagger1");
            pst.setInt(2, 1);

            // 执行
            int i = pst.executeUpdate();
            if (i > 0){
                System.out.println("更新成功");
            }


        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.releaseResource(connection, pst, null);
        }
    }
}

```


### 查
```Java
package com.swagger.lesson03;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.*;

public class QuestionOfSQLDrilling {
    public static void main(String[] args) {
        // 正常使用
        login("lisi", "123456");

        // 非正常使用 SQL非法注入情况
//        login("'or '1=1", "'or'1=1");
    }

    public static void login(String username, String password){
        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();

            // SELECT * FROM users WHERE `NAME` = 'lisi' and `PASSWORD` = '123456';
            // SELECT * FROM users WHERE `NAME` = '' or '1=1' and `PASSWORD` = ''or'1=1'; // 非法注入sql ，导致意思改变
            // 防止SQL的本质是把传递进来的参数当作字符，假设其中存在转译字符，会被直接转译
            String sql = "SELECT * FROM `users` WHERE `name1` = ? and `psd` = ?;";

            st = conn.prepareStatement(sql);
            st.setString(1, username);
            st.setString(2, password);

            rs = st.executeQuery(sql);
            while (rs.next()){
                System.out.println(rs.getString("name1"));
                System.out.println(rs.getString("psd"));
                System.out.println("==============");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.releaseResource(conn, st, rs);
        }
    }
}

```









## JDBC操作事务
* 要么都成功，要么都失败
* ACID原则：原子性、一致性、隔离性、持久性

### 代码实战
* 开启事务 `connection.setAutoCommit(false);`
* 一组事务执行完毕，提交事务
* 如果不成功就会回归（默认），或者在catch中显式调用回滚语句

```Java
package com.swagger.lesson04;

import com.swagger.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class TestTransaction1 {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement statement = null;
        ResultSet resultSet = null;

        try {
            connection = JdbcUtils.getConnection();

            // 关闭数据库的自动提交功能, 会自动开启事务
            connection.setAutoCommit(false);

            String sql1 = "update account set money = money - 100 where name = 'A'";
            statement = connection.prepareStatement(sql1);
            statement.executeUpdate();

            int x = 1/0; // 会报错失败

            String sql2 = "update account set money = money + 100 where name = 'B'";
            statement = connection.prepareStatement(sql2);
            statement.executeUpdate();

            // 业务完毕，提交事务
            connection.commit();
            System.out.println("提交成功！");


        } catch (SQLException e) {
            try {
                // 如果失败，则回滚事务
                // 实际上不写，也会默认回滚，不用显式定义
                connection.rollback();
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
            e.printStackTrace();
        } finally {
            JdbcUtils.releaseResource(connection, statement, resultSet);
        }
    }
}

```








## 数据库连接池
* 传统数据库连接：数据库连接-执行完毕-释放，实际上连接和释放非常占用系统资源
* **池化技术**：准备一些预先的资源，过来就先连接准备好的
* 最小连接数：根据常用连接数来设定
* 最大连接数：业务最高的承载上限
* 超过最大连接数则排队
* 等待超时则不再等待了
* 编写一个连接池，实现一个**接口 DataSource**

### 开源数据源实现
使用这些数据库连接池之后，就不用再写连接数据库的代码了
* DBCP
* C3P0
* Druid

### DBCP需要用到的jar包
* commons-dbcp-1.4
* commons-pool-1.6

#### 代码实战
* `dbcpconfig.properties` 配置文件
```properties
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true&serverTimezone=Asia/Shanghai
username=root
password=Wby785403310

#<!-- ????? -->
initialSize=10

#??????
maxActive=50

#<!-- ?????? -->
maxIdle=20

#<!-- ?????? -->
minIdle=5

#<!-- ???????????? 6000??/1000??60? -->
maxWait=60000
#JDBC?????????????????????????????=property;?
#???"user" ? "password" ????????????????????????
connectionProperties=useUnicode=true;characterEncoding=UTF8

#??????????????????auto-commit????
defaultAutoCommit=true

#driver default ????????????????read-only????
#???????????setReadOnly?????????????????????????Informix?
defaultReadOnly=

#driver default ??????????????????TransactionIsolation??
#??????????????javadoc??NONE,READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
defaultTransactionIsolation=READ_UNCOMMITTED
```

* `JdbcUtils_DBCP.java` 工具类
```Java
package com.swagger.lesson05.utils;

import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

// 数据库连接 工具类
public class JdbcUtils_DBCP {

    private static DataSource dataSource = null;

    static {
        try {
            InputStream in = JdbcUtils_DBCP.class.getClassLoader().getResourceAsStream("dbcpconfig.properties");
            Properties properties = new Properties();
            properties.load(in);


            // 创建数据源 工厂设置模式 --》 创建对象
            // 有池化技术，性能会高很多
            dataSource = BasicDataSourceFactory.createDataSource(properties);


        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 获取连接
    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection(); // 从数据源中获取连接
    }

    // 释放连接资源
    public static void releaseResource(Connection conn, Statement st, ResultSet rs){
        if (rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (st != null){
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

}
 
```

* `TestDBCP.java` 测试代码
```Java
package com.swagger.lesson05.utils;

import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

// 数据库连接 工具类
public class JdbcUtils_DBCP {

    private static DataSource dataSource = null;

    static {
        try {
            InputStream in = JdbcUtils_DBCP.class.getClassLoader().getResourceAsStream("dbcpconfig.properties");
            Properties properties = new Properties();
            properties.load(in);


            // 创建数据源 工厂设置模式 --》 创建对象
            // 有池化技术，性能会高很多
            dataSource = BasicDataSourceFactory.createDataSource(properties);


        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 获取连接
    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection(); // 从数据源中获取连接
    }

    // 释放连接资源
    public static void releaseResource(Connection conn, Statement st, ResultSet rs){
        if (rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (st != null){
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

}

```

### C3P0需要用到的jar包
* c3p0-0.9.5.5
* mchange-commons-java-0.2.19


#### 代码实战
* `c3p0-config.xml` 配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>

<c3p0-config>
    <!--
    c3p0的缺省（默认）配置
    如果在代码中"ComboPooledDataSource ds=new ComboPooledDataSource();"这样写就表示使用的是c3p0的缺省（默认）
    -->
    <default-config>
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true;characterEncoding=utf8;uesSSL=true;serverTimezone=UTC</property>
        <property name="user">root</property>
        <property name="password">Wby785403310</property>
        <property name="acquireIncrement">5</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">5</property>
        <property name="maxPoolSize">20</property>
    </default-config>


    <name-config name="MySQL">
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbcstudy?userUnicode=true;characterEncoding=utf8;useSSL=true;serverTimezone=UTC</property>
        <property name="user">root</property>
        <property name="password">Wby785403310</property>
        <property name="acquireIncrement">5</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">5</property>
        <property name="maxPoolSize">20</property>
    </name-config>

</c3p0-config>
```

* `JdbcUtils_C3P0.java` 工具类
```Java
 package com.swagger.lesson05.utils;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class JdbcUtils_C3P0 {
    private static DataSource dataSource = null;

    static {
        try {
            // 创建数据源 工厂设置模式 --》 创建对象
            // 有池化技术，性能会高很多
             dataSource = new ComboPooledDataSource("MySQL"); // 配置文件写法


        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 获取连接
    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection(); // 从数据源中获取连接
    }

    // 释放连接资源
    public static void releaseResource(Connection conn, Statement st, ResultSet rs){
        if (rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (st != null){
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

}

```

* `TestC3P0.java` 测试代码
```Java
package com.swagger.lesson05;

import com.swagger.lesson05.utils.JdbcUtils_C3P0;
import com.swagger.lesson05.utils.JdbcUtils_DBCP;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.util.Date;

public class TestC3P0 {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement pst = null;

        try {
            // 使用C3P0 数据源
            connection = JdbcUtils_C3P0.getConnection();

            // 区别
            // 使用 ? 占位符替代参数
            String sql = "INSERT into users(`id`, `name1`, `psd`, `email`, `birthday`)" +
                    "VALUES(?, ?, ?, ?, ?);";

            // 预编译SQL
            pst = connection.prepareStatement(sql);

            // 手动为参数赋值
            pst.setInt(1, 9);
            pst.setString(2 ,"wby9");
            pst.setString(3, "467879");
            pst.setString(4, "21431421@qq.com");
            // 注意点：sql.Date 数据库
            //        util.Date Java new Date().getTime() 获得时间戳
            pst.setDate(5, new java.sql.Date(new Date().getTime()));

            // 执行
            int i = pst.executeUpdate();
            if (i > 0){
                System.out.println("插入成功");
            }


        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils_C3P0.releaseResource(connection, pst, null);
        }
    }
}

```









