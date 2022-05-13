# Java网络编程学习1


## 网络编程的必要条件
* 定位互联网上的一台主机 192.168.16.124:port
* 定位到计算机的某个资源
* 找到了这个主机，如果传输数据（UDP， TCP）

## 混淆概念
* JavaWeb：网页编程 B/S
* TCP/IP：网络编程 C/S

## 网络通信的要素 
### 通信双方的地址
* IP地址（唯一）
* 端口号
### 规则，网络通信协议
![](/image_Net/pic1.png)
* HTTP：互联网网络通信协议（超文本传输协议）
* FTP：文件上传协议
* SMTP: QQ邮件的发送 
* Telnet：远程登陆相关 
* DNS：域名解析
* 网络层的东西为封包使用
* 数据链路层有无线也有有线
### 两个主要问题
* 如何主要定位到一个和几个主机
* 找到主机之后如何进行通信
### 网络编程重的要素
* IP和端口号-----IP
* 网络通信协议------UDP，TCP协议

## IP
* java.net.InetAddress 都是和网络编程相关的内容
* 唯一定位一台网络中的计算机
* `127.0.0.1` localhost 默认本机ip地址 
* IP地址分为广域网和局域网
* **IPv4**：形如`127.0.0.1`，由四个字节组成，每个字节的范围为0-255，大概有42亿个，30亿都在北美，亚洲4亿。2011年就用尽了
* **IPv6**：形如`fe80::2852:63ff:fe79:8013%anpi0`, 128 位，8个无符号整数 `2001:0bb2:aaaa:0015:0000:0000:1aaa:1312`
* 局域网：`192.168.xx.xx`
* IP地址四大分类：ABCD类
* 域名：IP地址是一串数字，难以记忆，所以可以通过申请域名的方式，得到www.vip.com此类的域名地址，方便记忆

### InetAddress 测试源码
```Java
package com.swagger.lesson01;

import java.net.InetAddress;
import java.net.UnknownHostException;

// IP
public class TestInetAddress {
    public static void main(String[] args) throws UnknownHostException {
        // 查询本机地址
        // InetAddress 没有构造函数，不能被new出来，直接调用其中的静态方法即可
        InetAddress inetAddress = InetAddress.getByName("127.0.0.1");
        InetAddress inetAddress_local = InetAddress.getByName("localhost");
        InetAddress local_name = InetAddress.getLocalHost();
        System.out.println(inetAddress);
        System.out.println(inetAddress_local);
        System.out.println(local_name);

        // 查询域名的IP地址
        InetAddress inetAddress2 = InetAddress.getByName("www.baidu.com");
        System.out.println(inetAddress2);

        // 常用方法
        System.out.println(inetAddress2.getAddress()); // 地址 通常为一组
        System.out.println(inetAddress2.getCanonicalHostName()); // 获取规范主机名
        System.out.println(inetAddress2.getHostAddress()); // ip
        System.out.println(inetAddress2.getHostName()); // 获取域名，或者自己的主机名

    }
}
```


## 端口 port
* 不同的进程有不同的端口号，用来区分软件
* 被规定为0-65535
* 分为TCP和UDP端口号，总共有0-65535 * 2，单个协议下端口号不能冲突

### 端口分类
#### 公有端口 0-1023
* HTTP：80
* HTTPS：443
* FTP：21
* Telnet：23
#### 程序注册端口 1014-49151 分配给用户或者程序的
* Tomcat：8080
* MySQL：3306
* ORACLE：1521
#### 动态、私有 49152 - 65535
* IDEA HTML 默认端口：63342
#### 常见的命令-MacOS
* 查看端口占用情况：`netstat -AaLlnW` 
* 查看指定端口情况：`lsof -i:80`

## 通信协议 
* 统一的通信标准
* TCP：传输控制协议
* UDP：用户数据报协议
* IP：网路互联协议，实际上 TCP/IP是一个协议簇

## TCP 实现聊天
### 客户端
* 得到服务器的地址 
* 创建一个Socket连接
* 通过IO流发送消息
```Java
package com.swagger.lesson02;

import java.io.IOException;
import java.io.OutputStream;
import java.net.InetAddress;
import java.net.Socket;
import java.net.UnknownHostException;

// 客户端
public class TCPClientDemo01 {
    public static void main(String[] args)  {

        Socket socket = null;
        OutputStream os = null;

        try {
            // 1 得到服务器的地址
            InetAddress serverIp = InetAddress.getByName("127.0.0.1");
            // 2 端口号
            int port = 9999;
            // 3 创建一个socket 链接
            socket = new Socket(serverIp, port);
            // 4 发送消息 IO流
            os = socket.getOutputStream();
            os.write("你好，欢迎学习javanet".getBytes());


        } catch (UnknownHostException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (os != null){
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (socket != null){
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }



    }
}
```
### 服务器
* 建立服务的端口 ServerSocket
* 等待用户连接过来 accept
* 读取客户端的消息
```Java
package com.swagger.lesson02;

import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;

// 服务器段
public class TCPServerDemo01 {
    public static void main(String[] args) {

        ServerSocket serverSocket = null;
        Socket socket = null;
        InputStream is = null;
        ByteArrayOutputStream baos = null;

        try {
            // 1 有一个地址
            serverSocket = new ServerSocket(9999);
            // 2 等待客户端链接过来
            socket = serverSocket.accept(); // 这里即为得到的客户端socket
            // 3 读取客户端的消息
            is = socket.getInputStream();
            // 4 管道流, 将输入的is 进行管道过滤
            baos = new ByteArrayOutputStream();
            byte[] buffer = new byte[1024];
            int len;
            while ((len = is.read(buffer)) != -1){
                baos.write(buffer, 0, len);
            }
            System.out.println(baos.toString());


        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 5 关闭资源
            if (baos != null){
                try {
                    baos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (is != null){
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (socket != null){
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (serverSocket != null){
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}
```



