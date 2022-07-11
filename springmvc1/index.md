# Spring学习2


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










