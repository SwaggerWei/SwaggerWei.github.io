# Java 学习4-异常



## 4 异常
异常机制是指当程序出现错误后，程序如何处理。具体来说，异常机制提供了程序退出的安全通道。当出现错误后，程序执行的流程发生改变，程序的控制权转移到异常处理器。

### 4.1 程序错误的分类
* 编译错误：编译错误是因为程序没有遵循语法规则，编译程序能够自己发现并且提示我们错误的原因和位置
* 运行时错误：程序在执行时，运行环境发现了不能执行的操作
* 逻辑错误：程序没有按照预期的逻辑顺序执行。异常也就是指程序运行时发生错误，而异常处理就是对这些错误进行处理和控制
![在这里插入图片描述](https://img-blog.csdnimg.cn/5b91968b9b8a40f8a5e194c1b618bdad.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* Throwable： 有两个重要的子类：Exception（异常）和 Error（错误），二者都是 Java 异常处理的重要子类，各自都包含大量子类。
* 异常和错误的区别是：异常能被程序本身处理，错误是无法处理

### 4.2 运行时异常和非运行时异常
* 运行时异常RuntimeException（不受检异常unchecked）：   
RuntimeException类极其子类表示JVM在运行期间可能出现的错误。编译器不会检查此类异常，并且不要求处理异常，比如用空值对象的引用（NullPointerException）、数组下标越界（ArrayIndexOutBoundException）。此类异常属于不可查异常，一般是由程序逻辑错误引起的，在程序中可以选择捕获处理，也可以不处理
* 非运行时异常，（受检异常checked）：  
  Exception中除RuntimeException极其子类之外的异常。编译器会检查此类异常，如果程序中出现此类异常，比如说IOException，必须对该异常进行处理，要么使用try-catch捕获，要么使用throws语句抛出，否则编译不通过。

### 4.3 异常的处理
* 抛出异常：throw，throws  
  throw用在方法内，用来抛出一个异常对象，将这个异常对象传递到调用者处，并结束当前方法的执行。  
  throws运用于方法声明之上，用于表示当前方法不处理异常，而是提醒该方法的调用者来处理异常
* 捕获异常：try，catch，finally

### 4.4 实例
* throw 的使用
```java
package ExceptionDemo;

public class TrowDemo {
    public static void main(String[] args) {
        int a = devide(4, 0);
        System.out.println(a);
    }
    public static int devide(int a, int b){
        if(b==0){
            throw new ArithmeticException("异常信息：除数不能为0");
        }
        return a/b;
    }
}
```

* throws 的使用
```java
package ExceptionDemo;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.InputStream;

public class ThrowsDemo {
  public static void main(String[] args) throws FileNotFoundException{
    try {
      //可能出现异常的代码段
      readFile();
    }catch (FileNotFoundException e){
      // 异常处理
      System.out.println("异常信息： 找不到指定路径");
    }
    System.out.println("后续处理");
  }
  public static void readFile() throws FileNotFoundException{
//        InputStream is = new FileInputStream("/Users/swaggerwei/Desktop/look4Job/Java_language/notes/docks");
    InputStream is = new FileInputStream("/Users/swaggerwei/Desktop/notes/docks");
  }
}
```
* 自定义异常类
```java
package ExceptionDemo;
/*
* 自定义异常类
* 用户不存在异常
* */

public class UserNotExistException extends RuntimeException{
    public UserNotExistException(){
        super();
    }
}
```

