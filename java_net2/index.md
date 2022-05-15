# Java网络编程学习2


## TCP 实现文件上传
### 客户端
* 创建一个Socket连接
* 创建一个输出流，用于发送TCP连接中的数据
* 创建一个文件输入流fis，用于读取文件
* 通过文件输入流fis，将文件写出到输出流os中
* 通过shutdownOutput提示服务器已经传输完毕
* 确定服务器已经接收完毕了
* 最后关闭资源，顺序和创建顺序相反  
* tips：注意尽量不要抛出，否则会有很多异常需要抛出，尽量用try catch来处理
```Java
package com.swagger.lesson02;

import java.io.*;
import java.net.InetAddress;
import java.net.Socket;
import java.net.UnknownHostException;
// TCP上传文件 客户端发送
public class TCPClientDemo02 {
    public static void main(String[] args) throws IOException {
        // 尽量不要抛出，否则会有很多异常需要抛出

        // 1 创建一个Socket连接
        Socket socket = new Socket(InetAddress.getByName("127.0.0.1"), 9000);
        // 2 创建一个输出流
        OutputStream os = socket.getOutputStream();
        // 3 读取文件
        FileInputStream fis = new FileInputStream(new File("pic.png"));
        // 4 写出文件 到输出流 os中去
        byte[] buffer = new byte[1024];
        int len;
        while ((len = fis.read(buffer)) != -1){
            os.write(buffer);
        }

        // 5 通知服务器，我已经结束了
        socket.shutdownOutput();// 我已经传输完了

        // 6 确定服务端接收完毕了
        InputStream inputStream = socket.getInputStream();
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] buffer2 = new byte[1024];
        int len2;
        while ((len2 = inputStream.read(buffer2)) != -1){
            baos.write(buffer2);
        }
        System.out.println(baos.toString());


        // 7 关闭资源，关闭顺序和创建顺序相反
        baos.close();
        inputStream.close();
        fis.close();
        os.close();
        socket.close();

    }
}

```

### 服务端
* 创建一个服务
* 监听客户端的请求连接，阻塞式监听，会一直等待客户端的连接请求
* 读取客户端的消息, 获取输入流
* 读取文件，将文件进行输出
* 通知客户端我已经接收完了
* 关闭资源
```Java
package com.swagger.lesson02;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

// TCP上传文件 服务端接收端
public class TCPServerDemo02 {
    public static void main(String[] args) throws IOException {
        // 1 创建一个服务
        ServerSocket serverSocket = new ServerSocket(9000);
        // 2 监听客户端的请求连接
        // 阻塞式监听，会一直等待客户端的连接请求
        Socket socket = serverSocket.accept();
        // 3 读取客户端的消息, 获取输入流
        InputStream is = socket.getInputStream();
        // 4 读取文件，将文件进行输出
        FileOutputStream fileOutputStream = new FileOutputStream(new File("received.png"));
        byte[] buffer = new byte[1024];
        int len;
        while ((len = is.read(buffer)) != -1){
            fileOutputStream.write(buffer, 0, len);
        }

        // 5 通知客户端我已经接收完了
        OutputStream outputStream = socket.getOutputStream();
        outputStream.write("我接受完毕了，你可以断开了".getBytes());

        // 6 关闭资源
        fileOutputStream.close();
        is.close();
        socket.close();
        serverSocket.close();
    }
}

```







## Tomcat
* tomcat是一个免费的，开放源代码的Web应用服务器，是Apache软件基金会项目中的一个核心项目，由Apache ，Sun和一些公司以及个人共同开发而成，深受Java爱好者的喜爱，是一款比较流行的web应用服务器
* 可以自定义服务端和客户端，就像前文的TCP实现消息传输和文件上传一样
* 也可以使用已经写好的开源Web应用服务项目Tomcat

### Tomcat 核心组件
* web容器:完成web服务器的功能
* servlet容器:名字为catalina，用于处理Servlet代码。
* JSP容器:用于将JSP动态网页翻译成Servlet代码
* 因此Tomcat**是web应用服务器，也是一个Servlet/JSP容器**。Tomcat作为 Servlet容器，负责处理客户请求，把请求传送给servlet，并将servlet的响应传送回给客户

### 什么是servlet
Servlet 是 Java Servlet 的简称，可以理解为是一个**服务连接器**，**是用Java编写的服务器端程序**，具有独立于平台和协议的特性，简单的理解: **servlet就是一个中间件，包含了接口和方法**，将客户端和数据库连接，从而实现动态网页的创建

### 什么是JSP
* JSP 全称 Java Server Pages，是一种动态网页开发技术。它使用JSP 标签在HTML,网页中插入Java代码。标签通常以 <% 开头，以 %> 结束。
* JSP是一种Java servlet，主要用于实现Java web 应用程序的用户界面部分
* JSP通过网页表单获取用户输入数据、访问数据库及其他数据源，然后动态地创建网页

### Tomcat 功能组件结构
* 负责接收和反馈外部请求的连接器Connector，和负责处理请求的容器Container
* 连接器和容器相辅相成，一起构成了基本的 web服务Service。每个Tomcat服务器可以管理多个Service
* Conector: 负责对外接收和响应请求。它是Tomcat与外界的交通枢纽，监听端口接收外界请求，并将请求处理后传递给容器做业务处理，最后将容器处理后的结果响应给外界
* Container: 负责对内处理业务逻辑。其内部由 Engine、Host、Context和wrapper 四个容器组成，用于管理和调用Servlet相关逻辑
* Service:对外提供的web服务。主要包含Connector和 Container两个核心组件，以及其他功能组件。Tomcat可以管理多个Service，且各Service 之间相互独立

### Container结构分析
每个Service 会包含一个Container容器。在Container内部包含了4个子容器:4个子容器的作用分别是  
* Engine:引擎，用来管理多个虚拟主机，一个Service最多只能有一个Engine；
* Host:代表一个虚拟主机，也可以叫站点，通过配置 Host 就可以添加站点
* Context:代表一个 web 应用，包含多个Servlet封装器
* wrapper:封装器，容器的最底层。每一Wrapper封装着一个Servlet，负责对象实例的创建、执行和销毁功能
* Engine、Host、Context和 wrapper，这四个容器之间属于父子关系。
* 容器由一个引擎可以管理多个虚拟主机。每个虚拟主机可以管理多个web应用。每个web应用会有多个Servlet封装器

### Tomcat请求过程
* 用户在浏览器中输入网址，请求被发送到本机端口8080，被在那里监听的 Connector获得；
* Connector 把该请求交给它所在的Service 的 Engine (Container）来处理，并等待Engine的回应;
* 请求在Engine、Host、Context和Wwrapper 这四个容器之间层层调用，最后在Servlet 中执行对应的业务逻辑、数据存储等
* 执行完之后的请求响应在Context、Host、Engine容器之间层层返回，最后返回给Connector，并通过Connector 返回给客户端



