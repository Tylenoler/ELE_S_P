#ros

## 小鱼ROS
要使用小鱼的一键安装系列，需要下载一个鱼香大佬写的脚本，然后执行这个脚本，进行ROS的安装与环境的配置
- 下载脚本并执行脚本
```
wget http://fishros.com/install -O fishros && . fishros
```
- 输入密码
![[Pasted image 20250122163937.png]]
- 选择[1]
![[Pasted image 20250122164035.png]]
- 换源并清理第三方源
![[Pasted image 20250122164235.png]]
- 等待软件更新和检测安装源
![[Pasted image 20250122164420.png]]
- 选择ROS版本
![[Pasted image 20250122164536.png]]
- 选择安装版本（建议桌面版）
![[Pasted image 20250122164607.png]]
- 开始安装
![[Pasted image 20250122164644.png]]
- 安装完成
![[Pasted image 20250122165759.png]]
## 验证安装
1. 检查Ros版本
- 新建一个终端，输入`ros2 --version`来检查ROS2是否已经正确安装。

2. 小乌龟验证
分别打开两个终端，输入
```
ros2 run turtlesim turtlesim_node		
# 启动乌龟GUI节点界面，乌龟可以在界面中运动
```
和
```
ros2 run turtlesim turtle_teleop_key	
# 启动键盘控制节点，可以通过键盘控制乌龟运动
```
- 结果：
![[Pasted image 20250122170657.png]]