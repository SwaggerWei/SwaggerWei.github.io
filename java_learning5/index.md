# Java 学习5-泛型



## 5 泛型
泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数

### 5.1 泛型方法
* 在调用时可以接收不同类型的参数
* 每一个类型参数声明部分包含一个或多个类型参数，参数间用逗号隔开
* 类型参数能被用来声明返回值类型
* 泛型方法体的声明和其他方法一样。注意类型参数只能代表引用型类型，不能是原始类型（像 int、double、char 等）

### 5.2 java 中范型标记符
* E - Element (在集合中使用，因为集合中存放的是元素)
* T - Type（Java 类）
* K - Key（键）
* V - Value（值）
* N - Number（数值类型）
* ？ - 表示不确定的 java 类型

### 5.3 类型通配符
* 类型通配符一般是使用 ? 代替具体的类型参数。例如 List<?> 在逻辑上是 List<String>,List<Integer> 等所有 List<具体类型实参> 的父类

### 5.4 实例1 --- 泛型方法
```java
package GenericsDemo;

public class genericDemo {
    public static < E > void printArray(E[] array){
        for(E element : array){
            System.out.println(element);
        }
    }

    public static void main(String[] args) {
        Integer[] intArray = {1 ,2, 3, 4, 5};
        Double[] doubleArray = {1.1, 2.1, 3.1, 4.1};
        Character[] charArray = {'a', 'b', 'c', 'd'};

        System.out.println("整型数组元素为：");
        printArray(intArray);
        System.out.println("双精度型数组元素为：");
        printArray(doubleArray);
        System.out.println("字符型数组元素为：");
        printArray(charArray);
    }
}
```

### 5.5 泛型类
```java
package GenericsDemo;

public class genericClassDemo <T>{
    public T val;
    public void add (T val){
        this.val = val;
    }
    public void display(){
        System.out.println(this.val);
    }

    public static void main(String[] args) {
        genericClassDemo<Integer> intTest = new genericClassDemo<>();
        genericClassDemo<String> stringTest = new genericClassDemo<>();

        intTest.add(1);
        stringTest.add("fdsafdas");

        intTest.display();
        stringTest.display();
    }
}
```

