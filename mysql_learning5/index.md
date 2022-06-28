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






