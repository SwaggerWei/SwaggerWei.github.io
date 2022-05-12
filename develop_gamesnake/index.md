# 贪吃蛇开发


* 游戏名：贪吃蛇
* github 地址：[https://github.com/SwaggerWei/SnakeGame](https://github.com/SwaggerWei/SnakeGame)

## 资源导入
### 步骤
* 单独定一个资源类，所有的数据变量都定义为静态变量，方便获取
* 使用 **java.net.URL** 获取文件地址
* 然后转换为swing中的ImageIcon格式

### 源码
```Java
package com.swagger.snake;

import javax.swing.*;
import java.net.URL;

// 存放外部数据
public class Data {
    // 头部图片 使用URL类，定位图片地址
    public static URL headerUrl = Data.class.getResource("/statics/header1.png");
    public static ImageIcon header = new ImageIcon(headerUrl);

//    // 头部图片
    public static URL upUrl = Data.class.getResource("/statics/up1.png");
    public static URL downUrl = Data.class.getResource("/statics/down1.png");
    public static URL leftUrl = Data.class.getResource("/statics/left1.png");
    public static URL rightUrl = Data.class.getResource("/statics/right1.png");
    public static ImageIcon up = new ImageIcon(upUrl);
    public static ImageIcon down = new ImageIcon(downUrl);
    public static ImageIcon left = new ImageIcon(leftUrl);
    public static ImageIcon right = new ImageIcon(rightUrl);

//    // 身体
    public static URL bodyUrl1 = Data.class.getResource("/statics/body1.png");
    public static ImageIcon body1 = new ImageIcon(bodyUrl1);

    //    // 食物
    public static URL foodUrl = Data.class.getResource("/statics/food1.png");
    public static ImageIcon food = new ImageIcon(foodUrl);


}
```




## 绘制主窗口
### 步骤
* 使用JFrame绘制一个静态窗口
* 设置页面打洗哦啊
* 设置窗口是否可调整大小
* 设置关闭事件,可以通过右上角叉叉进行关闭
* 加入一个JPanel到JFrame窗口当中
* 设置窗口的可见性，这一步应该放到最后，不然画板中的内容不能生效

### 源码
```Java
package com.swagger.snake;

import javax.swing.*;

public class StartGames {
    public static void main(String[] args) {
        // 1 绘制一个静态窗口 JFrame
        JFrame jFrame = new JFrame("swagger-Java-贪吃蛇小游戏");
        // 设置页面大小
        jFrame.setBounds(10, 10, 900, 720);
        // 设置窗口是否可调整大小
        jFrame.setResizable(true);
        // 设置关闭事件,可以通过右上角叉叉进行关闭
        jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);


        // 2 面板 JPanel 可以加入到JFrame
        jFrame.add(new GamePanel());


        // 设置窗口的可见性，这一步应该放到最后，不然画板中的内容不能生效
        jFrame.setVisible(true);
    }
}

```



