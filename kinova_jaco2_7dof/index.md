# 3-kinova jaco2 7dof 机械臂基础控制


## 机器人唤醒
_TIPS： 注意ROS节点和Development Center不能一起打开_

* source
```shell script
source devel/setup.bash
```

* bringup机械臂
```shell script
roslaunch kinova_bringup kinova_robot.launch kinova_robotType:=j2n7s300
```

## cartesian 控制模式

* 重新起一个终端
```shell script
cd ~/catkin_ws
source devel/setup.bash
```

* 运行pose_action_client.py 文件  
**tips： pose参数解释，机器人方向看手**   

_a.非相对（指定6D位置）移动， 不带-r参数_  
* （小）左（大）右   
* （小）高（大）低   
* （大）前（小）后  
```shell script
rosrun kinova_demo pose_action_client.py -v j2n7s300 mdeg -- -0.193846275806 -0.265829235315 0.505497992039 94.2 -43.2 -167.1
rosrun kinova_demo pose_action_client.py -v j2n7s300 mdeg -- -0.193846275806 -0.265829235315 0.705497992039 94.2 -43.2 -167.1
rosrun kinova_demo pose_action_client.py -v j2n7s300 mdeg -- -0.193846275806 -0.465829235315 0.505497992039 94.2 -43.2 -167.1
rosrun kinova_demo pose_action_client.py -v j2n7s300 mdeg -- -0.393846275806 -0.265829235315 0.505497992039 94.2 -43.2 -167.1
rosrun kinova_demo pose_action_client.py -v j2n7s300 mdeg -- -0.193846275806 -0.265829235315 0.705497992039 94.2 -43.2 -167.1
```

_b.相对（在当前6D位置基础上）移动，带-r参数_  
```shell script
rosrun kinova_demo pose_action_client.py -v -r j2n7s300 mdeg -- 0.01 0 0 0 10 10
```

## 手指控制
* 参数 
robotType： j2n7s300  
手指移动模式： percent  
手指打开程度： 100 100 100（完全闭合） ； 0 0 0（完全打开）  

_控制命令_  
```shell
rosrun kinova_demo fingers_action_client.py j2n7s300 percent -- 100 100 100
```
```shell
rosrun kinova_demo fingers_action_client.py j2n7s300 percent -- 0 0 0
```





