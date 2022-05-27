# 第1章 Git概述



​		Git是一个免费的、开源的**分布式版本控制系统**，可以快速高效地处理从小型到大型的各种项目。

## 1.1 何为版本控制

​		一种**记录文件内容变化**，以便将来查阅特定版本修订情况的系统。版本控制其实最重要的是可以记录文件修改历史记录，从而让用户能够查看历史版本，方便版本切换。

## 1.2 为什么需要版本控制

​		个人开发过渡到团队协作。

## 1.3 版本控制工具

​		**集中式版本控制工具**

​		CVS、SVN（Subversion）、VSS ......

​		都有一个单一的集中管理的服务器，保存所有文件的修订版本，而协同工作的人们都通过客户端连到这台服务器，取出最新的文件或者提交更新。这是多年以来的版本控制标准做法。

​		优点：每个人都可以在一定程度上看到项目中的其他人正在做什么。管理员也可以轻松掌控每个开发者的权限，并且管理一个集中化的版本控制系统，要远比在各个客户端上维护本地数据库更容易。

​		缺点：中央服务器的单点故障。如果服务器宕机一小时，那么这一小时内，谁都无法提交更新，也就无法协同工作。

​		**分布式版本控制工具**

​		客户端提取的不是最新版本的文件快照，而是把代码仓库完整地镜像下来（本地库）。这样任何一处协同工作用的文件发生故障，事后都可以用其他客户端的本地仓库进行恢复。因为每个客户端的每一次文件提取操作，实际上都是一次对整个文件仓库的完整备份。

​		分布式版本控制系统的出现，解决了集中式版本控制系统的缺陷：

​		1.服务器断网的情况下也可以进行开发，因为版本控制是在本地进行的。

​		2.每个客户端保存的也都是整个完整的项目，包含历史记录，更加安全。

## 1.4 Git工作机制

​		工作区（写代码，存放代码的位置）--> git add --> 暂存区（临时存储）--> git commit --> 本地库（历史版本）



# 第2章 Git安装



​		官网地址：  https://git-scm.com/

​		

​		注意：安装路径最好不要有空格。

![image-20220527011112589](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527011112589.png)	

<img src="https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527011826815.png" alt="image-20220527011826815"  />	    

注意：设置图片居左：偏好设置 --> 外观-主题-主题文件夹 --> github.css文件

```css
/*设置图⽚居左设置*/
p .md-image:only-child{
    width: auto;
    text-align: left;
}
```

![image-20220527014641446](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527014641446.png)





# 第3章 Git常用命令

​		

| 命令名称                             | 作用           |
| ------------------------------------ | -------------- |
| git config --global user.name 用户名 | 设置用户签名   |
| git config --global user.email 邮箱  | 设置用户签名   |
| git init                             | 初始化本地库   |
| git status                           | 查看本地库状态 |
| git add 文件名                       | 添加到暂存区   |
| git commit -m "日志信息"文件名       | 提交到本地库   |
| git reflog                           | 查看历史记录   |
| git reset --hard 版本号              | 版本穿梭       |

## 3.1 设置用户签名

​		签名的作用是区分不同操作者的身份。用户的签名信息在每一个版本的提交信息中能够看到，以此确认本次提交是谁做的。Git首次安装必须设置一下用户签名，否则无法提交代码。注意：这里设置用户签名和将来登录GitHub的账号没有任何关系。

## 3.2 初始化本地库

​		ll -a Linux下查看隐藏文件

## 3.3 查看本地库状态

## ![image-20220527024116909](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527024116909.png)



# 第4章 Git分支操作

## 4.1 什么是分支

​		在版本控制过程中，同时推进多个任务，为每个任务，我们就可以创建每个人物的单独分支。使用分支意味着开发者可以把自己的工作从开发主线上分离开来，开发自己的分支的时候不会影响主线分支的运行。简单理解就是副本，一个分支就是一个单独的副本。（分支底层其实也是指针的引用。）

## 4.2 分支的好处

​		同时并行推进多个功能开发，提高开发效率。

​		各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可。

## 4.3 分支的操作

​		

| 命令名称            | 作用                         |
| ------------------- | ---------------------------- |
| git branch 分支名   | 创建分支                     |
| git branch -v       | 查看分支                     |
| git checkout 分支名 | 切换分支                     |
| git merge 分支名    | 把指定的分支合并到当前分支上 |

​		注意：冲突产生的原因：

​				合并分支时，两个分支在**同一个文件的同一个位置**有两套完全不同的修改。Git无法替我们决定使用哪一个。必须人为决定新代码内容。

​		

# 第5章 GitHub操作

​		全球最大同性交友网站。

## 5.1 远程仓库操作

​		

| 命令名称                           | 作用                                                     |
| ---------------------------------- | -------------------------------------------------------- |
| git remote -v                      | 查看当前所有远程地址别名                                 |
| git remote add 别名 远程地址       | 起别名                                                   |
| git push 别名 分支                 | 推送本地分支上的内容到远程仓库                           |
| git clone 远程地址                 | 将远程仓库的内容克隆到本地                               |
| git pull 远程库地址别名 远程分支名 | 将远程仓库对于分支最新内容拉下来后与当前本地分支直接合并 |

## 5.2 创建远程仓库别名

​		git remote -v   查看当前所有远程地址别名

​		git remote add 别名 远程地址

![image-20220527145333453](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527145333453.png)

![image-20220527145352061](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527145352061.png)



## 5.3 推送本地分支到远程仓库

![image-20220527151517912](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527151517912.png)



## 5.4 拉取远程仓库到本地

![image-20220527153423470](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527153423470.png)



## 5.5 克隆远程仓库到本地

![image-20220527154152247](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527154152247.png)

​		clone会做三个操作：1.拉取代码。2.初始化本地仓库。 3.创建别名。

## 5.6 SSH免密登录

​		![image-20220527163345417](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527163345417.png)

![image-20220527163800033](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527163800033.png)

![image-20220527163815507](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527163815507.png)

![image-20220527164002175](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527164002175.png)



![image-20220527164807341](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527164807341.png)

![image-20220527170256803](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220527170256803.png)



# 第6章 IDEA集成Git

# 第7章 IDEA集成GitHub

# 第8章 Gitee 码云

# 第9章 GitLab