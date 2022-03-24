# Java 学习1-快速实现自定义字符链表


## class 文件结构
* ListNode.java 链表节点类  
* linkListString.java 链表类  
* test_ll_string.java 测试文件  
![在这里插入图片描述](https://img-blog.csdnimg.cn/e8ca83c6cc4f4868b94249a5684aba38.png#pic_center)


## ListNode.java 
* val 存放字符串  
* next 指向下一个节点  
* 构造函数
```java
package linkList_string;

public class ListNode {
    char val;
    ListNode next;

    public ListNode(char val) {
        this.val = val;
    }
}
```

## linkListString.java 链表类
* 头节点head  
* 尾节点tail  
* size 存放链表节点数目  
* 构造函数  
* 成员方法：尾部添加元素  
```java
package linkList_string;

public class linkListString {
    ListNode head;
    ListNode tail;
    int size;

    public linkListString() {
        head = null;
        tail = null;
        size = 0;
    }

    public void add(char val) {
        ListNode node = new ListNode(val);
        if (head == null) {
            head = node;
            tail = node;
        } else {
            tail.next = node;
            tail = node;
        }
        size++;
    }
}
```

## test_ll_string.java 测试文件
* 实例化linkListString对象  
* 添加节点
```java
package linkList_string;

public class test_ll_string {
    public static void main(String[] args) {
        linkListString list = new linkListString();
        list.add('a');
        list.add('b');
        list.add('c');
        list.add('d');
        list.add('e');
        list.add('f');
        list.add('g');
    }
}
```
* Debug 运行成功  
![在这里插入图片描述](https://img-blog.csdnimg.cn/6c4a6774890b4d0281536743ec5ad26d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

