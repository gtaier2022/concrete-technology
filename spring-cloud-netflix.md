<h3>组件综述

<h4>eureka：注册中心

<h4>ribbon:客户端负载均衡。

<h4>feign：声明式服务调用

<h4>hystrix:客户端容错包户。

<h5>zuul:api网关。





eureka[提供服务注册与发现] 

服务端

pom.xml

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

yml

```yml
server:
 port: 8761
eureka:
 instance:
   hostname: localhost
 client:
   registerWithEureka: false  
   fetchRegistry: false
   serviceUrl:
     defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

启动类

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
 public static void main(String[] args) {
 SpringApplication.run(EurekaServerApplication.class, args);
 }
}
```

客户端[有多个服务就有多少个客户端]

依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-commons</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

配置文件[将该服务注册到注册中心]

```yml
eureka:
 client:
   serviceUrl: # eureka server的路径
     defaultZone: http://localhost:8761/eureka/
 instance:
   prefer-ip-address: true #使用ip注册
```

启动类[不用加特殊的注解]

```java
@SpringBootApplication
//@EnableDiscoveryClient
//@EnableEurekaClient
public class UserApplication {
 public static void main(String[] args) {
 SpringApplication.run(UserApplication.class, args);
 }
}
```

**从Spring Cloud Edgware版本开始， @EnableDiscoveryClient 或 @EnableEurekaClient 可省略**。

**每一个微服务都应该有服务发现，负载均衡，容错   [@SpringCloud]**

eureka的自我保护机制[一般就默认配置就好]

Eureka中的元数据:[主机名、IP地址、端口号、状态页和健康检查]

eureka服务的高可用集群部署

