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



# 二、实际操作篇

远程登陆（XShell，XFtp）

实用指令

进程管理

用户管理

Vi和Vim编辑器

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

# 