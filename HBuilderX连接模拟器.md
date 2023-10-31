### 一、HBuilderX 连接网易mumu手机模拟器进行App开发

##### 1、下载安装手机模拟器

常见的安卓手机模拟器：

| 手机模拟器名称 | 对应端口号 |
| -------------- | ---------- |
| 夜神模拟器     | 62001      |
| 天天模拟器     | 6555       |
| 海马玩模拟器   | 26944      |
| 逍遥模拟器     | 21503      |
| 网易mumu模拟器 | 7555       |

**第一步**

先安装MuMu[模拟器](https://so.csdn.net/so/search?q=模拟器&spm=1001.2101.3001.7020)，调成手机模式。

**第二步**

查看MuMu模拟器监听的端口。
在安装目录下 /vms/MuMuPlayer-12.0-0/MuMuPlayer-12.0-0

![image-20231031102402529](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20231031102402529.png)

旧端口7555，新端口号16384

##### 2、配置hbuilderx

打开HBuilder X ——工具——设置，配置端口和[adb](https://so.csdn.net/so/search?q=adb&spm=1001.2101.3001.7020)路径

![image-20231031102852511](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20231031102852511.png)

##### 3、配置环境变量

1）复制adb.exe所在文件夹目录

找到[HBuilder](https://so.csdn.net/so/search?q=HBuilder&spm=1001.2101.3001.7020) X 的安装目录，查找adb.exe文件，复制adb.exe所在文件目录的路径，配置到环境变量的Path中。

![image-20231031103218820](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20231031103218820.png)

2）打开控制面板—系统和安全—系统—高级系统设置—环境变量，界面如下：

![image-20231031103322154](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20231031103322154.png)

3）点击系统变量的Path 点击编辑后 ，把之前复制的adb.exe路径拷贝进来，点击保存。

![image-20231031103342904](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20231031103342904.png)

5、使用adb命令连接手机模拟器并测试
首先，打开mumu手机模拟器，在HBuilder x 中查看能否检测到设备。
如果 HBuilder x 未检测到 模拟器，需要使用adb命令进行连接。

1）运行cmd，执行以下命令行查看 adb 的版本（测试是否能使用）

```
adb version
```

2）查看连接的设备

```
adb devices
```

3）使用adb命令 让模拟器连接上电脑

```
adb connect 127.0.0.1:7555
```

4）最后测试Hbuilder能否连接手机模拟器
![image-20231031103600089](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20231031103600089.png)