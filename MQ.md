# 一、消息队列

## 1.MQ的相关概念

### 1.1 什么是MQ

​		MQ(message queue), 本质是一个队列，只不过队列中存放的内容是message而已，是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，MQ是一种非常常见的上下游“逻辑解耦+物理解耦”的消息通信服务。使用了MQ之后，消息发送上游只需要依赖MQ，不用依赖其他服务。

### 1.2 为什么要用MQ

  1. **流量消峰**

     ​		举个例子，如果订单系统最多能处理一万次订单，这个处理能力应付正常时段的下单时绰绰有余，正常时段我们下单一秒后就能返回结果。但是在高峰期，如果有两万次下单操作，系统是处理不了的，只能限制订单超过一万后不允许用户下单。使用消息队列作为缓冲，我们可以取消这个限制，把一秒内下的订单分散成一段时间来处理，这时有些用户可能在下单十几秒后才能收到下单成功的消息，但是比不能下单的体验要好。

  2. **应用解耦**

     ​		以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合调用库存、物流、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在这几分钟的时间内，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成。当物流系统恢复后，继续处理订单信息即可，下单用户感受不到物流系统的故障，提升系统可用性。

  3. **异步处理**

     ​		有些服务间调用是异步的，例如A调用B，B需要花费很长时间执行，但是A需要知道B什么时候可以执行完。以前一般有两种方式，A过一段时间去调用B的查询api查询。或者A提供一个callback api，B执行完之后调用api通知A服务。这两种方式都不是很优雅，使用消息总线可以很方便解决这个问题。A调用B服务后，只需要监听B处理完成的消息，当B处理完成后，会发送一条消息给MQ，MQ会将此消息转发给A服务。这样A服务既不用循环调用B的查询api，也不用提供callback api。同样B服务也不用做这些操作。A服务还能及时的得到异步处理成功的消息。

### 1.3 MQ的分类

1. **ActiveMQ**

   优点：单机吞吐量万级，时效性ms级，可用性高，基于主从架构实现高可用性，消息可靠性高，有很低的概率丢失数据

   缺点：官方社区对它的维护越来越少，高吞吐量场景较少使用。

2. **kafka**

   针对大数据的消息中间件，百万级TPS的吞吐量。

   优点：性能卓越，单机写入TPS约在百万条/s，最大的优点就是吞吐量高。时效性ms级可用性非常高，kafka是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用，消费者采用Pull方式获取消息，消息有序，提供控制能够保证所有消息被消费且仅被消费一次；有优秀的第三方kafka Web管理界面Kafka-Manager；在日志领域比较成熟，被多家公司和多个开源项目使用；功能支持：功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用。

   缺点：Kafka单机超过64个队列/分区，Load会发生明显的飙高现象，队列越多，load越高，发送消息响应时间变长，通过短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，社区更新较慢；

3. **RocketMQ**

   阿里巴巴开源产品，Java实现，在设计时参考了kafka，并作出了自己的一些改进。被阿里用在订单、交易、充值、消息推送等场景

   优点：单机吞吐量十万级，可用性非常高，分布式架构，消息可以做到0丢失，MQ功能较为完善，扩展性好，支持10亿级别的消息堆积，不会因为堆积而导致性能下降，源码是Java，可以自己阅读并定制自己公司的MQ

   缺点：支持的客户端语言不多，目前是Java及c++，其中c++不成熟；社区活跃度一般，没有在MQ核心中去实现 JMS等接口，有些系统要迁移需要修改大量代码。

4. **RabbitMQ**

   2007年发布，是一个在AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。

   优点：由于erlang语言的高并发特性，性能较好，吞吐量到万级，MQ功能比较完备，健壮、稳定、易用、跨平台、支持多种语言。

   有方便实用的开源管理界面。

   缺点：商业版需要收费，学习成本较高。

### 1.4 MQ的选择

  1. kafka

     kafka主要特点是基于Pull的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。大型公司可以选用，如果有日志采集功能，肯定是首选kafka了。

  2. RocketMQ

     天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。RocketMQ在稳定性上更有优势，这些业务场景在阿里双十一经历了多次考验，如果你的业务有上述并发场景，建议可以选择RocketMQ。

  3. RabbitMQ

     结合erlang语言本身的并发优势，性能好时效性微秒级，社区活跃度也比较高，管理界面用起来十分方便，如果数据量没有那么大，中小型公司优先选择功能比较完备的RabbitMQ。

## 2.RabbitMQ

### 2.1 RabbitMQ的概念

​		RabbitMQ是一个消息中间件：它接受、存储并转发消息。可以把它当作一个快递站点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里。

### 2.2 四大核心概念

​		**生产者**

​		产生数据发送消息的程序是生产者

​		**交换机**

​		交换机是RabbitMQ非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或是把消息丢弃，这个得由交换机类型决定。

​		**队列**

​		队列是RabbitMQ内部使用的一种数据结构，尽管消息流经RabbitMQ和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式。

​		**消费者**

​		消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者、消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又可以是消费者



### 2.3 RabbitMQ 核心部分(六大模式)

1. Hello World（简单模式）
2. Work queues（工作模式）
3. Publish/Subscribe（发布订阅模式）
4. Routing（路由模式）
5. Topics（主题模式）
6. Publisher Confirms（发布确认模式）

### 2.4 各个名词介绍

​		Broker：接收和分发消息的应用，RabbitMQ Server就是Message Broker

​		Virtual host：出于多租户和安全因素设计的，把AMQP的基本组件划分到一个虚拟的分组中，类似与网络中的namespace概念。当多个不同的用户使用同一个RabbitMQ Server提供的服务时，可以划分出多个vhost，每个用户在自己的vhost创建exchange/queue等

​		Connection： publisher/consumer和broker之间的TCP连接

​		Channel：如果每一次访问RabbitMQ都建立一个Connection，在消息量大的时候建立TCP Connection的开销将是巨大的，效率也较低。Channel是在connection内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread创建单独的Channel进行通讯，AMQP method 包含了channel id 帮助客户端和message broker 识别 channel，所以channel之间是完全隔离的。Channel作为轻量级的Connection极大减少了操作系统建立TCP Connection的开销

​		Exchange： message到达broker的第一站，根据分发规则，匹配查询表中的routing key，分发消息到queue中去。常用的类型有：direct（point-to-point），topic（publish-subscribe）and fanout（multicast）

​		Queue： 消息最终被送到这里等待consumer取走

​		Binding： exchange和queue之间的虚拟连接，binding中可以包含routing key，Binding信息被保存到exchange中的查询表中，用于message的分发依据

### 2.5 安装

1. 网址

   ~~~~java
   https://www.rabbitmq.com/download.html
   ~~~~

2. 文件上传

   上传到 /usr/local/software 目录下（如果没有software需要自己创建）

3. 安装文件（按以下顺序）

   ~~~~java
   rpm -ivh erlang-21.3-1.el7.x86_64.rpm
   yum install socat -y
   rpm -ivh rabbitmq-server-3.8.8-1.el7.noarch.rpm
       (i:install简写	vh:显示安装进度)
   ~~~~

4. 常用命令

   ~~~~java
   添加开机启动RabbitMQ服务
       chkconfig rabbitmq-server on
   启动服务
       /sbin/service rabbitmq-server start
   查看服务状态
       /sbin/service rabbitmq-server status
   停止服务(选择执行)
       /sbin/service rabbitmq-server stop
   开启web管理插件
       rabbitmq-plugins enable rabbitmq_management
       
       用默认账号密码（guest）访问地址 http://192.168.118.129:15672/出现权限问题
   	User can only log in via localhost
   
   ~~~~

5. 添加一个新用户

   ~~~~java
   创建账号
       rabbitmqctl add_user admin admin
   设置用户角色
       rabbitmqctl set_user_tags admin administrator
   设置用户权限
       格式：rabbitmqctl set_permissions[-p <vhostpath>] <user> <conf> <write> <read>
       样例：rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"
       用户user_admin 具有/vhost1这个virtual host中所有资源的配置、写、读权限
   当前用户和角色
       rabbitmqctl list_users
   ~~~~

   

# 二、Hello World

## 1.依赖

~~~~java

    <!-- 指定jdk编译版本-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <!-- rabbitmq 依赖客户端-->
        <dependency>
            <groupId>com.rabbitmq</groupId>
            <artifactId>amqp-client</artifactId>
            <version>5.8.0</version>
        </dependency>
        <!-- 操作文件流的一个依赖 -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>
    </dependencies>

~~~~

## 2.消息生产者

~~~~java
package rabbitmq.one;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.one
 * @FILE_NAME: Producer
 * @Author: Jayfei-Wu
 * @create: 2023-03-06 22:47
 * @DESCRIPTION: 生产者：发消息
 */
public class Producer {


    /** 队列名称 */
    public static final String QUEUE_NAME = "hello";

    /** 发消息 */
    public static void main(String[] args) throws IOException, TimeoutException {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //设置工厂IP 连接RabbitMQ的队列
        factory.setHost("192.168.118.129");
        //设置用户名
        factory.setUsername("admin");
        //设置密码
        factory.setPassword("admin");

        //创建连接
        Connection connection = factory.newConnection();
        //获取连接中信道
        Channel channel = connection.createChannel();

        /** 生成一个队列
         *  1.队列名称
         *  2.队列里面消息是否持久化（磁盘） 默认情况消息存储在内存中
         *  3.该队列是否只供一个消费者进行消费 是否进行消息的共享 true表示可以多个消费者消费
         *  4.是否自动删除 最后一个消费者端开连接以后 该队列是否自动删除 true自动删除
         *  5.其他参数（延迟、死信等）
         * */
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);

        //发消息
        String message = "Hello World";

        /** 发送一个消息
         *  1.发送到哪个交换机
         *  2.路由的key是哪个 本次是队列名称
         *  3.其他参数信息
         *  4.发送消息的消息体
         * */
        channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
        System.out.println("消息发送完毕");
    }
}

~~~~



## 3.消息消费者

~~~~java
package rabbitmq.one;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.one
 * @FILE_NAME: Consumer
 * @Author: Jayfei-Wu
 * @create: 2023-03-06 23:17
 * @DESCRIPTION: 消费者 接收消息
 */
public class Consumer {

    /**  队列名称 */
    public static final String QUEUE_NAME = "hello";

    /** 接收消息 */
    public static void main(String[] args) throws IOException, TimeoutException {
        //创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.118.129");
        factory.setUsername("admin");
        factory.setPassword("admin");

        //建立连接
        Connection connection = factory.newConnection();
        //创建信道
        Channel channel = connection.createChannel();

        //声明 接收消息
        DeliverCallback deliverCallback = (consumerTag,message) -> {
            System.out.println(new String(message.getBody()));
        };

        // 取消消息时的回调
        CancelCallback cancelCallback = (consumerTag) -> {
            System.out.println("消息消费被中断");
        };

        /** 消费者消费消息
         *  1.消费哪个队列
         *  2.消费成功之后是否要自动应答 true表示自动应答 false表示手动应答
         *  3.消费者未成功消费的回调
         *  4.消费者取消消费的回调
         * */
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback);
    }
}

~~~~



# 三、Work Queues

​		工作队列（又称任务队列）的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的工作进程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。

## 1.轮询分发消息

### 1.1 抽取工具类

~~~~java
package rabbitmq.utils;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.utils
 * @FILE_NAME: RabbitMqUtils
 * @Author: Jayfei-Wu
 * @create: 2023-03-07 0:21
 * @DESCRIPTION: 此类为连接工厂创建信道的工具类
 */
public class RabbitMqUtils {
    
    /** 得到一个连接的channel */
    public static Channel getChannel() throws Exception {
        //创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.118.129");
        factory.setUsername("admin");
        factory.setPassword("admin");

        //建立连接
        Connection connection = factory.newConnection();
        //创建信道
        Channel channel = connection.createChannel();
        
        return channel;
    }
}

~~~~



### 1.2 启动两个工作线程

~~~~java
package rabbitmq.two;

import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import rabbitmq.utils.RabbitMqUtils;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.two
 * @FILE_NAME: Worker01
 * @Author: Jayfei-Wu
 * @create: 2023-03-07 0:25
 * @DESCRIPTION: 这是一个工作线程 （相当于之前的消费者）
 */
public class Worker01 {

    /** 队列的名称 */
    public static final String QUEUE_NAME = "hello";

    /** 接收消息 */
    public static void main(String[] args) throws Exception{
        Channel channel = RabbitMqUtils.getChannel();

        //声明 接收消息
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("接收到的消息：" + new String(message.getBody()));
        };

        // 取消消息时的回调
        CancelCallback cancelCallback = (consumerTag) -> {
            System.out.println(consumerTag + "消费者取消消费接口回调逻辑");
        };

        System.out.println("C1等待接收消息---------");
        //消息的接收
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback);
    }
}

~~~~



### 1.3 启动一个发送线程

~~~~java
package rabbitmq.two;

import com.rabbitmq.client.Channel;
import rabbitmq.utils.RabbitMqUtils;

import java.util.Scanner;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.two
 * @FILE_NAME: Task01
 * @Author: Jayfei-Wu
 * @create: 2023-03-07 2:45
 * @DESCRIPTION: 生产者 可以发送大量的消息
 */
public class Task01 {

    /** 队列名称 */
    public static final String QUEEN_NAME = "hello";

    /** 发送大量消息 */
    public static void main(String[] args) throws Exception{

        Channel channel = RabbitMqUtils.getChannel();

        // 队列声明
        channel.queueDeclare(QUEEN_NAME,false,false,false,null);
        //从控制台当中接受信息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            String message = scanner.next();
            channel.basicPublish("",QUEEN_NAME,null,message.getBytes());
            System.out.println("发送消息完成：" + message);
        }

    }
}

~~~~



## 2.消息应答

### 2.1 概念

​		消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分，突然它挂掉了，会发生什么情况。RabbitMQ一旦向消费者传递了一条信息，便立即将该消息标记为删除。在这种情况下，突然有个消费者挂掉了，我们将丢失正在处理的消息。以及后续发送给该消费者的消息，因为它无法接收到。

​		为了保证消息在发送过程中不丢失，rabbitmq引入消息应答机制，消费者在接收到消息并且处理该消息之后，告诉rabbitmq它已经处理了，rabbitmq可以把该消息删除了

### 2.2 自动应答

​		消息发送后立即被认为已经传送成功，这种模式需要在高吞吐量和数据传输安全性方面做权衡，因为这种模式如果消息在被接收到之前，消费者那边出现连接或者channel关闭，那么消息就丢失了，当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，当然这样有可能使得消费者这边由于接收太多还来不及处理的消息，导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死，所以这种模式仅适用于在消费者可以高效并以某种速率能够处理这些消息的情况下使用

​		自动应答相对不是很靠谱，它必须要有一个良好的环境，不能发生极端的情况下，才能使用自动应答。只要我接到消息，马上跟队列说已经完成，实际上并没有完成，以接收到消息为准，但接收消息不是目的，接收到消息后，会进行其他的操作。

### 2.3 消息手动应答的方法

~~~~java
A.	Channel.basicAck	(用于肯定确认)
    RabbitMQ 已知道该消息并且成功的处理消息，可以将其丢弃了
B.  Channel.basicNack	(用于否定确认)
C.	Channel.basicReject	(用于否定确认)
    与Channel.basicNack相比少一个参数
    不处理该消息了直接拒绝，可以将其丢弃了
~~~~

### 2.4 Multiple的解释

​		手动应答的好处是可以批量应答并且减少网络拥堵

~~~~java
channel.basicAck(deliveryTag,true);			//第二个参数就是批量应答
~~~~

​		Multiple 的 true 和 false 代表不同意思

​				true 代表批量应答 channel 上未应答的消息

​						比如说channel上有传送tag的消息 5，6，7，8 当前tag是 8 那么此时 5-8 的这些还未应答的消息都会被确认收到消息应答

​				false 同上面相比

​						只会应答 tag = 8 的消息 5，6，7 这三个消息依然不会被确认收到消息应答

### 2.5 消息自动重新入队

​		如果消费者由于某些原因失去连接（通道关闭，连接关闭或者TCP连接丢失），导致消息未发送ACK确认，RabbitMQ将了解到消息未完全处理，并将对其重新排队。如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。           

### 2.6 消息手动应答代码

​		默认消息采用的是自动应答，所以我们要想实现消息消费过程中不丢失，需要把自动应答改为手动应答

**生产者**

~~~~java
package rabbitmq.three;

import com.rabbitmq.client.Channel;
import rabbitmq.utils.RabbitMqUtils;

import java.util.Scanner;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.three
 * @FILE_NAME: Task02
 * @Author: Jayfei-Wu
 * @create: 2023-03-07 20:08
 * @DESCRIPTION: 消息在手动应答时不丢失，放回队列中重新消费
 */
public class Task02 {
    
    /** 队列名称 */
    public static final String TASK_QUEUE_NAME = "ack_queue";

    public static void main(String[] args) throws Exception {

        //获取信道
        Channel channel = RabbitMqUtils.getChannel();
        //声明队列
        channel.queueDeclare(TASK_QUEUE_NAME,false,false,false,null);
        
        //从控制台中输入信息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String message = scanner.next();
            channel.basicPublish("",TASK_QUEUE_NAME,null,message.getBytes());
            System.out.println("生产者发出消息：" + message);
        }
    }
}

~~~~

**消费者01**

~~~~java
package rabbitmq.three;

import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import rabbitmq.utils.RabbitMqUtils;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.three
 * @FILE_NAME: worker021
 * @Author: Jayfei-Wu
 * @create: 2023-03-07 20:15
 * @DESCRIPTION: 消息在手动应答时不丢失，放回队列重新消费
 */
public class Worker021 {

    /** 队列名称 */
    public static final String TASK_QUEUE_NAME = "ack_queue";

    /** 接收消息 */
    public static void main(String[] args) throws Exception{


        Channel channel = RabbitMqUtils.getChannel();
        System.out.println("C1 等待接收消息处理 时间较短");

        DeliverCallback deliverCallback = (consumerTag,message) -> {
            //沉睡 1S
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            System.out.println("接收到的消息：" + new String(message.getBody(),"UTF-8"));

            /** 手动应答
             *  1.消息的标记 tag
             *  2.是否批量应答
             */
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        };

        CancelCallback cancelCallback = (consumerTag) -> {
            System.out.println(consumerTag + "消费者取消消费接口回调");
        };

        //采用手动应答
        boolean autoAck = false;
        channel.basicConsume(TASK_QUEUE_NAME,autoAck,deliverCallback,cancelCallback);

    }
}

~~~~

**消费者02**

~~~~java
package rabbitmq.three;

import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import rabbitmq.utils.RabbitMqUtils;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.three
 * @FILE_NAME: worker021
 * @Author: Jayfei-Wu
 * @create: 2023-03-07 20:15
 * @DESCRIPTION: 消息在手动应答时不丢失，放回队列重新消费
 */
public class Worker022 {

    /** 队列名称 */
    public static final String TASK_QUEUE_NAME = "ack_queue";

    /** 接收消息 */
    public static void main(String[] args) throws Exception{


        Channel channel = RabbitMqUtils.getChannel();
        System.out.println("C2 等待接收消息处理 时间较短");

        DeliverCallback deliverCallback = (consumerTag,message) -> {
            //沉睡 1S
            try {
                Thread.sleep(30000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            System.out.println("接收到的消息：" + new String(message.getBody(),"UTF-8"));

            /** 手动应答
             *  1.消息的标记 tag
             *  2.是否批量应答
             */
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        };

        CancelCallback cancelCallback = (consumerTag) -> {
            System.out.println(consumerTag + "消费者取消消费接口回调");
        };

        //采用手动应答
        boolean autoAck = false;
        channel.basicConsume(TASK_QUEUE_NAME,autoAck,deliverCallback,cancelCallback);

    }
}

~~~~



### 2.7 手动应答效果展示

​		正常情况下消息发送方发送两个消息，C1和C2分别接收到消息并进行处理

|                                | aa 生产者发出消息aa |                                |
| ------------------------------ | ------------------- | ------------------------------ |
|                                | bb 生产者发出bb     |                                |
| C1等待接收消息 处理时间较短... |                     | C2等待接收消息 处理时间较长... |
| 接收到消息aa                   |                     | 接收到消息bb                   |

​		在发送者发送消息dd，发送消息之后，把C2消费者停掉，按理说该C2来处理该消息，但是由于它处理时间较长，在还未处理完，也就是说C2还没有执行ack代码的时候，C2被停掉了，此时会看到消息被C1接收到了，说明消息dd被重新入队，然后分配给能处理消息的C1处理了。

## 3.RabbitMQ持久化

### 3.1 概念

​		刚刚已经看了如何处理任务不丢失的情况，但是如何保障当RabbitMQ服务停掉以后消息生产者发送过来的消息不丢失。默认情况下RabbitMQ退出或由于某种原因崩溃时，它忽视队列和消息，除非告知它不要这样做。

​		确保消息不会丢失需要做两件事：**将队列和消息都标记为持久化。**

### 3.2 队列如何实现持久化

​		之前我们创建的队列都是非持久化的，RabbitMQ如果重启的话，该队列就会被删除掉，如果要队列实现持久化，需要在声明队列的时候把 durable 参数设为持久化

~~~~java
boolean durable = true;
channel.queueDeclare(ACK_QUEUE_NAME,durable,false,false,null);
~~~~

​		但是需要注意的就是如果之前声明的队列不是持久化的，需要把原先队列先删除，或者重新创建一个持久化的队列，不然就会报错

​		持久化的队列，在控制台的UI显示区中 Features 列，会出现大写字母 D

​		这个时候即使重启RabbitMQ队列也依然存在

### 3.3 消息实现持久化

​		消息实现持久化需要在消息生产者修改代码，在发布消息时，设置第三个参数

~~~~java
        channel.basicPublish("",TASK_QUEUE_NAME,null,message.getBytes("UTF-8"));
		//当 durable 为 true 的时候
		channel.basicPublish("",TASK_QUEUE_NAME,
                             MessageProperties.PERSISTENT_TEXT_PLAIN,message.getBytes("UTF-8"));

~~~~

​		将消息标记为持久化并不能完全保证不会丢失消息。尽管它告诉 RabbitMQ 将消息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候，但是还没有存储完，消息还在缓存的一个间隔点。此时并没有真正写入磁盘。持久性保证并不强，但是对于我们的简单任务队列而言，这已经够够的了。如果需要更强有力的持久化策略，后面还有发布确认。

### 3.4 不公平分发

​		在之前了解到 RabbitMQ 分发消息采用的轮询分发，但是在某些场景下这种策略并不是很好。比如有两个消费者在处理任务，其中一个消费者1处理任务的速度非常快，而另一个消费者2处理速度却很慢，这个时候若还是采用轮询分发的话，就会让处理速度快的这个消费者，很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活，这种分配方式在这种情况下其实就不太好，但是RabbitMQ并不知道这种情况，它依然会公平的轮询分发。

​		为了避免这种情况，可以在消费者代码设置参数 channel.basicQos(1);

~~~~java
int prefetchCount = 1;
channel.basicQos(PrefetchCount);
~~~~

​		设置后，可以在控制台的UI界面 Channels栏里面看到 Prefetch count 下对应值为 1

​		就是说，如果这个任务，我还没有处理完或者我还没有应答，那就先别给我，我目前只能处理一个任务，然后 RabbitMQ 就会把该任务分配给没有那么忙的那个空闲消费者，当然如果所有的消费者都没有完成手上任务，队列还在不停的添加新任务，队列就又可能会遇到队列被撑满的情况，这个时候就只能添加新的worker或者改变其他存储任务的策略

### 3.5 预取值

​		本身消息的发送就是异步发送的，所以在任何时候，channel 上肯定不止只有一个消息，另外来自消费者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，因此我们开发人员应**限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题**。

​		这个时候就可以通过 **basicQos** 方法设置 “**预取计数**” 值来完成的。**该值定义通道上允许的未确认消息的最大数量**。一旦数量达到配置的数量，RabbitMQ 将停止在通道上传递更多消息，除非至少有一个未确认的消息被确认，例如，假设在通道上有未确认的消息 5，6，7，8，并且通道的预取计数设置为4，此时 RabbitMQ 将不会在该通道上再传递任何消息，除非至少有一个未应答的消息被 ack。如果说 tag=6 这个消息刚刚被确认 ack，RabbitMQ 将会感知到这个情况，并再发送一条消息。

​		消息应答和Qos预留值对用户吞吐量有重大影响。通常，增加预取值将提高向消费者传递消息的速度。虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的RAM消耗，应该小心使用具有无限预处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到一个合适的预取值是一个反复实验的过程，不同的负载该值取值也不同，100-300 范围内的值通常可提供最佳的吞吐量，并且不会给消费者带来太大的风险。

​		预取值为1是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境中，对于大多数应用来说，稍微高一点的值将是最佳的。



# 四、发布确认

## 1.发布确认原理

​		生产者将信道设置成 confirm 模式，所有在该信道上面发布的消息都将会被指派一个唯一的ID（从1开始），一旦消息被投递到所有匹配的队列之后，broker 就会发送一个确认给生产者（包含这个消息的唯一ID），这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，broker 回传给生产者的确认消息中 delivery-tag 域包含了确认消息的序列号，此外 broker 也可以设置 basic.ack 的multiple 域。表示到这个序列号之前的所有消息都已经得到了处理

​		confirm 模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时，继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果 RabbitMQ因为自身内部错误导致消息丢失，就会发送一条 nack 消息，生产者应用程序同样可以在回调方法中处理该 nack 消息。

## 2.发布确认的策略

### 2.1 开启发布确认的方法

​		发布确认默认是没有开启的，如果要开启需要调用方法 confirmSelect , 每当要使用发布确认，都需要在 channel 上调用该方法

~~~~java
Channel channel = connection.createChannel;
channel.confirmSelect();
~~~~



### 2.2 单个确认发布

​		简单的确认方式，它是一种同步确认发布的方式，也就是发布一个消息之后，只有它被确认发布，后续的消息才能继续发布，waitForConfirmsOrDie(long) 这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常

​		这种确认方式有一个最大的缺点就是：发布速度特别的慢，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能已经足够了

~~~~java
  public static void publishMessageIndividually() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName,true,false,false,null);

        // 开启发布确认
        channel.confirmSelect();
        // 记录开始时间
        long begin = System.currentTimeMillis();

        // 批量发消息
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i + "";
            channel.basicPublish("",queueName,null,message.getBytes());
            // 单个消息就马上发布确认
            boolean flag = channel.waitForConfirms();
            if (flag) {
                System.out.println("消息发送成功");
            }
        }

        // 记录结束时间
        long end = System.currentTimeMillis();
        System.out.println("发布" + MESSAGE_COUNT + "个单独确认消息，耗时" + (end-begin) + "ms");

    }
~~~~

### 2.3 批量确认发布

​		上面那种方式非常慢，与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量，当然这种方式的缺点就是：当发生故障导致发布出现问题时，不知道是哪个消息出现问题了，必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布

~~~~java
    public static void publishMessageBatch() throws Exception{
        Channel channel = RabbitMqUtils.getChannel();
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName,true,false,false,null);

        // 开启发布确认
        channel.confirmSelect();
        // 记录开始时间
        long begin = System.currentTimeMillis();

        // 批量确认消息大小
        int batchSize = 100;

        // 批量发送消息，并且批量发布确认
        for (int i = 0; i < MESSAGE_COUNT; i++) {

            String message = i + "";
            channel.basicPublish("",queueName,null,message.getBytes());

            // 判断达到100条的时候 批量确认一次
            if (batchSize == 100) {
                // 发布确认
                channel.waitForConfirms();
            }
        }

        // 记录结束时间
        long end = System.currentTimeMillis();
        System.out.println("发布" + MESSAGE_COUNT + "个批量确认消息，耗时" + (end-begin) + "ms");
    }
~~~~

### 2.4 异步确认发布

​		异步确认虽然编程逻辑上比上两个要复杂，但是性价比最高，无论是可靠性还是效率都没得说，它是利用回调函数来达到消息可靠性传递的，这个中间件也是通过回调函数来保证是否投递成功。

 ~~~~java
     public static void publishMessageAsync() throws Exception {
 
         Channel channel = RabbitMqUtils.getChannel();
         String queueName = UUID.randomUUID().toString();
         channel.queueDeclare(queueName,true,false,false,null);
 
         // 开启发布确认
         channel.confirmSelect();
         // 记录开始时间
         long begin = System.currentTimeMillis();
 
         // 消息确认成功 回调函数
         ConfirmCallback ackCallback = (deliveryTag,multiple) -> {
             System.out.println("确认的消息" + deliveryTag);
         };
         // 消息确认失败，回调函数
         // 消息标记/是否为批量确认
         ConfirmCallback nackCallback = (deliveryTag,multiple) -> {
             System.out.println("未确认的消息：" + deliveryTag);
         };
 
         // 准备消息的监听器，来监听哪些消息成功了，哪些消息失败了  异步通知
         channel.addConfirmListener(ackCallback,nackCallback);
 
         //批量发送消息
         for (int i = 0; i < MESSAGE_COUNT; i++) {
             String message = "消息" + i;
             channel.basicPublish("",queueName,null,message.getBytes());
         }
 
 
         // 记录结束时间
         long end = System.currentTimeMillis();
         System.out.println("发布" + MESSAGE_COUNT + "个异步确认消息，耗时" + (end-begin) + "ms");
         
     }
 ~~~~



### 2.5 如何处理异步未确认消息

​		最好的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列，比如说用 ConcurrentLinkedQueue 这个队列在 confirm callbacks 与发布线程之间进行消息的传递

~~~~java
  public static void publishMessageAsync() throws Exception {

        Channel channel = RabbitMqUtils.getChannel();
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName,true,false,false,null);

        // 开启发布确认
        channel.confirmSelect();

        /** 线程安全有序的一个哈希表 适用于高并发的情况下
         *  1.轻松的将序号与消息进行关联
         *  2.轻松的批量删除条目 只要给到序号
         *  3.支持高并发（多线程）
         * */
        ConcurrentSkipListMap<Long,String> outstandingConfirms = new ConcurrentSkipListMap<>();


        // 消息确认成功 回调函数
        ConfirmCallback ackCallback = (deliveryTag,multiple) -> {

            if (multiple) {
                // 第二步 删除已经确认的消息 剩下的就是未确认的消息
                ConcurrentNavigableMap<Long, String> confirmed = outstandingConfirms.headMap(deliveryTag);
                confirmed.clear();
            } else {
                outstandingConfirms.remove(deliveryTag);
            }
            System.out.println("确认的消息" + deliveryTag);
        };
        // 消息确认失败，回调函数
        // 消息标记/是否为批量确认
        ConfirmCallback nackCallback = (deliveryTag,multiple) -> {
            // 第三步 打印一下未确认的消息有哪些
            String message = outstandingConfirms.get(deliveryTag);
            System.out.println("未确认的消息是：" + message + "--------" + "未确认的消息tag：" + deliveryTag);
        };

        // 准备消息的监听器，来监听哪些消息成功了，哪些消息失败了  异步通知
        channel.addConfirmListener(ackCallback,nackCallback);

        // 记录开始时间
        long begin = System.currentTimeMillis();

        //批量发送消息
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = "消息" + i;
            channel.basicPublish("",queueName,null,message.getBytes());
            // 第一步 此处记录下所有要发送的消息    消息的总和
            outstandingConfirms.put(channel.getNextPublishSeqNo(),message);
        }


        // 记录结束时间
        long end = System.currentTimeMillis();
        System.out.println("发布" + MESSAGE_COUNT + "个异步确认消息，耗时" + (end-begin) + "ms");

    }
~~~~



### 2.6 以上3种发布确认速度对比

~~~~java
    /** 批量发消息的个数 */
    public static final int MESSAGE_COUNT = 1000;

    public static void main(String[] args) throws Exception {
		// 1.单个确认   发布1000个单独确认消息，耗时1126ms
       ConfirmMessage.publishMessageIndividually();
        // 2.批量确认   发布1000个批量确认消息，耗时619ms
        ConfirmMessage.publishMessageBatch();
        // 3.异步批量确认  发布1000个异步确认消息，耗时100ms
        ConfirmMessage.publishMessageAsync();
    }
~~~~

​		单个确认发布消息

​				同步等待确认，简单，但吞吐量非常有限。

​		批量确认发布消息

​				批量同步等待确认，简单，合理的吞吐量，一旦出现问题很难推断出是哪条消息出现了问题

​		异步确认发布消息

​				最佳性能和资源使用，在出现错误的情况下可以很好的控制，但是实现起来稍微难些

# 五、交换机

​		之前是创建一个工作队列，假设的是工作队列背后，每个任务都恰好交给一个消费者（工作进程）。而”发布/订阅“模式将消息传达给多个消费者

​		示例将构建一个简单的日志系统。它将由两个程序组成：第一个程序将发出日志消息，第二个程序是消费者。其中我们会启动两个消费者，其中一个消费者接收到消息后把日志存储在磁盘，另外一个消费者接收到消息后把消息打印在屏幕上，事实上第一个程序发出的日志消息将广播给所有的消费者

## 1.Exchanges

### 1.1 Exchanges 概念

​		RabbitMQ消息传递模型的核心思想是：**生产者生产的消息从不会直接发送到队列。**实际上，通常生产者甚至都不知道这些消息传递到了哪些队列中。

​		相反，**生产者只能将消息发送到交换机（exchange）**，交换机工作的内容非常简单，一方面它接受来自生产者的消息，另一方面将它们推入队列。交换机必须确切知道如何处理收到的消息。是应该把这些消息放到特定队列还是说把它们放到许多队列中，还是说应该丢弃他们。这就由交换机的类型来决定。

### 1.2 Exchanges 的类型

​		总共有以下类型：

​		**直接（direct），主题（topic），标题（headers），扇出（fanout）**

### 1.3 无名exchange

​		之前没有使用交换机（发布时参数标为空串），但仍然能够将消息发送到队列。是因为使用的是默认交换。

~~~~java
            channel.basicPublish("",queueName,null,message.getBytes());
~~~~

​		第一个参数是交换机的名称。空字符串表示默认或无名称交换机：消息能路由发送到队列中其实是由 routingKey(bindingkey) 绑定 key 指定的，如果它存在的话

## 2.临时队列

​		之前使用的是具有特定名称的队列（例如 hello 和 ack_queue ）。队列的名称对我们来说很重要，需要指定我们的消费者取消费哪个队列的消息。

​		每当我们连接到 Rabbit 时，我们都需要一个全新的空队列，为此我们可以创建一个具有随即名称的队列，或者更好的情况就是让服务器为我们选择一个随机队列名称。其次，一旦我们断开了消费者的连接，队列将被自动删除。

​		创建临时队列的方式如下：

~~~~java
	String queueName = channel.queueDeclare().getQueue();
~~~~



## 3.绑定（bindings）

​		绑定就是 exchange 和 queue 之间的桥梁，它告诉我们 exchange 和哪个队列进行了绑定关系。

## 4.Fanout

### 4.1 Fanout 介绍

​		Fanout 这种类型非常简单。它将接收到的所有消息广播到它知道的所有队列中。系统中默认有些 exchange 类型

### 4.2 Fanout 实践

**消费者代码**（两个消费者，代码一样的）

~~~~java
package rabbitmq.five;

import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import rabbitmq.utils.RabbitMqUtils;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.five
 * @FILE_NAME: ReceiveLogs01
 * @Author: Jayfei-Wu
 * @create: 2023-03-09 0:46
 * @DESCRIPTION: 负载消息的接收
 */
public class ReceiveLogs01 {

    /** 交换机的名称 */
    public static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMqUtils.getChannel();

        //声明一个交换机
        channel.exchangeDeclare(EXCHANGE_NAME,"fanout");

        /** 声明一个队列 临时队列
         *  生成一个临时队列 队列的名称是随机的
         *  当消费者断开与队列的连接的时候 队列就自动删除了
         * */
        String queueName = channel.queueDeclare().getQueue();

        /** 绑定交换机与队列 */
        channel.queueBind(queueName,EXCHANGE_NAME,"");
        System.out.println("等待接收消息，把接收到的消息打印在屏幕上... ... ...");

        // 消费者接收消息回调
        DeliverCallback deliverCallback = (consumerTag,message) -> {
            System.out.println("ReceiveLogs01控制台打印接收到的消息：" + new String(message.getBody(),"UTF-8"));
        };
        // 消费者取消消息回调
        CancelCallback cancelCallback = (consumerTag) -> {

        };

        channel.basicConsume(queueName,true,deliverCallback,cancelCallback);

    }

}
~~~~

**生产者代码**

~~~~java
package rabbitmq.five;

import com.rabbitmq.client.Channel;
import rabbitmq.utils.RabbitMqUtils;

import java.util.Scanner;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.five
 * @FILE_NAME: EmitLog
 * @Author: Jayfei-Wu
 * @create: 2023-03-09 0:46
 * @DESCRIPTION: 生产者 负责发消息给交换机
 */
public class EmitLog {

    /** 交换机的名称 */
    public static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws Exception {

        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(EXCHANGE_NAME,"fanout");

        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String message = scanner.next();
            channel.basicPublish(EXCHANGE_NAME,"",null,message.getBytes("UTF-8"));
            System.out.println("生产者发出消息：" + message);
        }
    }

}

~~~~



## 5.Direct exchange

### 5.1 回顾

​		之前通过 Fanout 类型构建了一个简单的日志记录系统，能够向许多消费者广播日志接收消息。接下来将向其中添加一些特别的功能，比如只让某个消费者订阅发布的部分消息。例如只把严重错误消息定向存储到日志文件（以节省磁盘空间），同时仍然能够在控制台上打印所有日志消息。

### 5.2 Direct exchange 介绍

​		之前将所有消息广播给所有消费者，现在要将日志消息写入磁盘的程序仅接收 errors，而不存储 warning 或 info 日志。Fanout 这种交换类型并不能完成，它只能无意识的广播，接下来将使用 direct 类型进行替换，这种类型的工作方式是，消息只去到它绑定的 routingKey 队列中去。

### 5.3 多重绑定

​		如果 exchange 的绑定类型是 direct，但是它绑定的多个队列的 key 如果都相同，在这种情况下，虽然绑定类型是 direct 但是它表现的就和 Fanout 有点类似了

### 5.4 实践

**生产者代码**

~~~~java
package rabbitmq.six;

import com.rabbitmq.client.Channel;
import rabbitmq.utils.RabbitMqUtils;

import java.util.Scanner;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.six
 * @FILE_NAME: DirectLogs
 * @Author: Jayfei-Wu
 * @create: 2023-03-09 4:15
 * @DESCRIPTION: TODO
 */
public class DirectLogs {

    /** 交换机的名称 */
    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws Exception {

        Channel channel = RabbitMqUtils.getChannel();

        Scanner scanner = new Scanner(System.in);

        while (scanner.hasNext()) {
            String message = scanner.next();
            channel.basicPublish(EXCHANGE_NAME,"info",null,message.getBytes("UTF-8"));
            channel.basicPublish(EXCHANGE_NAME,"warning",null,message.getBytes("UTF-8"));
            channel.basicPublish(EXCHANGE_NAME,"error",null,message.getBytes("UTF-8"));
            System.out.println("生产者发出消息：" + message);
        }
    }

}

~~~~

**消费者1代码**

~~~~java
package rabbitmq.six;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import rabbitmq.utils.RabbitMqUtils;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.six
 * @FILE_NAME: ReceiveLogsDirect01
 * @Author: Jayfei-Wu
 * @create: 2023-03-09 4:05
 * @DESCRIPTION: TODO
 */
public class ReceiveLogsDirect01 {

    /** 交换机名字 */
    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws  Exception {

        Channel channel = RabbitMqUtils.getChannel();

        // 声明一个交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);

        // 声明一个队列
        channel.queueDeclare("console",false,false,false,null);

        channel.queueBind("console",EXCHANGE_NAME,"info");
        channel.queueBind("console",EXCHANGE_NAME,"warning");

        // 消费者接收消息回调
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("ReceiveLogsDirect01控制台打印接收到的消息：" + new String(message.getBody(),"UTF-8"));
        };
        // 消费者取消消息回调
        CancelCallback cancelCallback = (consumerTag) -> {

        };

        channel.basicConsume("console",true,deliverCallback,cancelCallback);


    }
}

~~~~

**消费者2代码**

~~~~java
package rabbitmq.six;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import rabbitmq.utils.RabbitMqUtils;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.six
 * @FILE_NAME: ReceiveLogsDirect02
 * @Author: Jayfei-Wu
 * @create: 2023-03-09 4:13
 * @DESCRIPTION: TODO
 */
public class ReceiveLogsDirect02 {

    /** 交换机名字 */
    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws  Exception {

        Channel channel = RabbitMqUtils.getChannel();

        // 声明一个交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);

        // 声明一个队列
        channel.queueDeclare("disk",false,false,false,null);

        channel.queueBind("disk",EXCHANGE_NAME,"error");

        // 消费者接收消息回调
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("ReceiveLogsDirect02控制台打印接收到的消息：" + new String(message.getBody(),"UTF-8"));
        };
        // 消费者取消消息回调
        CancelCallback cancelCallback = (consumerTag) -> {

        };

        channel.basicConsume("disk",true,deliverCallback,cancelCallback);


    }
}

~~~~



## 6.Topics

### 6.1 之前类型存在的问题

​		之前的 Fanout 模式可以随意广播，Direct 可以有选择的接收，但它仍然存在局限性。比如说我们想接收的日志类型有 info.base 和 info.advantage，某个队列执行要 info.base 的消息，这时候 direct 就办不到了，只能使用 Topic 类型

### 6.2 Topic 的要求

​		发送到 Topic 类型交换机的消息的 routing_key 不能随意写，必须满足一定的要求，它必须是一个单词列表，以点号分隔开。这些单词可以是任意单词，比如说：”stock.usd.nyse“, "nyse,vmw"。这个单词列表最多不能超过 255 个字节

​		在这个规则列表中，有两个替换符是需要注意的：

​				*（星号）可以代替一个单词

​				#（井号）可以替代零个或多个单词

### 6.3 Topic 匹配案例

​		Q1 --> 绑定的是

​					中间带 orange 带3个单词的字符串     ( *.orange. *)

​		Q2 --> 绑定的是

​					最后一个单词是 rabbit 的3个单词	（*. *.rabbit）

​					第一个单词是 lazy 的多个单词		（lazy.#）

quick.orange.rabbit				被队列 Q1Q2 接收到

lazy.orange.elephant			  被队列 Q1Q2 接收到

quick.orange.fox					  被队列 Q1 接收到

lazy.brown.fox						  被队列 Q2 接收到

lazy.pink.rabbit						 虽然满足两个绑定 但只被队列 Q2 接收一次

quick.brown.fox						不匹配任何绑定，不会被任何队列接收到，会被丢弃

quick.orange.male.rabbit		是四个单词，不匹配任何绑定，会被丢弃

lazy.orange.male.rabbit			是四个单词，但是匹配 Q2



​		当队列绑定关系是下列情况时需要注意：

​		当一个队列绑定键是 # ，那么这个队列将接收所有数据，类似 Fanout

​		如果队列绑定键中没有 # 和 * 的出现，那么该队列绑定类型就是 Direct 了

### 6.4 实践

**生产者代码**

~~~~java
package rabbitmq.seven;

import com.rabbitmq.client.Channel;
import rabbitmq.utils.RabbitMqUtils;

import java.util.HashMap;
import java.util.Map;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.seven
 * @FILE_NAME: EmitLogTopic
 * @Author: Jayfei-Wu
 * @create: 2023-03-09 5:54
 * @DESCRIPTION: 生产者
 */
public class EmitLogTopic {

    /** 交换机名称 */
    public static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws Exception {

        Channel channel = RabbitMqUtils.getChannel();

        Map<String,String> bindingKeyMap = new HashMap<>();
        bindingKeyMap.put("quick.orange.rabbit","被队列 Q1Q2接收到");
        bindingKeyMap.put("lazy.orange.elephant","被队列Q1Q2接收到");
        bindingKeyMap.put("quick.orange.fox","被队列Q1接收到");
        bindingKeyMap.put("lazy.brown.fox","被队列Q2接收到");
        bindingKeyMap.put("lazy.pink.rabbit","虽然满足两个绑定 但只被队列Q2接收一次");
        bindingKeyMap.put("quick.brown.fox","不匹配任何绑定，不会被任何队列接收到，会被丢弃");
        bindingKeyMap.put("quick.orange.male.rabbit","是四个单词，不匹配任何绑定，会被丢弃");
        bindingKeyMap.put("lazy.orange.male.rabbit","是四个单词，但是匹配Q2");

        for (Map.Entry<String, String> bindingKeyEntry : bindingKeyMap.entrySet()) {
            String routingKey = bindingKeyEntry.getKey();
            String message = bindingKeyEntry.getValue();
            channel.basicPublish(EXCHANGE_NAME,routingKey,null,message.getBytes("UTF-8"));
            System.out.println("生产者发出消息：" + message);
        }
    }
}

~~~~



**消费者1代码**

~~~~java
package rabbitmq.seven;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import rabbitmq.utils.RabbitMqUtils;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.seven
 * @FILE_NAME: ReceiveLogsTopic01
 * @Author: Jayfei-Wu
 * @create: 2023-03-09 5:39
 * @DESCRIPTION: 声明主题交换机 及相关队列
 *                  消费者 C1
 */
public class ReceiveLogsTopic01 {

    /** 交换机名称 */
    public static final String EXCHANGE_NAME = "topic_logs";

    /** 接收消息 */
    public static void main(String[] args) throws Exception {

        Channel channel = RabbitMqUtils.getChannel();

        // 声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);

        // 声明队列
        String queueName = "Q1";
        channel.queueDeclare(queueName,false,false,false,null);
        channel.queueBind(queueName,EXCHANGE_NAME,"*.orange.*");

        System.out.println("等待接收消息... ... ...");

        // 消费者接收消息回调
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println(new String(message.getBody(),"UTF-8"));
            System.out.println("接收队列： " + queueName + " 绑定键： " + message.getEnvelope().getRoutingKey());
        };

        // 消费者取消消息回调
        CancelCallback cancelCallback = (consumerTag) -> {};
        // 接收消息
        channel.basicConsume(queueName,true,deliverCallback,cancelCallback);

    }

}

~~~~



**消费者2代码**

~~~~java
package rabbitmq.seven;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import rabbitmq.utils.RabbitMqUtils;

/**
 * @PROJECT_NAME: myRabbitmq
 * @PACKAGE_NAME: rabbitmq.seven
 * @FILE_NAME: ReceiveLogsTopic02
 * @Author: Jayfei-Wu
 * @create: 2023-03-09 5:51
 * @DESCRIPTION: 消费者 C2
 */
public class ReceiveLogsTopic02 {

    /** 交换机名称 */
    public static final String EXCHANGE_NAME = "topic_logs";

    /** 接收消息 */
    public static void main(String[] args) throws Exception {

        Channel channel = RabbitMqUtils.getChannel();

        // 声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);

        // 声明队列
        String queueName = "Q2";
        channel.queueDeclare(queueName,false,false,false,null);
        channel.queueBind(queueName,EXCHANGE_NAME,"*.*.rabbit");
        channel.queueBind(queueName,EXCHANGE_NAME,"lazy.#");

        System.out.println("等待接收消息... ... ...");

        // 消费者接收消息回调
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println(new String(message.getBody(),"UTF-8"));
            System.out.println("接收队列： " + queueName + " 绑定键： " + message.getEnvelope().getRoutingKey());
        };

        // 消费者取消消息回调
        CancelCallback cancelCallback = (consumerTag) -> {};
        // 接收消息
        channel.basicConsume(queueName,true,deliverCallback,cancelCallback);

    }

}

~~~~



# 六、死信队列









