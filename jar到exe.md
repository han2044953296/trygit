---
title: 把jar包打包成exe
date: 11/19/2022 11:40:21 AM 
categories: 杂货技术栈
comments: "true"
tag: java
---
# 使用工具

- 我们所使用的工具是exe4j 和 inno setup

## exe4j

- 简单来说这个就是个工具，可以把java的jar包打包成exe的
- 可以让其他由java运行环境的机器运行这个jar
- 而且还可以稍微做到保密的程度
- exe4j 是一个帮助你集成 Java 应用程序到 Windows 操作环境的 java 可执行文件生成工具，无论这些应用是用于服务器，还是图形用户界面（GUI）或命令行的应用程序。如果你想在任务管理器中及 Windows XP 分组的用户友好任务栏里以你的进程名取代 java.exe 的出现，那么 exe4j 可以完成这个工作。exe4j 帮助你以一种安全的方式启动你的 java 应用程序，来显示本地启动画面，检测及发布合适的 JRE 和 JDK，以及进行启动时所发生的错误处理等，以至于更多。
- 关于exe4j的破解我就不多赘述了，懂的都懂
- 接下来是实际操作
- 下面是开始界面
- ![开始界面](https://pic.imgdb.cn/item/6378540416f2c2beb192c11f.jpg)
- 然后接下来
- 选择jar到exe的模式
- ![](https://pic.imgdb.cn/item/6378546916f2c2beb1932dfa.jpg)
- 然后接下来要选择你的输出路径
- ![](https://pic.imgdb.cn/item/637854cd16f2c2beb193c56c.jpg)
- 接下来选择我们的启动方式，我选择的是控制台启动
- ![](https://pic.imgdb.cn/item/6378550616f2c2beb1942a62.jpg)
- 接下来选择可以在32位和64位机器上都可以运行
- ![](https://pic.imgdb.cn/item/6378553316f2c2beb194a3be.jpg)
- ![](https://pic.imgdb.cn/item/6378556516f2c2beb1952fa9.jpg)
- 接下来选择我们exe要执行的主类
- 我这个项目是boot项目，所以选择的是这个
- ![](https://pic.imgdb.cn/item/6378558416f2c2beb195719f.jpg)
- 然后设置我们的jdk版本
- ![](https://pic.imgdb.cn/item/637855bd16f2c2beb195f1d6.jpg)
- ![](https://pic.imgdb.cn/item/637855dc16f2c2beb1962843.jpg)
- 最后一直点击next就好了
- ![](https://pic.imgdb.cn/item/6378564016f2c2beb196ce41.jpg)
- 这样我们就打包完成了
- 但是这样的打包只能再有jdk的环境中运行
- 接下来我们要为这个添加依赖

## inno setup

- 简单来说就是个绑定依赖的程序
- 打开
- ![](https://pic.imgdb.cn/item/6378572316f2c2beb198e2a2.jpg)
- ![](https://pic.imgdb.cn/item/6378577616f2c2beb199aecb.jpg)
- ![](https://pic.imgdb.cn/item/6378579d16f2c2beb19a1b5f.jpg)
- 然后配置我们的名称和版本号
- 然后选择我们，刚刚弄个出来的exe程序
- ![](https://pic.imgdb.cn/item/637858cd16f2c2beb19b780b.jpg)
- 然后就一直傻瓜式next
- ![](https://pic.imgdb.cn/item/6378593316f2c2beb19be141.jpg)
- 设置输出文件夹
- 图标
- 以及运行时的密码
- 然后一直next就好
- 然后会跳出两个对话框
- 全部选择是
- 最后会出现这个东西
- ![](https://pic.imgdb.cn/item/637859e916f2c2beb19d21f9.jpg)
- 就代表导入依赖了
- 接下来我们要对他进行修改
- ![](https://pic.imgdb.cn/item/63785a3d16f2c2beb19d8050.jpg)
- 这个是自己电脑本机的jdk路径
- 然后还有要在这里加上MYjrename
- ![](https://pic.imgdb.cn/item/63785a8116f2c2beb19dd2fa.jpg)
- 最后运行就成功了
- 他会给你成一个setup文件，通过这个文件安装的exe
- 就会自己带环境了
-
