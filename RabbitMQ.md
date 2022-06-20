1.MQ的基本概念

MQ全称 Message Queue（消息队列），是在消息的传输过程中保存消息的容器。多用于分布式系统之间进

行通信。

MQ，消息队列，存储消息的中间件

优势：应用解耦 异步提速 削峰填谷

劣势 系统可用性降低  系统复杂度提高  一致性问题

应用解耦：提高系统容错性和可维护性

异步提速：提升用户体验和系统吞吐量

削峰填谷：提高系统稳定性

声明条件下使用MQ   

生产者不需要从消费者处获得反馈  容许短暂的不一致性。 确实是用了有效果。即解耦、提速、削峰这些方面的收益，超过加入MQ，管理MQ这些成本。

常见的MQ产品 RabbitMQ、RocketMQ、ActiveMQ、Kafka、ZeroMQ、MetaMq

AMQP Advanced Message Queuing Protocol（高级消息队列协议）

RabbitMQ 提供了 6 种工作模式：简单模式、work queues、Publish/Subscribe 发布与订阅模式、Routing

路由模式、Topics 主题模式、RPC 远程调用模式

JMS 

Java 消息服务（JavaMessage Service）应用程序接口

2.RabbitMQ快速入门

依赖

```pom
        <dependency>
            <groupId>com.rabbitmq</groupId>
            <artifactId>amqp-client</artifactId>
            <version>5.6.0</version>
        </dependency>
```



```java
package com.pinkhat.producer;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * @Classname: Producer_HelloWord
 * @Auther: Created by 张文 on 2022/2/22.
 * @Description:
 */
public class Producer_HelloWord {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */

        channel.queueDeclare("hello_world",true,false,false,null);
        /**
         *exchange：交换机名称
         * routingKey：路由名称
         * prop:配置消息
         * body:发送的消息数据
         *
         */
        String boby = "hello rabbit~~~~";
        channel.basicPublish("","hello_world",null,boby.getBytes());
        channel.close();
        connection.close();
    }

}

```

消费者

```java
public class Producer_HelloWord {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */

        channel.queueDeclare("hello_world",true,false,false,null);
        /**
         *exchange：交换机名称
         * routingKey：路由名称
         * prop:配置消息
         * body:发送的消息数据
         *
         */
        String boby = "hello rabbit~~~~";
        channel.basicPublish("","hello_world",null,boby.getBytes());
        channel.close();
        connection.close();
    }

}

```

工作队列模式：10条消息 2个机子 一个5台

生产者

```
public class Producer_WorkQueue {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */

        channel.queueDeclare("work_queue",true,false,false,null);
        /**
         *exchange：交换机名称
         * routingKey：路由名称
         * prop:配置消息
         * body:发送的消息数据
         *
         */

        for (int i = 0; i < 10; i++) {
          String  body=i+"hello rabbit~~~~";
            channel.basicPublish("","work_queue",null,body.getBytes());
        }


        channel.close();
        connection.close();
    }
}
```

消费者1

```java
public class Consumer_QueueWork {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */

        channel.queueDeclare("work_queue",true,false,false,null);
    /**
     *queue:队列名称
     * autoAck 是否自动确认
     * callback 回调接口
     */

        Consumer consumer = new DefaultConsumer(channel){
            /*
                 回调方法
                 1.consumerTag:标识
                 2.envelope: 获取一些真实的消息
                 3.properties 配置消息
                 4.body数据
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
//                System.out.println(consumerTag);
//                System.out.println(envelope);
//                System.out.println(properties);
                System.out.println(new String(body));
            }
        };

 channel.basicConsume("work_queue",true,consumer);
 //不要关闭
    }
}

```

消费者2

```java
public class Consumer_QueueWork2 {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */

        channel.queueDeclare("work_queue",true,false,false,null);
    /**
     *queue:队列名称
     * autoAck 是否自动确认
     * callback 回调接口
     */

        Consumer consumer = new DefaultConsumer(channel){
            /*
                 回调方法
                 1.consumerTag:标识
                 2.envelope: 获取一些真实的消息
                 3.properties 配置消息
                 4.body数据
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
//                System.out.println(consumerTag);
//                System.out.println(envelope);
//                System.out.println(properties);
                System.out.println(new String(body));
            }
        };

 channel.basicConsume("work_queue",true,consumer);
 //不要关闭
    }
}

```

订阅模式

生产者

```java
public class Producer_PubSub {
    public static void main(String[] args) throws IOException, TimeoutException {

        //1.创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //2. 设置参数
        factory.setHost("172.16.98.133");//ip  默认值 localhost
        factory.setPort(5672); //端口  默认值 5672
        factory.setVirtualHost("/itcast");//虚拟机 默认值/
        factory.setUsername("heima");//用户名 默认 guest
        factory.setPassword("heima");//密码 默认值 guest
        //3. 创建连接 Connection
        Connection connection = factory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
       /*

       exchangeDeclare(String exchange, BuiltinExchangeType type, boolean durable, boolean autoDelete, boolean internal, Map<String, Object> arguments)
       参数：
        1. exchange:交换机名称
        2. type:交换机类型
            DIRECT("direct"),：定向
            FANOUT("fanout"),：扇形（广播），发送消息到每一个与之绑定队列。
            TOPIC("topic"),通配符的方式
            HEADERS("headers");参数匹配

        3. durable:是否持久化
        4. autoDelete:自动删除
        5. internal：内部使用。 一般false
        6. arguments：参数
        */

       String exchangeName = "test_fanout";
        //5. 创建交换机
        channel.exchangeDeclare(exchangeName, BuiltinExchangeType.FANOUT,true,false,false,null);
        //6. 创建队列
        String queue1Name = "test_fanout_queue1";
        String queue2Name = "test_fanout_queue2";
        channel.queueDeclare(queue1Name,true,false,false,null);
        channel.queueDeclare(queue2Name,true,false,false,null);
        //7. 绑定队列和交换机
        /*
        queueBind(String queue, String exchange, String routingKey)
        参数：
            1. queue：队列名称
            2. exchange：交换机名称
            3. routingKey：路由键，绑定规则
                如果交换机的类型为fanout ，routingKey设置为""
         */
        channel.queueBind(queue1Name,exchangeName,"");
        channel.queueBind(queue2Name,exchangeName,"");

        String body = "日志信息：张三调用了findAll方法...日志级别：info...";
        //8. 发送消息
        channel.basicPublish(exchangeName,"",null,body.getBytes());

        //9. 释放资源
        channel.close();
        connection.close();

    }
}

```

消费者2

```java
public class Consumer_SubPub2 {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */
        String queue1Name = "test_fanout_queue1";
        String queue2Name = "test_fanout_queue2";
        channel.queueDeclare(queue2Name,true,false,false,null);
    /**
     *queue:队列名称
     * autoAck 是否自动确认
     * callback 回调接口
     */

        Consumer consumer = new DefaultConsumer(channel){
            /*
                 回调方法
                 1.consumerTag:标识
                 2.envelope: 获取一些真实的消息
                 3.properties 配置消息
                 4.body数据
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
//                System.out.println(consumerTag);
//                System.out.println(envelope);
//                System.out.println(properties);
                System.out.println(new String(body));
                System.out.println("将数据包存到数据库");
            }
        };

 channel.basicConsume(queue2Name,true,consumer);
 //不要关闭
    }
}

```

消费者1

```java
public class Consumer_PubSub1 {
    public static void main(String[] args) throws IOException, TimeoutException {

        //1.创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //2. 设置参数
        factory.setHost("172.16.98.133");//ip  默认值 localhost
        factory.setPort(5672); //端口  默认值 5672
        factory.setVirtualHost("/itcast");//虚拟机 默认值/
        factory.setUsername("heima");//用户名 默认 guest
        factory.setPassword("heima");//密码 默认值 guest
        //3. 创建连接 Connection
        Connection connection = factory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();


        String queue1Name = "test_fanout_queue1";
        String queue2Name = "test_fanout_queue2";


        /*
        basicConsume(String queue, boolean autoAck, Consumer callback)
        参数：
            1. queue：队列名称
            2. autoAck：是否自动确认
            3. callback：回调对象

         */
        // 接收消息
        Consumer consumer = new DefaultConsumer(channel){
            /*
                回调方法，当收到消息后，会自动执行该方法

                1. consumerTag：标识
                2. envelope：获取一些信息，交换机，路由key...
                3. properties:配置信息
                4. body：数据

             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
              /*  System.out.println("consumerTag："+consumerTag);
                System.out.println("Exchange："+envelope.getExchange());
                System.out.println("RoutingKey："+envelope.getRoutingKey());
                System.out.println("properties："+properties);*/
                System.out.println("body："+new String(body));
                System.out.println("将日志信息打印到控制台.....");
            }
        };
        channel.basicConsume(queue1Name,true,consumer);


        //关闭资源？不要

    }
}


```



路由模式生产者

```java
public class Producer_Routing {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
     /*

       exchangeDeclare(String exchange, BuiltinExchangeType type, boolean durable, boolean autoDelete, boolean internal, Map<String, Object> arguments)
       参数：
        1. exchange:交换机名称
        2. type:交换机类型
            DIRECT("direct"),：定向
            FANOUT("fanout"),：扇形（广播），发送消息到每一个与之绑定队列。
            TOPIC("topic"),通配符的方式
            HEADERS("headers");参数匹配

        3. durable:是否持久化
        4. autoDelete:自动删除
        5. internal：内部使用。 一般false
        6. arguments：参数
        */
        String exchangeName = "test_direct";
        channel.exchangeDeclare(exchangeName, BuiltinExchangeType.DIRECT,true,false,false,null);
        String queue1Name = "test_direct_queue1";
        String queue2Name = "test_direct_queue2";
        channel.queueDeclare(queue1Name,true,false,false,null);
        channel.queueDeclare(queue2Name,true,false,false,null);
        channel.queueBind(queue1Name,exchangeName,"error");
        channel.queueBind(queue2Name,exchangeName,"info");
        channel.queueBind(queue2Name,exchangeName,"error");
        channel.queueBind(queue2Name,exchangeName,"warning");
        String body =  "日志信息：张三调用了findAll方法...日志级别：info...";
        channel.basicPublish(exchangeName,"error",null,body.getBytes());
        channel.close();
        connection.close();
    }
}

```

消费者1

```java
public class Consumer_Routing1 {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */
        String queue1Name = "test_direct_queue1";
        String queue2Name = "test_direct_queue2";
        channel.queueDeclare(queue1Name,true,false,false,null);
    /**
     *queue:队列名称
     * autoAck 是否自动确认
     * callback 回调接口
     */

        Consumer consumer = new DefaultConsumer(channel){
            /*
                 回调方法
                 1.consumerTag:标识
                 2.envelope: 获取一些真实的消息
                 3.properties 配置消息
                 4.body数据
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
//                System.out.println(consumerTag);
//                System.out.println(envelope);
//                System.out.println(properties);
                System.out.println(new String(body));
                System.out.println("将消息打印到控制台");
            }
        };

 channel.basicConsume(queue1Name,true,consumer);
 //不要关闭
    }
}
```

消费者2

```java
public class Consumer_Routing2 {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */

        String queue1Name = "test_direct_queue1";
        String queue2Name = "test_direct_queue2";
        channel.queueDeclare(queue2Name,true,false,false,null);
    /**
     *queue:队列名称
     * autoAck 是否自动确认
     * callback 回调接口
     */

        Consumer consumer = new DefaultConsumer(channel){
            /*
                 回调方法
                 1.consumerTag:标识
                 2.envelope: 获取一些真实的消息
                 3.properties 配置消息
                 4.body数据
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
//                System.out.println(consumerTag);
//                System.out.println(envelope);
//                System.out.println(properties);
                System.out.println(new String(body));
                System.out.println("将数据包存到数据库");
            }
        };

 channel.basicConsume(queue2Name,true,consumer);
 //不要关闭
    }
}

```



通配符模式

生产者

```java
public class Producer_Topics {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        String host = "/";
        factory.setVirtualHost(host);
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
     /*

       exchangeDeclare(String exchange, BuiltinExchangeType type, boolean durable, boolean autoDelete, boolean internal, Map<String, Object> arguments)
       参数：
        1. exchange:交换机名称
        2. type:交换机类型
            DIRECT("direct"),：定向
            FANOUT("fanout"),：扇形（广播），发送消息到每一个与之绑定队列。
            TOPIC("topic"),通配符的方式
            HEADERS("headers");参数匹配

        3. durable:是否持久化
        4. autoDelete:自动删除
        5. internal：内部使用。 一般false
        6. arguments：参数
        */
        String exchangeName = "test_topics";
        channel.exchangeDeclare(exchangeName, BuiltinExchangeType.TOPIC,true,false,false,null);
        String queue1Name = "test_topic_queue1";
        String queue2Name = "test_topic_queue2";
        channel.queueDeclare(queue1Name,true,false,false,null);
        channel.queueDeclare(queue2Name,true,false,false,null);
        channel.queueBind(queue1Name,exchangeName,"#.error");
        channel.queueBind(queue2Name,exchangeName,"order.*");
        channel.queueBind(queue2Name,exchangeName,"*.*");
        String body =  "日志信息：张三调用了order方法...日志级别：error...";
        channel.basicPublish(exchangeName,"order.error",null,body.getBytes());
        channel.close();
        connection.close();
    }
}

```

消费者1

```java
public class Consumer_Topic1 {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */
        String queue1Name = "test_topic_queue1";
        String queue2Name = "test_topic_queue2";
        channel.queueDeclare(queue1Name,true,false,false,null);
    /**
     *queue:队列名称
     * autoAck 是否自动确认
     * callback 回调接口
     */

        Consumer consumer = new DefaultConsumer(channel){
            /*
                 回调方法
                 1.consumerTag:标识
                 2.envelope: 获取一些真实的消息
                 3.properties 配置消息
                 4.body数据
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
//                System.out.println(consumerTag);
//                System.out.println(envelope);
//                System.out.println(properties);
                System.out.println(new String(body));
                System.out.println("将消息打印到控制台");
            }
        };

 channel.basicConsume(queue1Name,true,consumer);
 //不要关闭
    }
}

```

消费者2

```java
public class Consumer_Topic2 {
    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
         *1.queue 队列名称
         * durable 是否持久化
         * exclusive 是否独占
         * autoDelete是否删除
         * argument 参数
         *
         */
        String queue1Name = "test_topic_queue1";
        String queue2Name = "test_topic_queue2";
        channel.queueDeclare(queue2Name,true,false,false,null);
    /**
     *queue:队列名称
     * autoAck 是否自动确认
     * callback 回调接口
     */

        Consumer consumer = new DefaultConsumer(channel){
            /*
                 回调方法
                 1.consumerTag:标识
                 2.envelope: 获取一些真实的消息
                 3.properties 配置消息
                 4.body数据
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
//                System.out.println(consumerTag);
//                System.out.println(envelope);
//                System.out.println(properties);
                System.out.println(new String(body));
                System.out.println("将消息打印到控制台");
            }
        };

 channel.basicConsume(queue2Name,true,consumer);
 //不要关闭
    }
}


```

spring整合RabbitMQ

生产者

依赖

```pom
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.pinkhat</groupId>
    <artifactId>spring-rabbitmq-producer</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.7.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.amqp</groupId>
            <artifactId>spring-rabbit</artifactId>
            <version>2.1.8.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.7.RELEASE</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

rabbitmq-properties

```properties
rabbitmq.host=127.0.0.1
rabbitmq.port=5672
rabbitmq.username=guest
rabbitmq.password=guest
rabbitmq.virtual-host=/
```

spring-rabbitmq-producer.xml

```xm
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>

    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义管理交换机、队列-->
    <rabbit:admin connection-factory="connectionFactory"/>

    <!--定义持久化队列，不存在则自动创建；不绑定到交换机则绑定到默认交换机
    默认交换机类型为direct，名字为：""，路由键为队列的名称
    -->
    <rabbit:queue id="spring_queue" name="spring_queue" auto-declare="true"/>

    <!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~广播；所有队列都能收到消息~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_fanout_queue_1" name="spring_fanout_queue_1" auto-declare="true"/>

    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_fanout_queue_2" name="spring_fanout_queue_2" auto-declare="true"/>

    <!--定义广播类型交换机；并绑定上述两个队列-->
    <rabbit:fanout-exchange id="spring_fanout_exchange" name="spring_fanout_exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="spring_fanout_queue_1"/>
            <rabbit:binding queue="spring_fanout_queue_2"/>
        </rabbit:bindings>
    </rabbit:fanout-exchange>

    <!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~通配符；*匹配一个单词，#匹配多个单词 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_topic_queue_star" name="spring_topic_queue_star" auto-declare="true"/>
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_topic_queue_well" name="spring_topic_queue_well" auto-declare="true"/>
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_topic_queue_well2" name="spring_topic_queue_well2" auto-declare="true"/>

    <rabbit:topic-exchange id="spring_topic_exchange" name="spring_topic_exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding pattern="heima.*" queue="spring_topic_queue_star"/>
            <rabbit:binding pattern="heima.#" queue="spring_topic_queue_well"/>
            <rabbit:binding pattern="itcast.#" queue="spring_topic_queue_well2"/>
        </rabbit:bindings>
    </rabbit:topic-exchange>

    <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
    <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>
</beans>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations ="classpath:spring-rabbitmq-producer.xml")
public class ProducerTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Test
    public void testHelloWorld(){
        rabbitTemplate.convertAndSend("spring_queue","hello world");
    }
    @Test
    public void testFanout(){
        rabbitTemplate.convertAndSend("spring_fanout_exchange","","hello fanout" );
    }
    @Test
    public void testTopics(){
        rabbitTemplate.convertAndSend("spring_topic_exchange","heima.hehe.haha","hello topic" );
    }
}

```



properties

```properties
rabbitmq.host=127.0.0.1
rabbitmq.port=5672
rabbitmq.username=guest
rabbitmq.password=guest
rabbitmq.virtual-host=/
```

xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>

    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>

    <bean id="springQueueListener" class="com.itheima.rabbitmq.listener.SpringQueueListener"/>
    <bean id="fanoutListener1" class="com.itheima.rabbitmq.listener.FanoutListener1"/>
    <bean id="fanoutListener2" class="com.itheima.rabbitmq.listener.FanoutListener2"/>
    <bean id="topicListenerStar" class="com.itheima.rabbitmq.listener.TopicListenerStar"/>
    <bean id="topicListenerWell" class="com.itheima.rabbitmq.listener.TopicListenerWell"/>
    <bean id="topicListenerWell2" class="com.itheima.rabbitmq.listener.TopicListenerWell2"/>

    <rabbit:listener-container connection-factory="connectionFactory" auto-declare="true">
        <rabbit:listener ref="springQueueListener" queue-names="spring_queue"/>
        <rabbit:listener ref="fanoutListener1" queue-names="spring_fanout_queue_1"/>
        <rabbit:listener ref="fanoutListener2" queue-names="spring_fanout_queue_2"/>
        <rabbit:listener ref="topicListenerStar" queue-names="spring_topic_queue_star"/>
        <rabbit:listener ref="topicListenerWell" queue-names="spring_topic_queue_well"/>
        <rabbit:listener ref="topicListenerWell2" queue-names="spring_topic_queue_well2"/>
    </rabbit:listener-container>
</beans>
```

取出来的java

```java
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-consumer.xml")
public class ConsumerTest {
    @Test
    public void test1(){
        while (true){

        }
    }
}
```

```java
public class SpringQueueListener implements MessageListener {
    @Override
    public void onMessage(Message message) {
        System.out.println(new String(message.getBody()));
    }
}
```



springboot整合rabbitmq

```pom
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
```

配置

```yml
spring:
 rabbitmq:
   host: 127.0.0.1
   port: 5672
   username: guest
   password: guest
   virtual-host: /

```



生产者配置类

```java
@Configuration
public class RabbitMQConfig {
    public static final String EXCHANGE_NAME="boot_topic_exchange";
    public static final String QUEUE_NAME="boot_queue";
@Bean("bootExchange")
    public Exchange bootExchange(){
    return ExchangeBuilder.topicExchange(EXCHANGE_NAME).durable(true).build();
    }
    @Bean("bootQueue")
    public Queue bootQueue(){
    return QueueBuilder.durable(QUEUE_NAME).build();
    }
    @Bean
    public Binding bindQueueExchange(@Qualifier("bootQueue") Queue queue,@Qualifier("bootExchange")Exchange exchange){
    return BindingBuilder.bind(queue).to(exchange).with("boot.#").noargs();
    }
}
```

启动类

```java
@SpringBootApplication
public class ProducerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProducerApplication.class);
    }
}

```

消费者监听类

```java
@Component
public class RabbitMQListener {
    @RabbitListener(queues = "boot_queue")
    public void ListenerQueue(Message message){
        System.out.println(new String(message.getBody()));
    }

}

```

启动类

```java
spring:
 rabbitmq:
   host: 127.0.0.1
   port: 5672
   username: guest
   password: guest
   virtual-host: /
```

```java
@SpringBootApplication
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class,args);
    }
}
```



高级特性

消息的可靠性投递（生产端）

​	confirm 确认模式

```text
 步骤：
 * 1. 确认模式开启：ConnectionFactory中开启publisher-confirms="true" ioc
 * 2. 在rabbitTemplate定义ConfirmCallBack回调函数
```

```java
        //2. 定义回调
        rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallback() {
            /**
             *
             * @param correlationData 相关配置信息
             * @param ack   exchange交换机 是否成功收到了消息。true 成功，false代表失败
             * @param cause 失败原因
             */
            @Override
            public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                System.out.println("confirm方法被执行了....");

                if (ack) {
                    //接收成功
                    System.out.println("接收成功消息" + cause);
                } else {
                    //接收失败
                    System.out.println("接收失败消息" + cause);
                    //做一些处理，让消息再次发送。
                }
            }
        });

        //3. 发送消息
        rabbitTemplate.convertAndSend("test_exchange_confirm111", "confirm", "message confirm....");
```



​		return  退回模式

```text
     * 1. 开启回退模式:publisher-returns="true"
     * 2. 设置ReturnCallBack
     * 3. 设置Exchange处理消息的模式：
     * 1. 如果消息没有路由到Queue，则丢弃消息（默认）
     * 2. 如果消息没有路由到Queue，返回给消息发送方ReturnCallBack
```



 ```java
   rabbitTemplate.setMandatory(true);
 
         //2.设置ReturnCallBack
         rabbitTemplate.setReturnCallback(new RabbitTemplate.ReturnCallback() {
             /**
              *
              * @param message   消息对象
              * @param replyCode 错误码
              * @param replyText 错误信息
              * @param exchange  交换机
              * @param routingKey 路由键
              */
             @Override
             public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
                 System.out.println("return 执行了....");
 
                 System.out.println(message);
                 System.out.println(replyCode);
                 System.out.println(replyText);
                 System.out.println(exchange);
                 System.out.println(routingKey);
 
                 //处理
             }
         });
 
 
         //3. 发送消息
         rabbitTemplate.convertAndSend("test_exchange_confirm", "confirm", "message confirm....");
 ```



Consumer ACK(消费端)

​	消费端收到消息的确认方式

​	自动确认 acknowledge = "none"

​	手动确认 acknowledge =  "manual"

​	根据异常情况确认 acknowledge = "auto"

```text

 * Consumer ACK机制：
 *  1. 设置手动签收。acknowledge="manual"
 *  2. 让监听器类实现ChannelAwareMessageListener接口
 *  3. 如果消息成功处理，则调用channel的 basicAck()签收
 *  4. 如果消息处理失败，则调用channel的basicNack()拒绝签收，broker重新发送给consumer

```

```java

@Component
public class AckListener implements ChannelAwareMessageListener {

    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        long deliveryTag = message.getMessageProperties().getDeliveryTag();

        try {
            //1.接收转换消息
            System.out.println(new String(message.getBody()));

            //2. 处理业务逻辑
            System.out.println("处理业务逻辑...");
            int i = 3/0;//出现错误
            //3. 手动签收
            channel.basicAck(deliveryTag,true);
        } catch (Exception e) {
            //e.printStackTrace();

            //4.拒绝签收
            /*
            第三个参数：requeue：重回队列。如果设置为true，则消息重新回到queue，broker会重新发送该消息给消费端
             */
            channel.basicNack(deliveryTag,true,true);
            //channel.basicReject(deliveryTag,true);
        }
    }
}
```



消费端限流

```text
 1. 确保ack机制为手动确认。
 2. listener-container配置属性perfetch = 1,表示消费端每次从mq拉去一条消息来消费，直到手动确认消费完毕后，才会继续拉去下一条消息。

```

```java

@Component
public class QosListener implements ChannelAwareMessageListener {

    @Override
    public void onMessage(Message message, Channel channel) throws Exception {

        Thread.sleep(1000);
        //1.获取消息
        System.out.println(new String(message.getBody()));

        //2. 处理业务逻辑

        //3. 签收
        channel.basicAck(message.getMessageProperties().getDeliveryTag(),true);

    }
}

```



TTL(存活时间) 购物网站下单



统一过期

```xml

    <!--ttl-->
    <rabbit:queue name="test_queue_ttl" id="test_queue_ttl">
        <!--设置queue的参数-->
        <rabbit:queue-arguments>
            <!--x-message-ttl指队列的过期时间-->
            <entry key="x-message-ttl" value="100000" value-type="java.lang.Integer"></entry>
        </rabbit:queue-arguments>

    </rabbit:queue>
        <rabbit:topic-exchange name="test_exchange_ttl" >
        <rabbit:bindings>
            <rabbit:binding pattern="ttl.#" queue="test_queue_ttl"></rabbit:binding>
        </rabbit:bindings>
    </rabbit:topic-exchange>
```

```java

      /*  for (int i = 0; i < 10; i++) {
            // 发送消息
            rabbitTemplate.convertAndSend("test_exchange_ttl", "ttl.hehe", "message ttl....");
        }*/ 
```



单独过期

```text
  * 如果设置了消息的过期时间，也设置了队列的过期时间，它以时间短的为准。
     * 队列过期后，会将队列所有消息全部移除。
     * 消息过期后，只有消息在队列顶端，才会判断其是否过期(移除掉)
     *
```



```java
// 消息后处理对象，设置一些消息的参数信息
        MessagePostProcessor messagePostProcessor = new MessagePostProcessor() {

            @Override
            public Message postProcessMessage(Message message) throws AmqpException {
                //1.设置message的信息
                message.getMessageProperties().setExpiration("5000");//消息的过期时间
                //2.返回该消息
                return message;
            }
        };


        //消息单独过期
        //rabbitTemplate.convertAndSend("test_exchange_ttl", "ttl.hehe", "message ttl....",messagePostProcessor);


        for (int i = 0; i < 10; i++) {
            if(i == 5){
                //消息单独过期
                rabbitTemplate.convertAndSend("test_exchange_ttl", "ttl.hehe", "message ttl....",messagePostProcessor);
            }else{
                //不过期的消息
                rabbitTemplate.convertAndSend("test_exchange_ttl", "ttl.hehe", "message ttl....");

            }

        }

```

死信队列(死信交换机)

消息成为死信的条件(*****)

```text
队列消息长度达到限制
消费者拒接消息
原队列存在消息过期的设置，消息达到超时时间未被消费
```

对列绑定死信交换机

```text
x-dead-letter-exchange x-dead-letter-routing-key
```

```xml

    <!--
        死信队列：
            1. 声明正常的队列(test_queue_dlx)和交换机(test_exchange_dlx)
            2. 声明死信队列(queue_dlx)和死信交换机(exchange_dlx)
            3. 正常队列绑定死信交换机
                设置两个参数：
                    * x-dead-letter-exchange：死信交换机名称
                    * x-dead-letter-routing-key：发送给死信交换机的routingkey
    -->

    <!--
        1. 声明正常的队列(test_queue_dlx)和交换机(test_exchange_dlx)
    -->

    <rabbit:queue name="test_queue_dlx" id="test_queue_dlx">
        <!--3. 正常队列绑定死信交换机-->
        <rabbit:queue-arguments>
            <!--3.1 x-dead-letter-exchange：死信交换机名称-->
            <entry key="x-dead-letter-exchange" value="exchange_dlx" />

            <!--3.2 x-dead-letter-routing-key：发送给死信交换机的routingkey-->
            <entry key="x-dead-letter-routing-key" value="dlx.hehe" />

            <!--4.1 设置队列的过期时间 ttl-->
            <entry key="x-message-ttl" value="10000" value-type="java.lang.Integer" />
            <!--4.2 设置队列的长度限制 max-length -->
            <entry key="x-max-length" value="10" value-type="java.lang.Integer" />
        </rabbit:queue-arguments>
    </rabbit:queue>
    <rabbit:topic-exchange name="test_exchange_dlx">
        <rabbit:bindings>
            <rabbit:binding pattern="test.dlx.#" queue="test_queue_dlx"></rabbit:binding>
        </rabbit:bindings>
    </rabbit:topic-exchange>


    <!--
       2. 声明死信队列(queue_dlx)和死信交换机(exchange_dlx)
   -->

    <rabbit:queue name="queue_dlx" id="queue_dlx"></rabbit:queue>
    <rabbit:topic-exchange name="exchange_dlx">
        <rabbit:bindings>
            <rabbit:binding pattern="dlx.#" queue="queue_dlx"></rabbit:binding>
        </rabbit:bindings>
    </rabbit:topic-exchange>



  

```

```java
@Test
    public void testDlx(){
        //1. 测试过期时间，死信消息
        //rabbitTemplate.convertAndSend("test_exchange_dlx","test.dlx.haha","我是一条消息，我会死吗？");

        //2. 测试长度限制后，消息死信
       /* for (int i = 0; i < 20; i++) {
            rabbitTemplate.convertAndSend("test_exchange_dlx","test.dlx.haha","我是一条消息，我会死吗？");
        }*/

        //3. 测试消息拒收
        rabbitTemplate.convertAndSend("test_exchange_dlx","test.dlx.haha","我是一条消息，我会死吗？");

    }



    @Test
    public  void testDelay() throws InterruptedException {
        //1.发送订单消息。 将来是在订单系统中，下单成功后，发送消息
        rabbitTemplate.convertAndSend("order_exchange","order.msg","订单信息：id=1,time=2019年8月17日16:41:47");


        /*//2.打印倒计时10秒
        for (int i = 10; i > 0 ; i--) {
            System.out.println(i+"...");
            Thread.sleep(1000);
        }*/


    }
```

```java

@Component
public class DlxListener implements ChannelAwareMessageListener {

    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        long deliveryTag = message.getMessageProperties().getDeliveryTag();

        try {
            //1.接收转换消息
            System.out.println(new String(message.getBody()));

            //2. 处理业务逻辑
            System.out.println("处理业务逻辑...");
            int i = 3/0;//出现错误
            //3. 手动签收
            channel.basicAck(deliveryTag,true);
        } catch (Exception e) {
            //e.printStackTrace();
            System.out.println("出现异常，拒绝接受");
            //4.拒绝签收，不重回队列 requeue=false
            channel.basicNack(deliveryTag,true,false);
        }
    }
}

```



```tex
1、死信交换机和死信队列和普通队列没有什么区别
2、当消息成为死信后，如果该队列绑定了死信交换机，则消息会被死信交换机重新路由到思想队列
3.成为死信的三种情况 1.消息队列的长度达到限制 2.消费者拒绝消费消息，并且不重回队列。3.原队列存在消息过期设置，消息达到超时时间未被消费

```



延迟队列

```text
即消息进入队列后会被消费，只有达到指定时间后才会被消费
时间方式:1.定时器(开销大) 2.延时队列 TTL+死信队列（DLX）
```

```xml
  <!--
        延迟队列：
            1. 定义正常交换机（order_exchange）和队列(order_queue)
            2. 定义死信交换机（order_exchange_dlx）和队列(order_queue_dlx)
            3. 绑定，设置正常队列过期时间为30分钟
    -->
    <!-- 1. 定义正常交换机（order_exchange）和队列(order_queue)-->
    <rabbit:queue id="order_queue" name="order_queue">
        <!-- 3. 绑定，设置正常队列过期时间为30分钟-->
        <rabbit:queue-arguments>
            <entry key="x-dead-letter-exchange" value="order_exchange_dlx" />
            <entry key="x-dead-letter-routing-key" value="dlx.order.cancel" />
            <entry key="x-message-ttl" value="10000" value-type="java.lang.Integer" />

        </rabbit:queue-arguments>

    </rabbit:queue>
    <rabbit:topic-exchange name="order_exchange">
        <rabbit:bindings>
            <rabbit:binding pattern="order.#" queue="order_queue"></rabbit:binding>
        </rabbit:bindings>
    </rabbit:topic-exchange>

    <!--  2. 定义死信交换机（order_exchange_dlx）和队列(order_queue_dlx)-->
    <rabbit:queue id="order_queue_dlx" name="order_queue_dlx"></rabbit:queue>
    <rabbit:topic-exchange name="order_exchange_dlx">
        <rabbit:bindings>
            <rabbit:binding pattern="dlx.order.#" queue="order_queue_dlx"></rabbit:binding>
        </rabbit:bindings>
    </rabbit:topic-exchange>
```

```java
@Test
    public  void testDelay() throws InterruptedException {
        //1.发送订单消息。 将来是在订单系统中，下单成功后，发送消息
        rabbitTemplate.convertAndSend("order_exchange","order.msg","订单信息：id=1,time=2019年8月17日16:41:47");


        /*//2.打印倒计时10秒
        for (int i = 10; i > 0 ; i--) {
            System.out.println(i+"...");
            Thread.sleep(1000);
        }*/


    }

```

```java
@Component
public class OrderListener implements ChannelAwareMessageListener {






    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        long deliveryTag = message.getMessageProperties().getDeliveryTag();

        try {
            //1.接收转换消息
            System.out.println(new String(message.getBody()));

            //2. 处理业务逻辑
            System.out.println("处理业务逻辑...");
            System.out.println(" ...");
            System.out.println("判断状态是否为支付成功");
            System.out.println("取消订单，回滚库存....");
            //3. 手动签收
            channel.basicAck(deliveryTag,true);
        } catch (Exception e) {
            //e.printStackTrace();
            System.out.println("出现异常，拒绝接受");
            //4.拒绝签收，不重回队列 requeue=false
            channel.basicNack(deliveryTag,true,false);
        }
    }
}

```





日志与监控

```text
var\log\rabbitmq\rabbitmq@xxx.log
```



消息追踪

``` linux
开启链路追踪的插件：rabbitmqctl trance_on
关闭链路追踪的插件：rabbitmqctl trance_off
启用插件:rabbitmq_plugins enable rabbitmq_tracing
#所有的意思
```

消息的可靠性分析

​	消息补偿机制 

​	<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220223210448951.png" alt="image-20220223210448951" style="zoom:1000%;" />

消息幂等性保障

​	幂等性消费多次相同的消息与消费一次消息应该得到相同的结果

​	 version id 如果查询数据库中有相同的则不执行（乐观锁）  

集群搭建（文档） 用端口区分

​	

RabbitMQ高可用集群
