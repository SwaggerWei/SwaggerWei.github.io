# Java多线程学习4


## 同步方法和同步块
* 同步方法：`public synchronized void method(int args){}`
* 使用synchronized方法控制对对象的访问
* 每个对象都有一把锁，synchronized方法必须要获得对象的锁才能够执行，否则线程阻塞
* 一旦得到该对象锁，同步方法才开始执行，直到方法返回才释放锁，后面阻塞的线程才能获得这个锁，继续执行

### 同步方法的弊端
* 缺陷：如果将一个大的方法声明为synchronized 将会影响效率
* 方法里面需要写的部分才需要加锁，读不用加锁，锁太多会浪费资源

### 同步块
* 同步块`synchronized(Obj){}`
* Obj为同步监视器，Obj可以是任何对象，推荐使用共享资源作为同步监视器
* 同步方法中无需指定监视器，因为监视器就是this，这个对象本身
#### 同步监视器的执行过程
* 第一个线程访问，锁定同步监视器，执行其中代码
* 第二个线程访问，发现同步监视器被锁定，无法访问
* 第一个线程访问完毕，解锁同步监视器
* 第二个线程访问，发现同步监视器没有锁，然后锁定之后进行访问

### 具体实现（同步方法）--安全地买票
```Java
package MultiThread_learning.syncron;

public class SafeBuyTicket {
    public static void main(String[] args) {
        SafeBuy safeBuy = new SafeBuy();

        new Thread(safeBuy, "swagger").start();
        new Thread(safeBuy, "psyche").start();
        new Thread(safeBuy, "dad").start();
    }
}

class SafeBuy implements Runnable{
    // 票
    private int ticketNums = 10;
    boolean flag = true;

    @Override
    public void run() {
        // 买票操作
        while(flag){
            buy();
        }
    }

    private synchronized void buy(){
        // 判断是否有票
        if (ticketNums <= 0){
            flag = false;
            return;
        }

        // 模拟延时
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // 买票
        System.out.println(Thread.currentThread().getName() + "拿到票" + ticketNums--);
    }
}
```

* 将买票这个方法加上synchronized 修饰符之后就变成了同步方法

### 具体实现（同步代码块） ---安全地取钱
```Java
package MultiThread_learning.syncron;

public class SafeBank {
    public static void main(String[] args) {
        SafeAccount safeAccount = new SafeAccount(100, "结婚基金");

        SafeWithdraw swagger = new SafeWithdraw(safeAccount, 50, "swagger");
        SafeWithdraw psyche = new SafeWithdraw(safeAccount, 100, "psyche");

        swagger.start();
        psyche.start();
    }
}

// 账户
class SafeAccount{
    int money;
    String name;

    SafeAccount(int money, String name) {
        this.money = money;
        this.name = name;
    }
}

// 银行：模拟取钱
class SafeWithdraw extends Thread{
    SafeAccount safeAccount;
    int withdrawNums;
    int nowMoney;

    public SafeWithdraw(SafeAccount safeAccount, int withdrawNums, String name){
        super(name);
        this.safeAccount = safeAccount;
        this.withdrawNums = withdrawNums;
        this.nowMoney = 0;
    }

    @Override
    public void run() {

        synchronized (safeAccount){
            if(safeAccount.money - withdrawNums < 0){
                System.out.println(Thread.currentThread().getName() + "要取钱，" + "基金钱不够，取不了了");
                return;
            }

            // 延时，放大问题发生性
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            safeAccount.money = safeAccount.money - withdrawNums;
            nowMoney = nowMoney + withdrawNums;

            System.out.println(safeAccount.name + "账户余额为" + this.safeAccount.money);
            System.out.println(Thread.currentThread().getName() + "手里的钱为" + this.nowMoney);
        }
    }
}
```

* 代码块中地加锁参数只要为多个线程需要增删改地对象即可
* 这里结婚基金为多个线程修改，所以需要在run方法中加锁

### 具体实现（同步代码块） ---安全的集合
```Java
package MultiThread_learning.syncron;

import java.util.ArrayList;
import java.util.List;

public class SafeList {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();

        for (int i = 0; i < 10000; i++) {
            new Thread(()->{
                synchronized (list){
                    list.add(Thread.currentThread().getName());
                }
            }).start();
        }

        System.out.println(list.size());
    }
}
```

* list被多个线程同时修改
* 对list进行加锁，就不会出问题


## JUC（Java Util Concurrent）
* Java 并发包
* 此包里的类都是线程安全的，不用再使用synchronized
```Java
package MultiThread_learning.syncron;

import java.util.concurrent.CopyOnWriteArrayList;

public class TestJUC {
    public static void main(String[] args) throws InterruptedException {
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        for (int i = 0; i < 10000; i++) {
            new Thread(()->{
                list.add(Thread.currentThread().getName());
            }).start();
        }

        Thread.sleep(3000);

        System.out.println(list.size());
    }
}
```

## 死锁
* 两个活着多个线程都在等待对方释放资源
* 具体来说：一个同步块同时拥有两个以上的对象锁，就有可能发生死锁
### 产生死锁的四个条件
* 互斥：一个资源每次只能被一个线程使用
* 请求与保持：一个进程因请求资源而阻塞时，对已获得的资源保持不放
* 不剥夺：线程已获得的资源，在未使用完之前，不能强行剥夺
* 循环等待条件：若干进行之间形成一种头尾相接的循环等待资源关系

### 具体实现，产生死锁
```Java
package MultiThread_learning.syncron;

// 多个线程互相抱着对方的资源，形成僵持
public class DeadLock {
    public static void main(String[] args) {
        MakeUp makeUp1 = new MakeUp(0, "swagger");
        MakeUp makeUp2 = new MakeUp(1, "psyche");

        makeUp1.start();
        makeUp2.start();
    }

}

class Lipstick{
}

class Mirror{
}

class MakeUp extends Thread{
    // 需要只有一份的资源
    static Lipstick lipstick = new Lipstick();
    static Mirror mirror = new Mirror();

    int choice;
    String girlName;

    MakeUp(int choice, String girlName){
        this.choice = choice;
        this.girlName = girlName;
    }

    @Override
    public void run() {
        super.run();
        try {
            makeup();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    private void makeup () throws InterruptedException {
        if (choice == 0){
            synchronized (lipstick){
                System.out.println(this.girlName + "get lipstick's lock");
                Thread.sleep(1000);
                synchronized (mirror){
                    System.out.println(this.girlName + "get mirror's lock");
                }
            }
        }else {
            synchronized (mirror){
                System.out.println(this.girlName + "get mirror's lock");
                Thread.sleep(2000);
                synchronized (lipstick){
                    System.out.println(this.girlName + "get lipstick's lock");
                }
            }
        }
    }
}
```
* 产生死锁，程序停不下来
* 互相想要得到对方资源的锁
* 但是不放开自己手中资源的锁

### 具体实现，解开死锁
```Java
package MultiThread_learning.syncron;

// 多个线程互相抱着对方的资源，形成僵持
public class DeadLock {
    public static void main(String[] args) {
        MakeUp makeUp1 = new MakeUp(0, "swagger");
        MakeUp makeUp2 = new MakeUp(1, "psyche");

        makeUp1.start();
        makeUp2.start();
    }

}

class Lipstick{
}

class Mirror{
}

class MakeUp extends Thread{
    // 需要只有一份的资源
    static Lipstick lipstick = new Lipstick();
    static Mirror mirror = new Mirror();

    int choice;
    String girlName;

    MakeUp(int choice, String girlName){
        this.choice = choice;
        this.girlName = girlName;
    }

    @Override
    public void run() {
        super.run();
        try {
            makeup();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    private void makeup () throws InterruptedException {
        if (choice == 0){
            synchronized (lipstick){
                System.out.println(this.girlName + "get lipstick's lock");
                Thread.sleep(1000);
            }
            synchronized (mirror){
                System.out.println(this.girlName + "get mirror's lock");
            }
        }else {
            synchronized (mirror){
                System.out.println(this.girlName + "get mirror's lock");
                Thread.sleep(2000);
            }
            synchronized (lipstick){
                System.out.println(this.girlName + "get lipstick's lock");
            }
        }
    }
}
```
* 解开死锁
* 获取对方资源的锁之前，放开手中持有资源的锁

## Lock（锁）
* 显式定义同步锁
* JUC包下的内容 java.util.concurrent.locks
* Lock对象开始访问共享资源之前应先活着Lock对象
* ReentrantLock类实现了Lock，他拥有和synchronized相同的并发性和内存语义
* 在实现线程安全控制中，比较常用的是ReentrantLock，可以显式地加锁和释放锁
* 如果有异常，需要将解锁放入到finally之中

### lock和synchronized 的区别
* lock 是显示锁（手动开启和关闭）；synchronized是隐式锁，出了作用域自动关闭
* Lock 只能锁代码块，synchronized既可以锁方法，也可以锁代码块
* 使用Lock，JVM将花费较少的时间来调度线程，性能更好，并且扩展性更好
* 优先使用顺序：Lock > 同步代码块（已经进入了方法体，非配了相应资源）> 同步方法（在方法体之外）

### 具体实现
```Java
package MultiThread_learning.SeniorMethod;

import java.util.concurrent.locks.ReentrantLock;

// 测试Lock锁
public class TestLock {

    public static void main(String[] args) {
        TestLock2 testLock2 = new TestLock2();

        new Thread(testLock2).start();
        new Thread(testLock2).start();
        new Thread(testLock2).start();

    }
}

class TestLock2 implements Runnable{

    int ticketNums = 10;

    // 定义Lock锁
    private final ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true){
            try {
                // 加锁
                lock.lock();

                if(ticketNums > 0){
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(ticketNums--);
                }else {
                    break;
                }
            }finally {
                // 解锁
                // 如果有异常，需要将解锁放入到finally之中
                lock.unlock();
            }
        }
    }
}
```

## 线程通信

### 生产者消费者问题
![](/image_Thread/pic16.png)
* 仓库只能存放一件产品，生产者将生产出来的产品放入仓库，消费者将仓库中的产品取走消费
* 对于生产者，如果仓库中没有产品，则生产者将产品放入仓库，如果仓库中已经有产品，生产者停止生产，直到产品被消费者取走为止
* 对于消费者，仓库中如果放有产品，消费者可以将产品取走，否则停止消费并等待，直到仓库中有产品为止
* 生产者消费者问题之中synchronized是不够的，只能加锁，但是无法实现通信

### Java 线程通信问题的方法
![](/image_Thread/pic17.png)
* 注意：局势Object类的方法，都只能在同步方法和同步代码块中使用，否则会抛出IllegalMonitorStateException异常

## 通信解决方式1-管程法
![](/image_Thread/pic18.png)
* 设置一个缓冲区

### 具体实现
```Java
package MultiThread_learning.SeniorMethod;

/**
 * @author swaggerwei
 * @version 1.0
 * @since 1.8
 */

// 测试：生产者消费者问题  利用缓冲区（管程法）
public class TestPandC {
    public static void main(String[] args) {
        SynContainer container = new SynContainer();

        new Producer(container).start();
        new Consumer(container).start();
    }
}

// 生产者
class Producer extends Thread{
    SynContainer container;

    public Producer(SynContainer container){
        this.container = container;
    }

    @Override
    public void run() {
        super.run();

        for (int i = 0; i < 100; i++) {
            try {
                container.push(new Chicken(i));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("生产了" + i + "只鸡");
        }
    }
}

// 消费者
class Consumer extends Thread{
    SynContainer container;

    public Consumer(SynContainer container){
        this.container = container;
    }



    @Override
    public void run() {
        super.run();
        for (int i = 0; i < 100; i++) {
            try {
                System.out.println("消费了" + container.pop().id + "只鸡");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

// 产品
class Chicken{
    int id;// 产品编号

    public Chicken(int id) {
        this.id = id;
    }
}

// 缓冲区
class SynContainer{

    // 容器大小
    Chicken[] chickens = new Chicken[10];
    // 容器计数器
    int count = 0;


    // 生产者上货
    public synchronized void push(Chicken chicken) throws InterruptedException {
        // 如果容器满了，就要等待消费者消费
        if(count == chickens.length){
            // 通知消费者消费，生产等待
            this.wait();
        }

        // 如果没有满，就要上货
        chickens[count] = chicken;
        count++;

        // 可以通知消费这消费了
        this.notifyAll();

    }

    // 消费者消费产品
    public synchronized Chicken pop() throws InterruptedException {
        // 判断是否可以消费
        if(count == 0){
            this.wait();
            // 等待生产者生产，消费者等待
        }

        // 如果可以消费
        count--;
        Chicken chicken = chickens[count];

        // 通知生产者开始上货
        this.notifyAll();
        return chicken;

    }
}
```

## 通信解决方式2-信号灯法
* 设置一个标志位

### 具体实现

```Java
package MultiThread_learning.SeniorMethod;

/**
 * @author swaggerwei
 * @since 1.8
 */

// 生产者消费者问题
// 信号灯法解决

public class TestPandC2 {
    public static void main(String[] args) {
        TV tv = new TV();
        new Player(tv).start();
        new Audience(tv).start();
    }
}

// 生产者---演员
class Player extends Thread{
    TV tv;
    public Player(TV tv){
        this.tv = tv;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            if (i%2 == 0){
                try {
                    this.tv.play("甄嬛传");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }else {
                try {
                    this.tv.play("天下足球");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

// 消费者---观众
class Audience extends Thread{
    TV tv;
    public Audience(TV tv){
        this.tv = tv;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            try {
                tv.watch();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

// 产品---节目
class TV{
    // 演员表演，观众等待
    // 观众观看，演员等待
    String voice;//表演节目
    boolean flag = true;

    // 表演
    public synchronized void play(String voice) throws InterruptedException {
        if (!flag){
            this.wait();
        }

        System.out.println("演员表演了节目：" + voice);

        // 通知观众观看
        this.voice = voice;
        this.notifyAll();
        this.flag = !this.flag;
    }

    // 观看
    public synchronized void watch() throws InterruptedException {
        if(flag){
            this.wait();
        }

        System.out.println("观众观看了" + voice);
        // 通知演员表演
        notifyAll();
        this.flag = !this.flag;
    }
}
 
```

## 线程池
* 经常创建和销毁线程非常浪费资源，对性能影响很大
* 提前创建好多个线程池，使用时直接获取，使用完放回池中
* 优点：提高了相应速度
* 优点：降低了资源消耗，重复利用线程池中线程，不需要重复创建
* 便于线程管理：corePoolSize核心池大小, maximumPoolSize最大线程数，keepAliveTime线程没有任务时最多保持多长时间后终止

### 具体使用
* JDK5 开始提供线程池相关的API：ExecutorService 和 Executors
* ExecutorService为真正的线程池接口，常见的子类ThreadPoolExecutor
* Executors为工具类，线程池的工厂类，用于创建并返回不同类型的线程池

### 具体实现
```Java
package MultiThread_learning.SeniorMethod;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ForkJoinPool;

/**
 * @author swaggerwei
 * @since 1.8
 * @version 1.0
 */
// 测试线程池
public class TestPool {
    public static void main(String[] args) {
        // 1 创建线程池, 参数为线程池的大小（线程的数目）
        ExecutorService service = Executors.newFixedThreadPool(10);

        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());

        // 关闭链接
        service.shutdown();

    }
}

class MyThread implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```

![](/image_Thread/pic19.png)




















