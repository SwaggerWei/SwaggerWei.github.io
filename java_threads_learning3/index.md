# Java多线程学习3


## 线程强制执行
* Join合并线程，此线程执行完成后再执行其他线程，其他线程阻塞
* 可以想像成插队
* 线程中尽量少使用次方法，会让其他线程陷入阻塞
### 具体实现
```java
package MultiThread_learning;

// 测试join方法,想象为插队
public class TestJoin implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 500; i++) {
            System.out.println("线程VIP来了" + i);
        }
    }

    public static void main(String[] args) {
        // 启动线程
        TestJoin testJoin = new TestJoin();
        Thread thread = new Thread(testJoin);
        thread.start();

        // 主线程
        for (int i = 0; i < 500; i++) {
            if (i == 200){
                try {
                    thread.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("main" + i);
        }
    }
}
```
![](/image_Thread/pic9.png)

## 线程状态观测（Thread.State）
* NEW：尚未启动的线程
* RUNNABLE：在执行中的线程
* BLOCKED：被阻塞等待监视器锁定的线程处于次状态
* WAITING：正在等待另一个线程执行特定动作的线程处于次状态
* TIMED_WAITING：正在等待另一个线程执行动作达到到指定等待时间的线程处于次状态
* TERMINATED：已退出的线程

### 观测线程状态具体实现
```java
package MultiThread_learning;

// 观察线程状态
public class TestState {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("////////");
        });

        // 观察线程状态 NEW
        Thread.State state = thread.getState();
        System.out.println(state.toString());

        // 观察线程启动后状态 RUNNABLE
        thread.start();
        state = thread.getState();
        System.out.println(state.toString());

        while (state != Thread.State.TERMINATED){// 只要线程不终止，就一直出状态
            Thread.sleep(100);
            state = thread.getState();//更新线程状态
            System.out.println(state.toString());
        }
    }
}
```
![](/image_Thread/pic10.png)  
![](/image_Thread/pic11.png)  
* 线程在死亡之后无法在启动

## 线程优先级
* Java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程，线程调度器按照优先级决定应该调度哪个线程来执行
* 线程的优先级用数字表示，范围从1～10
* Thread.MIN_PRIORITY = 1
* Thread.MAX_PRIORITY = 10
* Thread.NORM_PRIORITY = 5
* 获取优先级：getPriority()
* 修改优先级：setPriority()
* 需要先设置优先级，再启动

### 具体实现
```java
package MultiThread_learning;

// 测试线程优先级
public class TestPriority {
    public static void main(String[] args) {
        // 主线程优先级
        System.out.println(Thread.currentThread().getName() + "--->" + Thread.currentThread().getPriority());

        MyPriority myPriority = new MyPriority();

        Thread thread1 = new Thread(myPriority);
        Thread thread2 = new Thread(myPriority);
        Thread thread3 = new Thread(myPriority);
        Thread thread4 = new Thread(myPriority);
        Thread thread5 = new Thread(myPriority);
        Thread thread6 = new Thread(myPriority);

        // 设置优先级
        thread1.start();

        thread2.setPriority(1);
        thread2.start();

        thread3.setPriority(4);
        thread3.start();

        thread4.setPriority(Thread.MAX_PRIORITY);
        thread4.start();

        thread5.setPriority(2);
        thread5.start();

        thread6.setPriority(8);
        thread6.start();

    }
}

class MyPriority implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "--->" + Thread.currentThread().getPriority());
    }
}
```
![](/image_Thread/pic12.png)  
* 实际上优先级高的不一定先跑
* 最终的执行还是看CPU调度器的心情
* 可能CPU会执行优先级低的线程（性能倒置）

## 守护（daemon）线程
* 线程分为**用户线程**和**守护线程**
* 虚拟机必须确保用户线程执行完毕
* 虚拟机不用等待守护线程执行完毕
* 如：后台记录操作日志，监控内存，垃圾回收等待
* 守护线程参数默认为false 表示为用户线程，正常线程都为用户线程 
```java
thread.setDaemon(true); // 默认为false 表示为用户线程，正常线程都为用户线程 
```

## 线程同步机制（并发）
* 多个线程操作同一个资源
* 并发，多个线程在同一时刻操作同一个对象
* 实际上线程同步就是一种等待机制，多个同时需要访问一个对象的线程需要进入对象等待池形成队列，等待前面的线程使用完毕，下一个再使用
* 一个线程在使用数据对象时需要对对象进行加锁，防止其他线程进入篡改
* 每个对象都有一把锁，sleep不会释放锁，所以其他的线程依旧无法使用此对象

### 锁机制 
* 访问时加入锁机制**synchronized**
* 当一个线程获得了对象的排他锁，独占资源，其他线程必须等待，使用后释放锁即可

### 锁机制带来的问题
* 一个线程持有锁会导致其他所有需要此锁的线程挂起
* 多线程竞争时，加锁个释放锁会导致比较多的上下文切换和调度延时，引起性能问题
* 如果一个优先级高的线程等待一个优先级低的线程释放锁，会导致优先级倒置，引起性能问题

### 不安全案例1
```java
package MultiThread_learning.syncron;

// 线程不安全，出现负数
// 不安全的买票
public class UnSafeBuyTicket {
    public static void main(String[] args) {
        BuyTicket buyTicket = new BuyTicket();

        new Thread(buyTicket, "swagger").start();
        new Thread(buyTicket, "psyche").start();
        new Thread(buyTicket, "dad").start();
    }
}

class BuyTicket implements Runnable{
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

    private void buy(){
        // 判断是否有票
        if (ticketNums < 0){
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
![](/image_Thread/pic13.png)  
* 每个线程都有自己的CPU内工作内存，不加锁会造成票数为负数的情况

### 不安全案例2
```java
package MultiThread_learning.syncron;

// 不安全的取钱
// 两个人去银行取钱，账户出问题
public class UnsafeBank {
    public static void main(String[] args) {
        Account account = new Account(100, "基金");

        Withdrawing swagger = new Withdrawing(account, 50, "swagger");
        Withdrawing psyche = new Withdrawing(account, 100, "psyche");

        swagger.start();
        psyche.start();
    }
}

// 账户
class Account{
    int money;
    String name;

    Account(int money, String name) {
        this.money = money;
        this.name = name;
    }
}

// 银行：模拟取款
class Withdrawing extends Thread{
    Account account;
    int withdrawNums;
    int nowMoney;

    public Withdrawing(Account account, int WithdrawNums, String name){
        super(name);
        this.account = account;
        this.withdrawNums = WithdrawNums;
        this.nowMoney = 0;
    }

    @Override
    public void run() {
        if (account.money - withdrawNums < 0){
            System.out.println(Thread.currentThread().getName() + "要取钱，" + "基金钱不够，取不了了");
            return;
        }

        // 延时，放大问题发生性
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        account.money = account.money - withdrawNums;
        nowMoney = nowMoney + withdrawNums;

        System.out.println(account.name + "账户余额为" + this.account.money);
        System.out.println(Thread.currentThread().getName() + "手里的钱为" + this.nowMoney);
    }
}
```
![](/image_Thread/pic14.png)  

### 线程不安全集合
```java
package MultiThread_learning.syncron;

import java.util.ArrayList;
import java.util.List;

// 线程不安全的集合
public class UnsafeList {
    public static void main(String[] args) {
        List<String> strings = new ArrayList<String>();
        // 创建10000个线程, 加入到数组当中
        for (int i = 0; i < 10000; i++) {
            new Thread(()->{
                strings.add(Thread.currentThread().getName());
            }).start();
        }
        // 最后的数组size和线程数量不一致
        System.out.println(strings.size());
    }
}
```
* 数组为线程不安全的
* 说明有线程在同一瞬间操作了同一个地址，导致最后数组size和线程数量不一致
![](/image_Thread/pic15.png)  

