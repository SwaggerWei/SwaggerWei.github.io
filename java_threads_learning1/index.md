# Java多线程学习1


## Java 多线程基础知识  
* 程序project：指令和数据的有序集合，静态概念
* 进程Process：程序的运行过程，**是系统资源分配的单位**，动态概念
* 线程Thread：一个进程中可以有多个线程，至少有一个线程，**线程是CPU调度和执行的单位**。
* 很多多线程是模拟出来的，真正的多线程是因为有多个cpu核，如果只有单核，实际上cpu一次只能执行一个线程，知识切换很快
* 程序运行时，即使没有自己创建线程，后台也会有多个线程（主线程和GC线程）
* 如果一个程序中有多个线程，线程运行的先后人为无法干预，都是由调度器安排调度（调度器与操作系统紧密相关）
* 对同一份资源操作时，会存在资源抢夺问题，需要加入并发控制
* 线程会带来额外开销，如CPU调度时间，并发控制开销等
* 每个线程在自己的工作内存交互，内存控制不当会造成数据不一致（结合JMM理解）

## 线程的创建
* Thread class：继承Thread类，重写其中的run方法
* Runnable接口：实现Runnable接口
* Callable接口：实现Callable接口
### Thread创建多线程
```java
package MultiThread_learning;

// 创建线程方法1：继承Thread类，重写run()方法，调用start开启线程
public class TestThread1 extends Thread{
    @Override
    public void run() {
        // run方法线程体
        for (int i = 0; i < 20; i++) {
            System.out.println("我在看代码"+i);
        }
    }

    public static void main(String[] args) {
        // 创建一个线程对象，调用start，开启线程
        TestThread1 testThread1 = new TestThread1();
        testThread1.start();
        // main线程，主线程
        for (int i = 0; i < 20; i++) {
            System.out.println("我在学习多线程"+i);
        }
    }
}
```
* 自定义线程类继承Thread类
* 重写run方法
* 创建一个线程对象，调用start，开启线程
* 如果在创建线程对象后调用start，则为多线程模式
* 如果在创建线程对象后调用run，则为单线程模式
* 线程不一定立即执行，CPU安排调度，所以每一次多线程运行的结果都可能不一样
### 实例-下载图片
```java
package MultiThread_learning;

import org.apache.commons.io.FileUtils;

import java.io.File;
import java.io.IOException;
import java.net.URL;

// 练习Threa，实现多线程同步下载图片
public class TestThread2 extends Thread{
    private String url; // 网络图片地址
    private String name; // 保存的文件名

    public TestThread2(String url, String name){
        this.url = url;
        this.name = name;
    }


    @Override
    public void run() {
        super.run();

        WebDownloader webDownloader = new WebDownloader();
        webDownloader.downloader(url, name);
        System.out.println("下载了文件名为："+name);
    }

    public static void main(String[] args) {
        TestThread2 t1 = new TestThread2("https://img-blog.csdnimg.cn/88eba0a511a84000b1e458e9aca123c7.png", "1.png");
        TestThread2 t2 = new TestThread2("https://img-blog.csdnimg.cn/88eba0a511a84000b1e458e9aca123c7.png", "2.png");
        TestThread2 t3 = new TestThread2("https://img-blog.csdnimg.cn/88eba0a511a84000b1e458e9aca123c7.png", "3.png");

        t1.start();
        t2.start();
        t3.start();
    }
}

// 下载器
class WebDownloader{
    public void downloader(String url, String name){
        try{
            FileUtils.copyURLToFile(new URL(url), new File(name));
        }catch(IOException e){
            e.printStackTrace();
            System.out.println("IO异常，downloader出现问题");
        }
    }
}


```
* 多线程下载一张图片，观察执行的先后顺序是否为单线程的顺序执行
![](/image_Thread/pic1.png)
* 可以观察到，执行顺序和单线程顺序执行不一致，实际为多线程同时执行



