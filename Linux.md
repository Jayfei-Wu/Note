# 一、基础篇

## 1.Linux概述

开源、免费、稳定、安全、处理多并发，企业级的Java项目等都会部署到Linux/Unix系统上。

## 2.vm和Linux的安装

### 2.1 VM

1.在VMware中新建虚拟机

![image-20220606131018005](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606131018005.png)

2.下一步，选择自定义安装

![image-20220606131150884](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606131150884.png)

3.虚拟机兼容性，默认下一步

![image-20220606131245833](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606131245833.png)

4.安装来源，选择稍后安装操作系统

![image-20220606131455593](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606131455593.png)

5.操作系统类型，选择Linux centos 64

![image-20220606131638113](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606131638113.png)

6.自定义虚拟机名称，和文件夹位置

![image-20220606131843215](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606131843215.png)

7.虚拟机CPU,默认下一步

![image-20220606131925131](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606131925131.png)

8.内存选择，1024MB

![image-20220606132013883](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132013883.png)

9.网络类型，可选NAT或者桥接

![image-20220606132051000](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132051000.png)

10. IO类型，默认下一步

![image-20220606132123499](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132123499.png)

11.虚拟机磁盘类型，默认下一步

![image-20220606132158199](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132158199.png)

12.创建新磁盘，默认下一步

![image-20220606132236315](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132236315.png)

13.磁盘大小，默认下一步

![image-20220606132350830](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132350830.png)

14.磁盘文件名，默认下一步

![image-20220606132427656](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132427656.png)

15.完成虚拟机创建

![image-20220606132500609](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132500609.png)

16.出bug了

![image-20220606132555809](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132555809.png)

17.解决bug-退出，右键vmware选择以管理员身份运行即可。

![image-20220606132831963](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606132831963.png)

### 2.2 Linux

1.在新的虚拟机，选择DVD，选择使用IOS镜像，选择Centos7 安装镜像

![image-20220606134323324](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606134323324.png)

![image-20220606134720237](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606134720237.png)

2.开启此虚拟机

![image-20220606134806807](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606134806807.png)



3. install centos 7 安装操作系统

![这里写图片描述](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/20180628102045479)

4.中文/英文

![image-20220606135409408](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606135409408.png)



5.基本配置

![image-20220606135622452](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606135622452.png)

可以选择最小系统安装或者带GUI安装

![image-20220606135741912](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606135741912.png)

![image-20220606135909844](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606135909844.png)

![image-20220606140103738](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606140103738.png)

6.点击开始安装

![image-20220606140201866](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606140201866.png)



7.设置root密码

![image-20220606140322661](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606140322661.png)



8.自定义一个新用户名，密码

![image-20220606140522565](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606140522565.png)

9.完成Linux安装

![image-20220606142417273](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220606142417273.png)

## 3.Linux目录结构

linux的文件系统是采用级层式的树状目录结构，在此结构中的最上层是根目录“/”，然后在此目录下再创建其他的目录。

linux里面，一切皆文件

### 具体的结构目录

/bin （/usr/sbin、/usr/local/bin）

是Binary的缩写，这个目录存放着最经常使用的命令

/sbin （/usr/sbin、/usr/local/sbin）

s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。

/home

存放普通用户的主目录，在Linux中每个用户都有一个自己的目录，一般该目录名是以用户的账号命名。

/root

该目录为系统管理员，也称作超级权限者的用户主目录

/lib

系统开机所需要最基本的动态连接共享库，其作用类似于Windows里的DLL文件。几乎所有的应用程序都需用到这些共享库

/lost + found 

这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。

# 二、实际操作篇

## 1.远程登陆--XShell

**双击打开软件**

![image-20220607103342035](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607103342035.png)

**点击新建**-名称自定义-主机输入IP

虚拟机端ifconfig获取linux公网地址

主机端ping该地址测试是否连通

![image-20220607103702473](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607103702473.png)

**双击左侧该会话-接受并保存**

![image-20220607104223088](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607104223088.png)

![image-20220607104304240](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607104304240.png)

![image-20220607104632200](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607104632200.png)

## 2.远程上传或下载--XFtp

基于windows平台的SFTP、FTP文件传输软件。windows用户能安全的在UNIX/Linux和windowsPC之间传输文件

**双击打开软件**

![image-20220607105131159](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607105131159.png)

**点击新建**

![image-20220607105320190](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607105320190.png)

![image-20220607113620001](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607113620001.png)

**点击连接-出现bug-乱码。。。**

![image-20220607113652986](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607113652986.png)

**解决bug**

![image-20220607110222061](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607110222061.png)

![image-20220607110426899](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607110426899.png)

**搞定**

![image-20220607110529814](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607110529814.png)

**右键点击传输**

![image-20220607105833602](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607105833602.png)



## 3.Vi和Vim编辑器

**基本介绍**

Linux系统会内置Vi文本编辑器

Vim具有程序编辑的能力，可以看做是Vi的增强版本，可以主动的以字体颜色辨别语法的正确性，方便程序设计，代码补全，编译及错误跳转等方便编程的功能特别丰富，在开发者中被广泛使用。

**三种模式**

**正常模式**

以Vim打开一个档案就直接进入一般模式了（这是默认的模式）。在这个模式中，可以使用（上下左右）按键来移动光标，使用（删除字符）或（删除整行）来处理档案内容，也可以使用（复制、粘贴）来处理文件数据

**插入模式**

按下 i，o，a，r等任何一个字母会进入编辑模式

**命令行模式**

按下esc退出，再输入：，在这个模式中，可以提供相关指令，完成读取，存盘，替换，离开vim，显示行号等动作

实例：使用vim开发一个hello.java程序

![image-20220607125217336](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607125217336.png)

![image-20220607125240473](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607125240473.png)

![image-20220607125535935](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607125535935.png)

按下esc，再输入：，进入命令行模式

wq：写入并退出

![image-20220607125927818](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607125927818.png)

![image-20220607130002280](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607130002280.png)



## 4.Vi和Vim快捷键

![image-20220607131022517](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607131022517.png)

一般模式下：

1.拷贝当前行 yy，拷贝当前行向下的5行 5yy，并粘贴（p）

2.删除当前行 dd，删除当前行向下的5行 5dd

3.在文件中查找某个单词【命令行下/关键字，回车 查找，输入n就是查找下一个】

4.设置文件的行号，取消文件的行号。【命令行下：set nu 和 ：setnonu】

5.编辑 /etc/profile文件，在一般模式下使用快捷键到该文档的最末行【G】和最首行【gg】

6.在一个文件中输入“Hello”，然后又撤销这个动作 u

7.编辑 /etc/profile文件，并将光标移动到 20行 shift + g



![image-20220607132532878](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607132532878.png)





实用指令

进程管理

用户管理



定时任务调度

RPM和YUM

开机重启和用户登录注销

磁盘分区、挂载

网络配置

# 三、高级篇

日志管理

Linux内核源码&内核升级

定制自己的Linux

Linux备份与恢复

Linux可视化管理webmin和bt运维工具

Linux入侵检测&权限划分&系统优化

Linux面试题

