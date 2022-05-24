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

## UDP消息发送
* 不用连接上，就可以发送消息，只用知道对方的地址
### 客户端
* 建立一个UDP Socket
* 建一个数据包
* 指明packet中的参数，说明发送给谁
* 发送数据包
* 关闭流close
* 不需要服务器端打开就可以发送消息，不保证消息的一定可达，所以直接运行客户端是不报错的
```Java
package com.swagger.lesson03;

import java.io.IOException;
import java.net.*;

public class UdpClientDemo01 {
    public static void main(String[] args) throws IOException {
        // 1 建立一个UDP Socket
        DatagramSocket socket = new DatagramSocket();
        // 2 建一个数据包
        String msg = "你好服务器";

        // 3 发送给谁
        InetAddress inetAddress = InetAddress.getByName("localhost");
        int port = 9090;
        DatagramPacket packet = new DatagramPacket(msg.getBytes(), 0, msg.getBytes().length, inetAddress, port);

        // 4 发送数据包
        socket.send(packet);

        // 5 关闭流
        socket.close();

        // 不需要服务器端打开就可以发送消息，不保证消息的一定可达，所以直接运行客户端是不报错的
    }
}

```

### 接收端
* 开放端口
* 接收数据包，并且打印接收的数据
* 关闭连接
* 实际上不存在TCP中的服务端，因为服务端也可以用客户端的方式发送消息
```Java
package com.swagger.lesson03;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

// 只有保持服务端在监听状态，才能接收到数据包
public class UdpServerDemo01 {
    public static void main(String[] args) throws IOException {
        // 1 开放端口
        DatagramSocket socket = new DatagramSocket(9090);
        // 2 接收数据包
        byte[] buffer = new byte[1024];
        DatagramPacket packet = new DatagramPacket(buffer, 0, buffer.length);

        socket.receive(packet);

        System.out.println(packet.getAddress().getHostAddress());
        System.out.println(new String(packet.getData(), 0, packet.getLength()));

        // 3 关闭连接
        socket.close();


    }
}

```

## UDP聊天实现 
### 发送方
* 新建一个UDP Socket
* 新建一个Buffer Reader 用于读取发送的消息
* 新建一个UDP Packet ，并将发送的数据填入其中，发送
* 如果检测到bye，字样的消息，则结束
* 消息可以循环发送
* 最后关闭socket
```Java
package com.swagger.lesson04_chat;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
import java.net.SocketException;

public class UdpSenderDemo01 {
    public static void main(String[] args) throws IOException {
        DatagramSocket socket = new DatagramSocket(8888);

        // 准备数据：控制台读取数据 System.in
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));

        while (true){
            String data = reader.readLine();
            byte[] datas = data.getBytes();

            InetAddress inetAddress = InetAddress.getByName("localhost");
            int port = 6666;

            DatagramPacket packet = new DatagramPacket(datas, 0, datas.length, inetAddress, port);

            socket.send(packet);

            if (data.equals("bye")){
                break;
            }

        }
        socket.close();

    }
}
```

### 接收方
* 新建一个UDP Socket
* 新建一个UDP Packet，并将接收到的数据填入到容器中
* 阻塞式接收包裹 `socket.receive(packet);`
* 使用packet中的getData 取出接收的数据
* 打印出接收的消息
* 检测如果消息为bye，则停止接收消息
```Java
package com.swagger.lesson04_chat;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

public class UdpReceiverDemo01 {
    public static void main(String[] args) throws IOException {
        DatagramSocket socket = new DatagramSocket(6666);

        while (true){
            // 准备接收包裹
            byte[] container = new byte[1024];
            DatagramPacket packet = new DatagramPacket(container, 0, container.length);
            socket.receive(packet); // 阻塞式接收包裹

            // 断开连接 如果为bye，则断开连接
            byte[] data = packet.getData();
            String receive_data = new String(data, 0, packet.getLength());
            System.out.println(receive_data);

            if (receive_data.equals("bye")){
                break;
            }


        }

        socket.close();
    }
}
```






## UDP多线程实现咨询
* 多线程实现
* 聊天的双方都需要有发送线程和接收线程
* 互相之间的发送线程和接收线程需要对应

### 发送线程类
* 实现Runnable接口即可
```Java
package com.swagger.lesson04_chat;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
import java.net.InetSocketAddress;

public class TalkSend implements Runnable{

    DatagramSocket socket = null;
    BufferedReader reader = null;

    private int fromPort;
    private String toIP;
    private int toPort;

    public TalkSend(int fromPort, String toIP, int toPort) {
        this.fromPort = fromPort;
        this.toIP = toIP;
        this.toPort = toPort;

        try{
            socket = new DatagramSocket(this.fromPort);
            reader = new BufferedReader(new InputStreamReader(System.in));
        }catch (Exception e){
            e.printStackTrace();
        }

    }

    @Override
    public void run() {

        while (true){

            try{
                String data = reader.readLine();
                byte[] datas = data.getBytes();

                InetAddress inetAddress = InetAddress.getByName("localhost");
                int port = 6666;

                DatagramPacket packet = new DatagramPacket(datas, 0, datas.length, new InetSocketAddress(this.toIP, this.toPort));

                socket.send(packet);

                if (data.equals("bye")){
                    break;
                }

            }catch (Exception e){
                e.printStackTrace();
            }

        }

        socket.close();

    }
}
```

### 接收线程类
* 实现Runnable 接口
```Java
package com.swagger.lesson04_chat;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;

public class TalkReceive implements Runnable{
    DatagramSocket socket = null;
    private int port;
    private String msgFrom;

    public TalkReceive(int port, String msgFrom) {
        this.port = port;
        this.msgFrom = msgFrom;
        try{
            socket = new DatagramSocket(port);
        }catch (Exception e){
            e.printStackTrace();
        }
    }

    @Override
    public void run() {

        while (true){


            try {
                // 准备接收包裹
                byte[] container = new byte[1024];
                DatagramPacket packet = new DatagramPacket(container, 0, container.length);
                socket.receive(packet); // 阻塞式接收包裹

                // 断开连接 如果为bye，则断开连接
                byte[] data = packet.getData();
                String receive_data = new String(data, 0, packet.getLength());
                System.out.println(msgFrom+":"+receive_data);

                if (receive_data.equals("bye")){
                    break;
                }
            } catch (IOException e) {
                e.printStackTrace();
            }

        }

        socket.close();

    }
}

```

### 聊天双方
* 均同时创建发送和接收线程
* 总共四个线程之间在通信
```Java

package com.swagger.lesson04_chat;

public class TalkStudent {
    public static void main(String[] args) {
        // 开启线程
        // 静态代理模式
        new Thread(new TalkSend(777, "localhost", 9999)).start();
        new Thread(new TalkReceive(8888, "老师")).start();

    }
}
```

```Java
package com.swagger.lesson04_chat;

public class TalkTeacher {
    public static void main(String[] args) {
        // 开启线程
        // 静态代理模式
        new Thread(new TalkSend(5555, "localhost", 8888)).start();
        new Thread(new TalkReceive(9999, "学生")).start();

    }
}
```

