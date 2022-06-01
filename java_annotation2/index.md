# Java Annotation注解和反射2


## 获取类的运行时结构
### 可以获取到的结构
* 实现的全部接口
* 所继承的父类
* 全部的构造器
* 全部的方法
* 全部的Field
* 注解

### 代码实战
```Java
package com.swagger.reflection;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

// 获取类的所有信息
public class test06 {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException {
        Class c1 = Class.forName("com.swagger.reflection.User");

        // 获取类的名字
        System.out.println(c1.getName()); // 获取包名+类名
        System.out.println(c1.getSimpleName()); // 获取类名

        // 获取类的属性
        System.out.println("=================");
        Field[] fields = c1.getFields(); // 只能找到 public的属性
        for (Field field : fields) {
            System.out.println(field);
        }

        Field[] fields2 = c1.getDeclaredFields(); // 可以找到所有的属性
        for (Field field : fields2) {
            System.out.println(field);
        }

        // 获取指定属性的方法
        Field name = c1.getDeclaredField("name");
        System.out.println(name);

        // 获得类的方法
        System.out.println("==================");
        Method[] methods = c1.getDeclaredMethods(); // 获取本类的所有方法
        for (Method method : methods) {
            System.out.println(method);
        }

        methods = c1.getMethods(); // 获取本类和父类的所有public方法啊
        for (Method method : methods) {
            System.out.println(method);
        }

        // 获取指定方法
        // 第二个参数是指定方法的所需要的参数列表，因为要考虑到重载的情况
        System.out.println("======================");
        Method getName = c1.getMethod("getName", null);
        Method setName = c1.getMethod("setName", String.class);
        System.out.println(getName);
        System.out.println(setName);

        // 获取构造器
        System.out.println("======================");
        Constructor[] constructors = c1.getConstructors();
        for (Constructor constructor : constructors) {
            System.out.println(constructor);
        }

        constructors = c1.getDeclaredConstructors();
        for (Constructor constructor : constructors) {
            System.out.println(constructor);
        }

        // 获取指定构造器
        Constructor constructor = c1.getConstructor(String.class, int.class, int.class);
        System.out.println(constructor);

        constructor = c1.getDeclaredConstructor(String.class, int.class, int.class);
        System.out.println(constructor);


    }
}

```


## 动态创建对象的执行方法
* 反射获取类的方法时候如何将其利用起来
* 实际操作中，获取类信息的操作代码并不会经常开发
* 一定要熟悉java.lang.reflect包的作用，反射机制
* 如何获取属性、方法、构造器的名称、修饰符等

### 创建类的对象基本步骤
* 获取class对象
* 通过构造器（无参或者有参都是可以的）构造一个对象
* 实例化对象

### 通过反射调用类中的方法
* 使用Class类的getMethod方法获取得到一个Method对象
* 使用invoke激活，并向方法中传入要设置obj对象的参数信息
* 如果方法为私有，则需要将方法对象的**setAccessible设置为真，这样才可以访问到（破坏了封装性）**

### 代码实战
```Java
package com.swagger.reflection;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

// 动态创建对象，通过反射
public class test07 {
    public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
        // 获得class对象
        Class<?> c1 = Class.forName("com.swagger.reflection.User");

        // 构造一个对象
        User user = (User) c1.newInstance(); // 本质上是调用的无参数构造器
        System.out.println(user);

        // 通过构造器构造一个对象
        Constructor<?> constructor = c1.getDeclaredConstructor(String.class, int.class, int.class);
        User user1 = (User) constructor.newInstance("swagger", 001, 18);
        System.out.println(user1);

        // 通过反射调用普通方法
        User user2 = (User) c1.getDeclaredConstructor().newInstance();
        // 通过反射获取一个普通方法
        Method setName = c1.getDeclaredMethod("setName", String.class);
        // invoke 激活
        // 反射操作方法
        setName.invoke(user2, "swagger");
        System.out.println(user2.getName());

        // 通过反射操作属性
        System.out.println("===============");
        User user4 = (User) c1.getDeclaredConstructor().newInstance();
        Field name = c1.getDeclaredField("name");

        name.setAccessible(true); // 取消操作私有属性的安全检测，降低程序效率
        name.set(user4, "swagger2");
        System.out.println(user4.getName());


    }
}
```





## 性能对比分析
![](/image_Java_Annotation/pic3.png)
* new方法的方法最快
* 普通反射方式最慢
* 开放访问权限的反射方式快于普通反射，但是还是大幅落后于new方法

### 代码实战
```Java
package com.swagger.reflection;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

// 性能分析
public class test10 {

    public static void main(String[] args) throws InvocationTargetException, NoSuchMethodException, IllegalAccessException {
        func1();
        func2();
        func3();
    }

    // 普通方法调用
    public static void func1(){
        User user = new User();

        long startTime = System.currentTimeMillis();

        for (int i = 0; i < 1000000000; i++) {
            user.getName();
        }

        long endTime = System.currentTimeMillis();

        System.out.println("普通方法执行10亿次：" + (endTime - startTime) + "ms");
    }

    // 反射方法调用

    public static void func2() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        User user = new User();
        Class<? extends User> c1 = user.getClass();

        Method getName = c1.getDeclaredMethod("getName", null);

        long startTime = System.currentTimeMillis();

        for (int i = 0; i < 1000000000; i++) {
            getName.invoke(user, null);
        }

        long endTime = System.currentTimeMillis();

        System.out.println("反射方法执行10亿次：" + (endTime - startTime) + "ms");
    }

    // 反射方法调用，关闭检测setAccessible(true)

    public static void func3() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        User user = new User();
        Class<? extends User> c1 = user.getClass();

        Method getName = c1.getDeclaredMethod("getName", null);
        getName.setAccessible(true);

        long startTime = System.currentTimeMillis();

        for (int i = 0; i < 1000000000; i++) {
            getName.invoke(user, null);
        }

        long endTime = System.currentTimeMillis();

        System.out.println("反射方法(关闭访问限制)执行10亿次：" + (endTime - startTime) + "ms");
    }
}
```

## 反射操作泛型
```Java
package com.swagger.reflection;

import java.lang.reflect.Method;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.util.List;
import java.util.Map;

// 通过反射获取泛型
public class test11 {

    public static void func1(Map<String, User> map, List<User> list){
        System.out.println("func1");
    }

    public Map<String, User> func2(){
        System.out.println("func2");
        return null;
    }

    public static void main(String[] args) throws NoSuchMethodException {
        Method func1 = test11.class.getMethod("func1", Map.class, List.class);

        Type[] genericParameterTypes = func1.getGenericParameterTypes();

        // 反射获取方法参数泛型

        for (Type genericParameterType : genericParameterTypes) {
            System.out.println("#" + genericParameterType);
            if (genericParameterType instanceof ParameterizedType){
                Type[] actualTypeArguments = ((ParameterizedType) genericParameterType).getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    System.out.println(actualTypeArgument);
                }
            }
        }

        // 反射获取方法返回值泛型

        System.out.println("=============");
        Method func2 = test11.class.getMethod("func2", null);
        Type genericReturnType = func2.getGenericReturnType();
        if (genericReturnType instanceof ParameterizedType){
            Type[] actualTypeArguments = ((ParameterizedType) genericReturnType).getActualTypeArguments();
            for (Type actualTypeArgument : actualTypeArguments) {
                System.out.println(actualTypeArgument);
            }
        }
    }

}
```

## 反射获取注解信息
### 实例
![](/image_Java_Annotation/pic4.png)
* ORM - Object Relationship Mapping 对象关系映射
* 类和表结构对应
* 属性和字段对应
* 对象和记录对应
* 要求：利用注解和反射完成类和表结构的映射关系

### 代码实战
```Java
package com.swagger.reflection;

import java.lang.annotation.*;
import java.lang.reflect.Field;

// 反射操作注解
public class test12 {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class<?> c1 = Class.forName("com.swagger.reflection.Student2");

        // 通过反射获得注解
        Annotation[] annotations = c1.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }

        // 获取注解value的值
        TableSwagger annotation = c1.getAnnotation(TableSwagger.class);
        System.out.println(annotation.value());

        // 获得类指定的注解
        Field name = c1.getDeclaredField("name");
        FieldSwagger annotation1 = name.getAnnotation(FieldSwagger.class);
        System.out.println(annotation1.columnName());
        System.out.println(annotation1.type());
        System.out.println(annotation1.length());
    }
}

@TableSwagger("db_student")
class Student2{

    @FieldSwagger(columnName = "db_id", type = "int", length = 10)
    private int id;
    @FieldSwagger(columnName = "db_age", type = "int", length = 10)
    private int age;
    @FieldSwagger(columnName = "db_name", type = "varchar", length = 3)
    private String name;

    public Student2(int id, int age, String name) {
        this.id = id;
        this.age = age;
        this.name = name;
    }

    public Student2() {
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student2{" +
                "id=" + id +
                ", age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
}

// 类名的注解
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface TableSwagger{
    String value();
}

// 属性的注解
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@interface FieldSwagger{
    String columnName();
    String type();
    int length();
}
```






