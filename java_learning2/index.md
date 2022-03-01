# Java 学习2-继承和接口



## 1 继承（关键字extends）
* 父类中私有成员可以被继承，只是外界无法访问
* 父类中公共属性、方法可以被子类继承
* 支持单继承，多重继承（单链式继承），不支持多继承（一个类继承多个父类）
* 子类中的方法重写必须是父类中已有的方法
* 重写后再次调用父类的方法使用关键字super  例如 super.需要调用父类中的方法;
* super必须在子类构造器中第一句  
* 重写与重载的区别
* 重写：方法名、参数跟父类方法一致，只是方法体内容不一样（运行时多态）  
* 重载：方法名一致，形参返回值不一致（编译时多态）
* Java中，在创建类时，都会默认继承Object类  
* 只要父类构造方法不发生重载（保留无参的） 子类无需改动
* 需要遵循先构造父类，在构造子类的顺序

## 2 接口
* 接口（英文：Interface），在JAVA编程语言中是一个抽象类型，是抽象方法的集合  
* 接口通常以interface来声明。一个类通过继承接口的方式，从而来继承接口的抽象方法  
* 接口无法被实例化，但是可以被实现

### 2.1 接口与类的异同点
* 接口不能用于实例化对象
* 接口没有构造方法
* 接口中所有的方法必须是抽象方法，Java 8 之后 接口中可以使用 default 关键字修饰的非抽象方法
* 接口不能包含成员变量，除了 static 和 final 变量
* 接口不是被类继承了，而是要被类实现
* 接口支持多继承
* 接口和类都可以有多个方法
* 文件名类似

### 2.2 实例
```java
package objectOriented;
//接口demo

public interface Animal {
    static int height = 0;
    public void eat();
    public void travel();
}
```

```java
package objectOriented;

public class testAnimal_interface implements Animal{
    public void eat(){
        System.out.println("tiger eat");
    }

    public void travel(){
        System.out.println("tiger travel");
    }

    public int numOfLegs(){
        return 0;
    }

    public static void main(String[] args) {
        testAnimal_interface tiger = new testAnimal_interface();
        tiger.eat();
        tiger.travel();
        System.out.println(tiger.numOfLegs());
    }
}
```

