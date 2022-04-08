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
