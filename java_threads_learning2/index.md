# Java多线程学习2


## 静态代理模式
* 真实对象和代理对象都要实现同一个接口
* 代理对象要代理真实角色
* 优点，代理对象无法做很多真实对象无法做的事，真实对象可以专注于自己的事情
* 实际上Thread实现了Runnable接口，对线程实现代理

### 婚庆公司代理结婚
```java
package MultiThread_learning;

public class TestStaticProxy {
    public static void main(String[] args) {
        you swagger = new you();
        WeddingCompany weddingCompany = new WeddingCompany(swagger);
        weddingCompany.HappyMarry();
    }
}

interface Marry{
    void HappyMarry();
}

// 真实角色，实现接口（新郎本人）
class you implements Marry{
    @Override
    public void HappyMarry() {
        System.out.println("swagger is going to marry");
    }
}

// 代理角色，代理实现接口（婚庆公司）
class WeddingCompany implements Marry{
    private Marry target;

    WeddingCompany(Marry target) {
        this.target = target;
    }

    @Override
    public void HappyMarry() {
        before();
        this.target.HappyMarry();
        after();
    }

    private void after() {
        System.out.println("pay money");
    }

    private void before() {
        System.out.println("prepare the wedding procedure");
    }
}
```
* 婚庆公司代理新郎结婚
* 新郎可以专注于自己的事情
```java
new Thread(()-> System.out.println("I love you")).start();
new WeddingCompany(new you()).HappyMarry(); 
```
* 如上述代码，Thread代理输出I love you进行执行start()方法

## Lambda表达式
* JDK8才出现
* lambda 表达式只有实现接口部分代码的核心只有一句话是才能简化成一行，如果有多行，则需要花括号进行代码快包裹
* 前提是为函数式接口
* 多个参数也可以去掉参数类型，只能同时都去掉，活着同时都不去掉，必须加上括号

```java
new Thread(()-> System.out.println("I love you")).start();
```
### 使用lambda表达式的原因
* 避免匿名内部类的定义过多
* 使代码更简介
* 去掉没有意义的代码，只留下核心逻辑

### 函数接口定义
* 任何接口，如果只包含唯一一个抽象方法，那么他就是一个函数式接口
![](/image_Thread/pic3.png)
* 函数式接口，可以通过lambda表达式创建该接口，

### 具体实现1
```java
package MultiThread_learning;

// 推导lambda表达式
public class TestLambda {

    //3 静态内部类
    static class Like2 implements ILike{
        @Override
        public void lambda() {
            System.out.println("i like lambda2");
        }
    }

    public static void main(String[] args) {
        // 声明一个接口，使用实现类Like来new
        ILike like = new Like();
        like.lambda();
        
        // 静态内部类
        like = new Like2();
        like.lambda();

        // 4 局部内部类
        class Like3 implements ILike{
            @Override
            public void lambda() {
                System.out.println("i like lambda3");
            }
        }

        like = new Like3();
        like.lambda();

        // 5 匿名内部类
        like = new ILike() {
            @Override
            public void lambda() {
                System.out.println("i like lambda4");
            }
        };
        like.lambda();

        // 6 用lambda表达式简化
        like = ()->{
            System.out.println("i like lambda5");
        };
        like.lambda();

    }
}

// 1 定一个函数式接口
interface ILike{
    void lambda();
}

// 2 定义一个实现类
class Like implements ILike{
    @Override
    public void lambda() {
        System.out.println("i like lambda");
    }
}
```

### 具体实现2
```java
package MultiThread_learning;

public class TestLambda2 {

    public static void main(String[] args) {

        // 原版lambda 表达式
        Ilove love = (int a)-> {
            System.out.println("i love you" + " " + a + 1);
        };

        // 简化1：省略参数类型
        love = (a)->{
            System.out.println("i love you" + " " + a + 2);
        };

        // 简化2：简化花括号
        love = a -> {
            System.out.println("i love you" + " " + a + 3);
        };

        // 简化3：去掉花括号
        love = a -> System.out.println("i love you" + " " + a + 4);

        love.love(1);
    }
}

interface Ilove {
    void love(int a);
}
```

## 线程的五大状态
![](/image_Thread/pic4.png)
* 具体内容如下 
![](/image_Thread/pic5.png)

## 线程方法
![](/image_Thread/pic6.png)

## 停止线程
* 不推荐使用JDK提供的stop()，destroy()方法，已经废弃了
* 推荐线程自己停下来，利用次数，不建议死循环
* 建议使用一个标志位进行终止变量，比如当flag=false时，终止线程运行

### 具体实现
```java
package MultiThread_learning;

public class TestStop implements Runnable{
    // 1 设置一个标志位
    private boolean flag = true;

    @Override
    public void run() {
        int i = 0;
        while (flag){
            System.out.println("run.....Thread" + i++);
        }
    }

    // 2 设置一个公开的方法停止线程，转换标志位，方法为自己定义
    public void mystop(){
        this.flag = false;
    }

    public static void main(String[] args) {
        TestStop testStop = new TestStop();
        new Thread(testStop).start();

        for (int i = 0; i < 1000; i++) {
            System.out.println("main" + i);
            if (i == 900){
                testStop.mystop();
                System.out.println("线程停止了");
            }
        }
    }
}

```

## 线程休眠
* sleep(时间毫秒)
* sleep存在异常InterruptedException
* sleep时间到达后线程进入就绪状态，等待CPU调度
* sleep可以模拟网络延迟，倒计时等
* 每一个对象都有一个锁，sleep不会释放锁

### 具体实现1
* 模拟网络延迟：放大线程的发生性
```java
package MultiThread_learning;

public class TestSleep implements Runnable{
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
        TestSleep testSleep = new TestSleep();

        new Thread(testSleep, "小明").start();
        new Thread(testSleep, "老师").start();
        new Thread(testSleep, "黄牛党").start();
    }
}
```

### 具体实现2
* 模拟倒计时
```java
package MultiThread_learning;

// 模拟倒计时
public class TestSleep2 {

    public static void main(String[] args) {
        // 主线程 main的倒计时
        try {
            tenDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    // 模拟倒计时
    public static void tenDown() throws InterruptedException {
        int num = 10;
        while (true){
            Thread.sleep(1000);
            System.out.println(num--);
            if (num <= 0){
                break;
            }
        }

    }

}
```

### 具体实现3 
* 隔一段时长打印系统时间
```java
package MultiThread_learning;

import java.text.SimpleDateFormat;
import java.util.Date;

public class TestSleep3 {

    public static void main(String[] args) {
        // 打印当前系统时间
        Date startTime = new Date(System.currentTimeMillis());

        while (true){
            try {
                Thread.sleep(1000);
                System.out.println(new SimpleDateFormat("HH:mm:ss").format(startTime));
                // 更新系统时间
                startTime = new Date(System.currentTimeMillis());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    // 模拟倒计时
    public static void tenDown() throws InterruptedException {
        int num = 10;
        while (true){
            Thread.sleep(1000);
            System.out.println(num--);
            if (num <= 0){
                break;
            }
        }
    }
}
```
![](/image_Thread/pic7.png)


## 线程礼让
* 让当前只在执行的线程礼让，但不阻塞
* 让线程从运行状态转为就绪状态
* 让CPU重新调度，礼让不一定成功，看CPU心情

### 具体实现
* 如果礼让成功，则a线程开始执行后b线程应该也开始执行
* 如果礼让不成功，则a线程执行完之后b线程再执行
```java
package MultiThread_learning;

// 测试礼让
public class TestYield {
    public static void main(String[] args) {
        MyYield myYield = new MyYield();

        new Thread(myYield, "a Thread").start();
        new Thread(myYield, "b Thread").start();
    }
}

class MyYield implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "线程开始执行");
        Thread.yield();
        System.out.println(Thread.currentThread().getName() + "线程停止执行");
    }
}
```
![](/image_Thread/pic8.png)
* 说明礼让成功





