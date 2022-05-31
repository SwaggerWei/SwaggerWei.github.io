# Java Annotation注解和反射1


## 注解 Annotation
![](/image_Java_Annotation/pic1.png)
* 如图所示，以`@`开头的修饰符
* 注释为comment，注意区分开，以 `//`开头

### 什么是注解 
* JDK5.0引入的新技术
* Annotation的作用：不是程度本身，可以对程序做出解释； 可以被其他程序（比如编译器）读取
* 格式：注解是用`@`开头的修饰符在代码中存在的，还可以添加一些参数值，例如@SuppressWarnings(value="unchecked")
* 哪里使用：可以附加在package，class，method，field等上面，相当于给他们添加了额外的辅助信息，我么你可以通过反射机制编程实现对这些元数据的访问

## 内置注解
* `@Override`：定义在java.lang.Override中，此注释只适用于修辞方法，表示一个方法声明打算重写超累中的另一个方法申明，只能用来**修饰方法**
* `@Deprecated`：定义在java.lang.Deprecated中，此注释可以用于修辞方法，表示不鼓励程序员使用，通常有更好的选择，但是可以使用
* `@SuppressWarnings`：抑制编译时的警告信息，这个内置注解需要参数，可以用来修饰**类或者方法**

### 代码测试
```Java
package com.swagger.annotation;

import java.util.ArrayList;
import java.util.List;

public class test01 {

    // @Override 重写注解，只能用来修饰方法
    @Override
    public String toString() {
        return super.toString();
    }

    // @Deprecated 不推荐程序员使用，但是可以使用，或者存在更好的方式
    @Deprecated
    public static void test(){
        System.out.println("Deprecated");
    }

    // @SuppressWarnings("all")，镇压警告，所有的警告都不提醒，也可以用来修饰类
    @SuppressWarnings("all")
    public static void test1(){
        List list = new ArrayList();
    }

    public static void main(String[] args) {
        test();
        test1();
    }
}
```

## 元注解 meta-annotation
元注解的作用就是负责注解其他注解，Java定一了4个标准的meta-annotation类型

### 分类
* `@Target`：注解的使用范围
* `@Retention`：什么级别保存该注解，用于描述注解的声明周期（SOURCE<CLASS<RUNTIME一般都是RUNTIME）
* `@Document`：该注释将会被包含在javadoc中
* `@Inherited`：说明子类可以继承父类中的该注解

### 代码
```Java
package com.swagger.annotation;

import java.lang.annotation.*;

// 测试元注解
public class test20 {

    @MyAnnotation
    public void test(){

    }
}

// 定一个一个注解
// Target表示注解可以用在那个地方
@Target(value = {ElementType.METHOD, ElementType.TYPE})

// Retention表示什么级别才有效
@Retention(value = RetentionPolicy.RUNTIME)

// Document表示是否将注解生成在javadoc中
@Documented

// Inherited 表示子类可以继承父类的注解
@Inherited
@interface MyAnnotation{

}

```

## 自定义注解
* 使用`@interface`自定义注解时，自动继承了java.lang.annotation.Annotation接口
* 格式 public @interface 注解名 {定义内容}
* 其中每一个方法实际上是声明了一个配置参数
* 方法的名称就是参数的名称
* 返回值类型就是参数的类型，返回值只能是基本类型（Class，String，enum）
* 可以通过default来声明参数的默认值
* 如果只有一个参数成员，一般参数名为value
* 注解元素必须要有值，我们定义注解元素是，经常使用空字符串，0作为默认值

### 实战代码
```Java
package com.swagger.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// 自定义注解
public class test03 {

    // 注解可以显式赋值，如果没有默认值，就一定要直接赋值
    @MyAnnotation2(name = "swagger", schools = {"1高", "12高"}) // 有默认值就可以不写
    public void test(){}

    @MyAnnotation3(value = "1")
    public void test2(){}

}

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotation2{
    // 注解的参数：参数类型 + 参数名（）
    String name() default "";
    int age() default 0;
    int id() default -1; // 如果默认值为-1，代表不存在
    String[] schools() default {"清华", "北大"};
}

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotation3{
    String value(); // 如果只有一个参数，直接使用value作为参数名, 并且使用时参数列表中可以省略value
}
```






## 反射概述
### 动态语言
* 运行的时候代码可以根据某些条件改变自身的结构
* 主要动态语言：Object-C、C#、JavaScript、PHP、Pyhton

### 静态语言
* 运行时结构不变的语言就是静态语言：Java、C、C++
* Java不是动态语言，但是可以称为准动态语言，可以通过反射机制让其活的类似动态语言的特性

### Java Reflection
* 反射机制运行程序在执行期借助于Reflection API去的任何类的内部信息，并能直接操作任意对象的内部属性及方法
* 加载完成类之后，在堆内存中就有一个Clss类型的对象，通过反射可以看到类的结构
![](/image_Java_Annotation/pic2.png)

### Java 反射机制提供的功能
* 运行时判断任意一个对象所属的类
* 运行时构造任意一个类的对象
* 运行时判断任意一个类所具有的方法和成员变量
* 运行时获取泛型信息
* 运行时调用任意一个对象的成员变量和方法
* 运行时**处理注解**
* **生成动态代理**

### 反射的优点和缺点
#### 优点
可以动态创建对象和编译，体现出很大的灵活性
#### 缺点
* 对性能有影响，为解释行操作，我们可以告诉JVM希望做什么并且它满足我们的要求。这类操作总是慢于直接执行相同的操作
* 破坏封装性，影响安全




## 反射获取对象
### class 类
* 对象照镜子之后可以得到的信息，某个类的属性、方法、构造器、某个类实现了哪些接口
* 对于每个类而言，JRE都为妻保留了一个不变的Class类型的对象。
* 一个Class对象包含了特定某个结构（class/interface/enum/annotation/primitive type/void/[]）的有关信息
* Class本身也是一个类
* Class对象只能由系统建立对象
* 一个加载的类在JVM中只会有一个Class实例
* 一个Class对象对应的是一个加载到JVM中的一个class文件
* 每个类的实例都会记得自己是由哪个Class实例所生成的
* 通过Class可以完整地得到一个类中所有被加载的结构
* Class类是Reflection的根源，针对任何你想动态加载、运行的类，唯有先获取相应的Class对象

### 如何获取Class类的实例
* 若已知具体的类，通过类的class属性获取（最为安全可靠，性能最高）`Class clazz = Person.class;`
* 已知某个类的实例，通过实例的getClass()方法获取Class对象 `Class clazz = person.getClass();`
* 已知一个类的全类名，且该类在类路径下，可通过Class类的静态方法forName()获取，可能抛出ClassNotFoundException异常 `Class clazz = Class.forName("demo.Student")`
* 内置基本数据类型可以直接用类名.Type
* 还可以利用ClassLoader

### 代码实战
```Java
package com.swagger.reflection;

public class test02 {
    public static void main(String[] args) throws ClassNotFoundException {
        Person person = new Student();
        System.out.println("这个人是：" + person.name);

        // 获取Class
        // 方法1： 通过对象获得
        Class c1 = person.getClass();
        System.out.println(c1.hashCode());

        // 方式2：forName获取
        Class c2 = Class.forName("com.swagger.reflection.Student");
        System.out.println(c2.hashCode());

        // 方式3：通过 类名.class
        Class c3 = Student.class;
        System.out.println(c3.hashCode());

        // 方法4：基本内置类型的包装类都有一个Type属性
        Class c4 = Integer.TYPE;
        System.out.println(c4);

        // 获得父类类型
        System.out.println(c1.getSuperclass());




    }
}

class Person{
    public String name;

    public Person() {
    }

    public Person(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                '}';
    }
}

class Student extends Person{
    public Student() {
        this.name = "学生";
    }
}

class Teacher extends Person{
    public Teacher() {
        this.name = "老师";
    }
}
```


## 所有类型的Class对象 
### 哪些类型可以有Class对象
* class：外部类、成员（成员内部类，静态内部类）、局部内部类、匿名内部类
* interface：接口
* []：数组
* enum：枚举
* annotation：注解@interface
* primitive type：基本数据类型
* void

### 代码实战
```Java
package com.swagger.reflection;

import java.lang.annotation.ElementType;

// 所有类型的Class对象
public class test03 {
    public static void main(String[] args) {
        Class c1 = Object.class; // 类
        Class c2 = Comparable.class; // 接口
        Class c3 = String[].class; // 一维数组
        Class c4 = int[][].class; // 二维数组
        Class c5 = Override.class; // 注解
        Class c6 = ElementType.class; // 枚举
        Class c7 = Integer.class; // 基本数据类型
        Class c8 = void.class; // void
        Class c9 = Class.class; // Class

        System.out.println(c1);
        System.out.println(c2);
        System.out.println(c3);
        System.out.println(c4);
        System.out.println(c5);
        System.out.println(c6);
        System.out.println(c7);
        System.out.println(c8);
        System.out.println(c9);

        // 只要元素类型与维度一样，就是同一个class对象
        int[] a = new int[10];
        int[] b = new int[100];
        System.out.println(a.getClass().hashCode());
        System.out.println(b.getClass().hashCode());

    }
}
```



## 类加载内存分析
* 1 加载：类的数据（静态变量、静态方法、常量迟、代码）加载到内存，会产生对应java.lang.Class对象对应方法去中加载的各个类
* 2 链接：分配内存，并输入一个默认的值
* 3 初始化：静态方法块合并起来
### 代码实战
```Java
package com.swagger.reflection;

// 类加载内存分析
public class test04 {
    public static void main(String[] args) {
        A a = new A();
        System.out.println(A.m);


        /*
        1 加载到内存，会产生一个类对应Class对象
        2 链接，链接结束后m的初始化值为0
        3 初始化, 静态代码块全部合并，执行后发现m为100
            <clinit>(){
                System.out.println("A类静态代码块初始化");
                m = 300;
                m = 100;
            }
         */
    }
}

class A{
    static {
        System.out.println("A类静态代码块初始化");
        m = 300;
    }

    static int m = 100;

    public A(){
        System.out.println("A类的无参构造方法初始化");
    }
}
```

## 分析类的初始化（何时一个类会被初始化）
### 主动初始化
* new 关键字：如果父类没有初始化，会主动初始化父类
* 反射：如果父类没有初始化，会主动初始化父类

### 被动初始化
* 当访问一个静态域时，只有真正声明这个域的类才会被初始化。如：当通过子类引用父类的静态变量时，不会导致子类的初始化
* 通过数组定义类的引用
* 引用常量(常量在链接阶段就已经存入调用类的常量池了)

### 代码实战
```Java
package com.swagger.reflection;

public class test05 {
    static {
        System.out.println("Main类被加载");
    }

    public static void main(String[] args) throws ClassNotFoundException {
        // 1 主动引用
        // new 关键字：如果父类没有初始化，会主动初始化父类
//        Son son = new Son();

        // 反射：如果父类没有初始化，会主动初始化父类
//        Class.forName("com.swagger.reflection.Son");

        // 3 被动引用
        // 当访问一个静态域时，只有真正声明这个域的类才会被初始化。如：当通过子类引用父类的静态变量时，不会导致子类的初始化
//        System.out.println(Son.b);

        // 通过数组定义类的引用
        Son[] Array = new Son[10];

        // 引用常量
        System.out.println(Son.x);

    }
}

class Father{
    static int b = 2;

    static {
        System.out.println("父类被加载");
    }

}

class Son extends Father{
    static {
        System.out.println("子类被加载");
        m = 300;
    }
    static int m = 100;
    static final int x = 1;
}
```
