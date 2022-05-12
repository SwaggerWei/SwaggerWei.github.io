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


## GamePanel 类：继承JPanel，实现KeyListener和ActionListener
### 构造函数
* 调用init() 完成各种初始化操作
* 获取键盘监听事件，是的键盘聚焦到游戏上，然后添加一个键盘监听者
* 让事件定义的Timer实例动起来start
```Java
public GamePanel(){
    init();

    // 获取键盘的监听事件
    this.setFocusable(true);// 键盘聚焦到游戏上
    this.addKeyListener(this);

    // 时间动起来
    timer.start();
}
```

### 初始化init 
* 初始化长度和蛇头蛇身坐标
* 初始化食物坐标，注意不能和蛇重合
* 初始化分数为0
```Java
public void init(){
    length = 3;
    snakeX[0] = 100; snakeY[0] = 100; // 蛇头坐标
    snakeX[1] = 75; snakeY[1] = 100; // 第一节身体坐标
    snakeX[2] = 50; snakeY[2] = 100; // 蛇头坐标

    boolean isFoodCoveredByBody = false;
    while (true){
        // 重新生成食物坐标
        foodx = 25 + 25 * random.nextInt(34);
        foody = 75 + 25 * random.nextInt(24);
        for (int i = 0; i < length; i++) {
            if (foodx == snakeX[i] && foody == snakeY[i]){
                isFoodCoveredByBody = true;
            }
        }
        if (isFoodCoveredByBody  == true){
            continue;
        }else {
            break;
        }
    }


    score = 0;
}
```

### 绘制静态画板
* 调用父类，达到清屏效果
* 设置背景的颜色
* 从Data中获取header绘制头部广告栏
* 绘制游戏区域
* 画一条静态小蛇，注意头方向的判断，然后绘制身体
* 画积分，设置画积分的各种参数
* 画一个食物
* 提示游戏开始，注意判断是否为开始状态
* 失败提醒，注意判断是否为失败状态
```Java
protected void paintComponent(Graphics g) {

    // 1 绘制静态面板
    // 调用父类，达到清屏效果
    super.paintComponent(g);
    // 设置背景的颜色
    this.setBackground(Color.WHITE);
    // 绘制头部广告栏
    Data.header.paintIcon(this, g, 25, 11);
    // 绘制游戏区域
    g.fillRect(25, 75, 850, 600);

    // 2 画一条静态的小蛇
    // 画蛇头
    if (direction.equals("R")){
        Data.right.paintIcon(this, g, snakeX[0], snakeY[0]);
    }else if (direction.equals("L")){
        Data.left.paintIcon(this, g, snakeX[0], snakeY[0]);
    }else if (direction.equals("U")){
        Data.up.paintIcon(this, g, snakeX[0], snakeY[0]);
    }else if (direction.equals("D")){
        Data.down.paintIcon(this, g, snakeX[0], snakeY[0]);
    }

    // 画蛇身体

    for (int i = 1; i < length; i++) {
        // 蛇的身体长度，通过length 参数来控制
        Data.body1.paintIcon(this, g, snakeX[i], snakeY[i]);
    }


    String font = "微软雅黑"; // 字体类型

    // 3 画积分
    g.setColor(Color.WHITE); // 设置画笔的颜色
    g.setFont(new Font(font, Font.BOLD, 18));
    g.drawString("长度：" + length, 750, 30);
    g.drawString("分数：" + score, 750, 50);

    // 4 画一个食物
    Data.food.paintIcon(this, g, foodx, foody);

    // 5 游戏提示，是否开始
    if (isStart == false){
        // 画一个文字
        g.setColor(Color.WHITE); // 设置画笔的颜色
        g.setFont(new Font(font, Font.BOLD, 40));
        g.drawString("按下空格开始游戏", 300, 300);
    }

    // 6 失败提醒
    if (isFail == true){
        // 画一个文字
        g.setColor(Color.RED); // 设置画笔的颜色
        g.setFont(new Font(font, Font.BOLD, 40));
        g.drawString("游戏失败，按下空格重新开始", 200, 300);
    }
}
```

### 接受键盘输入
* 空格键控制暂停和开始，注意首先判断是否为失败状态，如果为失败状态，则重新init，重制各项数据，如果没有失败则进把是否开始状态置反
* 接受键盘输入，改变方向，注意控制相反方向不能改变
```Java
public void keyPressed(KeyEvent e) {
    // 键盘：按下（未释放）
    // 获取按下的键盘是哪一个键
    int keyCode = e.getKeyCode();
    if (keyCode == KeyEvent.VK_SPACE){ // 如果按下的是空格键

        if (isFail == true){
            isFail = false;
            init(); // 重新初始化游戏
        }else {
            isStart = !isStart;
        }
        // 刷新界面
        repaint();
    }


    // 键盘控制走向
    if (keyCode == KeyEvent.VK_LEFT){
        if (direction != "R"){direction = "L";}
    }else if (keyCode == KeyEvent.VK_RIGHT){
        if (direction != "L"){direction = "R";}
    }else if (keyCode == KeyEvent.VK_UP){
        if (direction != "D"){direction = "U";}
    }else if (keyCode == KeyEvent.VK_DOWN){
        if (direction != "U"){direction = "D";}
    }
}
```

### actionPerformed
* 首先身体移动，再移动头部，注意控制边界判断
* 判断蛇头是否和食物重合，如果重合则重新生成食物，注意重新生成的食物不能和蛇重合
* 进行失败判断，蛇头不能和蛇身重合
* 刷新界面repaint，进入下一帧
```Java
public void actionPerformed(ActionEvent e) {
    // 如果游戏处于开始状态, 并且游戏没有失败
    if (isStart && isFail == false){

        for (int i = length - 1; i > 0 ; i--) { // 身体移动
            snakeX[i] = snakeX[i-1];
            snakeY[i] = snakeY[i-1];
        }

        // 通过控制方向让头部移动
        if (direction == "R"){
            if (snakeX[0] > 825){snakeX[0] = 0;}// 边界判断
            snakeX[0] = snakeX[0] + 25; // 头部移动
        }else if (direction == "L"){
            if (snakeX[0] < 50){snakeX[0] = 825;}// 边界判断
            snakeX[0] = snakeX[0] - 25; // 头部移动
        }else if (direction == "U"){
            if (snakeY[0] < 100){snakeY[0] = 675;}// 边界判断
            snakeY[0] = snakeY[0] - 25; // 头部移动
        }else if (direction == "D"){
            if (snakeY[0] > 625){snakeY[0] = 75;}// 边界判断
            snakeY[0] = snakeY[0] + 25; // 头部移动
        }

        // 如果蛇头和食物的坐标重合，说明吃到了食物
        if (snakeX[0] == foodx && snakeY[0] == foody){
            // 蛇的长度+1
            length ++;
            score = score+10;

            // 重新生成食物
            // 如果重新生成的食物坐标和蛇身或者头部相重合，则重新生成
            boolean isFoodCoveredByBody = false;
            while (true){
                // 重新生成食物坐标
                foodx = 25 + 25 * random.nextInt(34);
                foody = 75 + 25 * random.nextInt(24);
                for (int i = 0; i < length; i++) {
                    if (foodx == snakeX[i] && foody == snakeY[i]){
                        isFoodCoveredByBody = true;
                    }
                }
                if (isFoodCoveredByBody  == true){
                    continue;
                }else {
                    break;
                }
            }


        }

        // 失败判断
        for (int i = 1; i < length; i++) {
            if (snakeX[0] == snakeX[i] && snakeY[0] == snakeY[i]){
                isFail = true;
            }
        }


        // 刷新界面
        this.repaint();
    }
    timer.start();// 时间动起来
}
```

### 源码
```Java
package com.swagger.snake;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.util.Random;

public class GamePanel extends JPanel implements KeyListener, ActionListener{

    // 定义一个蛇
    int length; // 蛇的长度
    int[] snakeX = new int[600];// 蛇的坐标X
    int[] snakeY = new int[500];// 蛇的坐标Y

    boolean isStart = false;// 游戏是否开始
    String direction = "R";// 蛇的方向

    Timer timer = new Timer(100, this); // 间隔100ms 触发一次

    // 定义一个食物
    int foodx;
    int foody;
    Random random = new Random();

    // 判断失败
    boolean isFail = false;

    // 游戏积分榜
    int score;


    // 构造函数
    public GamePanel(){
        init();

        // 获取键盘的监听事件
        this.setFocusable(true);// 键盘聚焦到游戏上
        this.addKeyListener(this);

        // 时间动起来
        timer.start();
    }

    // 初始化
    public void init(){
        length = 3;
        snakeX[0] = 100; snakeY[0] = 100; // 蛇头坐标
        snakeX[1] = 75; snakeY[1] = 100; // 第一节身体坐标
        snakeX[2] = 50; snakeY[2] = 100; // 蛇头坐标

        boolean isFoodCoveredByBody = false;
        while (true){
            // 重新生成食物坐标
            foodx = 25 + 25 * random.nextInt(34);
            foody = 75 + 25 * random.nextInt(24);
            for (int i = 0; i < length; i++) {
                if (foodx == snakeX[i] && foody == snakeY[i]){
                    isFoodCoveredByBody = true;
                }
            }
            if (isFoodCoveredByBody  == true){
                continue;
            }else {
                break;
            }
        }


        score = 0;
    }

    // 画板： 画界面、画蛇
    // Graphics：为一个画笔
    @Override
    protected void paintComponent(Graphics g) {

        // 1 绘制静态面板
        // 调用父类，达到清屏效果
        super.paintComponent(g);
        // 设置背景的颜色
        this.setBackground(Color.WHITE);
        // 绘制头部广告栏
        Data.header.paintIcon(this, g, 25, 11);
        // 绘制游戏区域
        g.fillRect(25, 75, 850, 600);

        // 2 画一条静态的小蛇
        // 画蛇头
        if (direction.equals("R")){
            Data.right.paintIcon(this, g, snakeX[0], snakeY[0]);
        }else if (direction.equals("L")){
            Data.left.paintIcon(this, g, snakeX[0], snakeY[0]);
        }else if (direction.equals("U")){
            Data.up.paintIcon(this, g, snakeX[0], snakeY[0]);
        }else if (direction.equals("D")){
            Data.down.paintIcon(this, g, snakeX[0], snakeY[0]);
        }

        // 画蛇身体

        for (int i = 1; i < length; i++) {
            // 蛇的身体长度，通过length 参数来控制
            Data.body1.paintIcon(this, g, snakeX[i], snakeY[i]);
        }


        String font = "微软雅黑"; // 字体类型

        // 3 画积分
        g.setColor(Color.WHITE); // 设置画笔的颜色
        g.setFont(new Font(font, Font.BOLD, 18));
        g.drawString("长度：" + length, 750, 30);
        g.drawString("分数：" + score, 750, 50);

        // 4 画一个食物
        Data.food.paintIcon(this, g, foodx, foody);

        // 5 游戏提示，是否开始
        if (isStart == false){
            // 画一个文字
            g.setColor(Color.WHITE); // 设置画笔的颜色
            g.setFont(new Font(font, Font.BOLD, 40));
            g.drawString("按下空格开始游戏", 300, 300);
        }

        // 6 失败提醒
        if (isFail == true){
            // 画一个文字
            g.setColor(Color.RED); // 设置画笔的颜色
            g.setFont(new Font(font, Font.BOLD, 40));
            g.drawString("游戏失败，按下空格重新开始", 200, 300);
        }




    }


    // 接受键盘输入
    @Override
    public void keyPressed(KeyEvent e) {
        // 键盘：按下（未释放）
        // 获取按下的键盘是哪一个键
        int keyCode = e.getKeyCode();
        if (keyCode == KeyEvent.VK_SPACE){ // 如果按下的是空格键

            if (isFail == true){
                isFail = false;
                init(); // 重新初始化游戏
            }else {
                isStart = !isStart;
            }
            // 刷新界面
            repaint();
        }


        // 键盘控制走向
        if (keyCode == KeyEvent.VK_LEFT){
            if (direction != "R"){direction = "L";}
        }else if (keyCode == KeyEvent.VK_RIGHT){
            if (direction != "L"){direction = "R";}
        }else if (keyCode == KeyEvent.VK_UP){
            if (direction != "D"){direction = "U";}
        }else if (keyCode == KeyEvent.VK_DOWN){
            if (direction != "U"){direction = "D";}
        }


    }

    // 定时器，监听时间 帧：执行定时操作
    @Override
    public void actionPerformed(ActionEvent e) {
        // 如果游戏处于开始状态, 并且游戏没有失败
        if (isStart && isFail == false){

            for (int i = length - 1; i > 0 ; i--) { // 身体移动
                snakeX[i] = snakeX[i-1];
                snakeY[i] = snakeY[i-1];
            }

            // 通过控制方向让头部移动
            if (direction == "R"){
                if (snakeX[0] > 825){snakeX[0] = 0;}// 边界判断
                snakeX[0] = snakeX[0] + 25; // 头部移动
            }else if (direction == "L"){
                if (snakeX[0] < 50){snakeX[0] = 825;}// 边界判断
                snakeX[0] = snakeX[0] - 25; // 头部移动
            }else if (direction == "U"){
                if (snakeY[0] < 100){snakeY[0] = 675;}// 边界判断
                snakeY[0] = snakeY[0] - 25; // 头部移动
            }else if (direction == "D"){
                if (snakeY[0] > 625){snakeY[0] = 75;}// 边界判断
                snakeY[0] = snakeY[0] + 25; // 头部移动
            }

            // 如果蛇头和食物的坐标重合，说明吃到了食物
            if (snakeX[0] == foodx && snakeY[0] == foody){
                // 蛇的长度+1
                length ++;
                score = score+10;

                // 重新生成食物
                // 如果重新生成的食物坐标和蛇身或者头部相重合，则重新生成
                boolean isFoodCoveredByBody = false;
                while (true){
                    // 重新生成食物坐标
                    foodx = 25 + 25 * random.nextInt(34);
                    foody = 75 + 25 * random.nextInt(24);
                    for (int i = 0; i < length; i++) {
                        if (foodx == snakeX[i] && foody == snakeY[i]){
                            isFoodCoveredByBody = true;
                        }
                    }
                    if (isFoodCoveredByBody  == true){
                        continue;
                    }else {
                        break;
                    }
                }


            }

            // 失败判断
            for (int i = 1; i < length; i++) {
                if (snakeX[0] == snakeX[i] && snakeY[0] == snakeY[i]){
                    isFail = true;
                }
            }


            // 刷新界面
            this.repaint();
        }
        timer.start();// 时间动起来
    }




    @Override
    public void keyReleased(KeyEvent e) {
        // 键盘：弹起（释放某个键）
    }
    @Override
    public void keyTyped(KeyEvent e) {
        // 键盘： 按下+弹起 -》敲击了一下键盘
    }
}

```


