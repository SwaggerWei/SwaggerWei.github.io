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
* 推荐使用Runnable接口，避免单继承的局限性，还可以多个对象被一个线程使用，节省资源
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
#### 实例-下载图片
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
### 实现Runnable接口
```java
package MultiThread_learning;

import javax.crypto.spec.PSource;

public class TestThread3 implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("我在看代码--"+i);
        }
    }

    public static void main(String[] args) {
        // 创建runnable接口的实现类对象
        TestThread3 testThread3 = new TestThread3();
        // 创建线程对象，通过线程对象来开启线程，代理
        Thread thread =  new Thread(testThread3);
        thread.start();

        for (int i = 0; i < 20; i++) {
            System.out.println("我在学习多线程--"+i);
        }
    }
}

```
* 定义MyRunnable类实现Runnable接口
* 实现run()方法，编写线程执行体
* 创建线程对象，调用start()方法启动线程

## 并发问题--同一个对象被多个线程使用
```Java
package MultiThread_learning;

public class TestThread4 implements Runnable{
    private int ticketNums = 10;

    @Override
    public void run() {
        while (true){
            if(ticketNums <=0){
                break;
            }
            // 模拟延时
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // 买票
            System.out.println(Thread.currentThread().getName() + "拿到了第" + ticketNums-- + "票");
        }
    }

    public static void main(String[] args) {
        TestThread4 testThread4 = new TestThread4();

        new Thread(testThread4, "小明").start();
        new Thread(testThread4, "老师").start();
        new Thread(testThread4, "黄牛党").start();
    }
}
``` 
![](/image_Thread/pic2.png)
* 发现多个线程对同一个资源进行操作的时候，会出现并发问题，线程不安全，数据紊乱

## 龟兔赛跑案例
```java
package MultiThread_learning;

// 模拟归途赛跑
public class Race implements Runnable{
    private static String winner;

    @Override
    public void run() {
        for (int i = 0; i <= 100; i++) {

            // 模拟兔子休息, 10次休息1毫秒
            if (Thread.currentThread().getName().equals("兔子") && i%10 == 0){
                try {
                    Thread.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            // 判断比赛是否结束
            boolean flag = gameOver(i);
            // 如果比赛结束，停止程序
            if(flag){
                break;
            }
            System.out.println(Thread.currentThread().getName() + "-->跑了" + i + "步");
        }
    }

    private boolean gameOver(int steps){
    // 判断是否有胜利者

        if (winner != null){
            // 已经存在胜利者了
            return true;
        }else{
            if (steps >= 100){
                winner = Thread.currentThread().getName();
                System.out.println("winner is " + winner);
                return true;
            }
        }
        return false;
    }

    public static void main(String[] args) {
        Race race = new Race();

        new Thread(race, "兔子").start();
        new Thread(race, "乌龟").start();
    }
}

```

## 实现Callable接口
* 实现Callable接口需要返回值的类型
* 重写call方法，需要抛出异常
* 创建目标对象
* 创建执行服务
* 提交执行
* 获取结果
* 关闭服务

### 具体实现
```java
package MultiThread_learning;

import java.util.concurrent.*;

// 多线程的方式三， 实现callable接口
public class TestCallable implements Callable<Boolean> {
    public String url;
    public String name;

    public TestCallable(String url, String name){
        this.url = url;
        this.name = name;
    }

    @Override
    public Boolean call() throws Exception {
        WebDownloader webDownloader = new WebDownloader();
        webDownloader.downloader(url, name);
        System.out.println("下载了文件名为："+name);

        return true;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        TestCallable testCallable1 = new TestCallable("https://img-blog.csdnimg.cn/88eba0a511a84000b1e458e9aca123c7.png", "1.png");
        TestCallable testCallable2 = new TestCallable("https://img-blog.csdnimg.cn/88eba0a511a84000b1e458e9aca123c7.png", "2.png");
        TestCallable testCallable3 = new TestCallable("https://img-blog.csdnimg.cn/88eba0a511a84000b1e458e9aca123c7.png", "3.png");

        // 创建执行服务, 生成一个线程池
        ExecutorService ser = Executors.newFixedThreadPool(3);

        // 提交执行
        Future<Boolean> r1 = ser.submit(testCallable1);
        Future<Boolean> r2 = ser.submit(testCallable2);
        Future<Boolean> r3 = ser.submit(testCallable3);

        // 获取结果
        boolean rs1 = r1.get();
        boolean rs2 = r2.get();
        boolean rs3 = r3.get();

        // 关闭服务
        ser.shutdown();
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
### Callable特性
* 可以定义返回值
* 可以跑出异常
