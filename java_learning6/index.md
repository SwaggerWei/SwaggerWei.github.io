# Java 学习6-反射


## 6 反射
反向获取class中各种对象的信息、例如成员变量、方法、构造方法、包等等信息

### 6.1 创建一个对象的过程
![在这里插入图片描述](https://img-blog.csdnimg.cn/7365942536844f148d7a5b1768fe877e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* new来实例一个对象时jvm会在加载对应的class文件
* jvm在本地磁盘中查找class文件
* 将class文件读入到内存当中，以便使用
* 一个class 文件只占用个同一个空间，对此用一个class再new，不会重复在内存空间中初始化

### 6.2 获取类的字节码
```java
//获取类的字节码
//        方法一
        Student stu1 = new Student();
        Class stuClass = stu1.getClass();
        System.out.println(stuClass.toString());
        System.out.println(stuClass.getName());

//        方法二
        Class stuClass2 = Student.class;
        System.out.println(stuClass2.toString());
        System.out.println(stuClass2 == stuClass);

//        方法三
        Class stuClass3 = Class.forName("reflectionDemo.Student");
        System.out.println(stuClass3.toString());
```

### 6.3 反射得到有参数的构造函数
```java
//        反射得到有参数构造函数
        Constructor c = stuClass3.getConstructor(String.class);
        c.newInstance("wby");
```

### 6.4 反射得到私有构造函数
```java
//        反射得到私有构造函数
        Constructor c4Private = stuClass3.getDeclaredConstructor(int.class);
        c4Private.setAccessible(true);
        c4Private.newInstance(20);
```

### 6.5 反射得到所有的构造方法
```java
Constructor[] c4All = stuClass3.getDeclaredConstructors();
        for (Constructor c1 : c4All){
            System.out.println(c1);
        }
```

### 6.6 反射得到类中的普通方法
```java
Student p = (Student) stuClass3.newInstance();
        Method m = stuClass3.getMethod("Student1", String.class, int.class);
        m.invoke(p, "wby", 20);
```

### 6.7 反射类中的属性字段
```java
Field f = stuClass3.getField("name");
        String s = (String) f.get(p);
        System.out.println(s);
```

### 6.8 应用场合、缺点
* 在编译时根本无法知道该对象或类可能属于哪些类，程序只依靠运行时信息来发现该对象和类的真实信息
* 使用反射基本上是一种解释操作，用于字段和方法接入时要远慢于直接代码
* 使用反射会模糊程序内部逻辑


