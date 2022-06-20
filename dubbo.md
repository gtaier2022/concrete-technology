1.dubbo依赖配置

```xml
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>0.2.0</version>
</dependency>
```



2.dubbo生产者配置

```properties
dubbo.application.name= 
dubbo.registry.protocol=zookeeper
dubbo.registry.address=
dubbo.scan.base-package=
dubbo.protocol.name=dubbo

#application.name就是服务名，不能跟别的dubbo提供端重复
#registry.protocol 是指定注册中心协议
#registry.address 是注册中心的地址加端口号
#protocol.name 是分布式固定是dubbo,不要改。
#base-package  注解方式要扫描的包
```

3.dubbo消费者配置

```properties
dubbo.application.name=
dubbo.registry.protocol=zookeeper
dubbo.registry.address=192.168.67.159:2181
dubbo.scan.base-package=
dubbo.protocol.name=dubbo
dubbo.consumer.check=false
dubbo.consumer.timeout=1000
dubbo.provider.timeout=1000
dubbo.registry.timeout=1000
dubbo.consumer.retries=2
dubbo.provider.retries=2

#application.name就是服务名，不能跟别的dubbo提供端重复
#registry.protocol 是指定注册中心协议
#registry.address 是注册中心的地址加端口号
#protocol.name 是分布式固定是dubbo,不要改。
#base-package  注解方式要扫描的包
#consumer.check 启动不检查
#timeout 超时配置
#retries 重试次数

```



4.dubbo注解

```java
@Service[暴露服务]

@Reference[远程调用服务]
UserService userService
@EnableDubbo [没有指指定包扫描]
@Configuration
@EnableDubbo(scanBasePackages = "") 
@PropertySource("") //指定配置文件
@Reference(timeout = 3000,retries = 2,version = "1.0.0")//version="*"随机调用
 @Service(timeout = 3000,retries = 2,version = "1.0.0")
   容错 

```

```java
@SpringBootApplication
@EnableDubbo
@EnableHystrix
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class);
    }
}

```

```java
@Service(timeout = 3000,retries = 2,version = "1.0.0",stub = "")//stub=配置本地存根参数是本地存根类的全类名
public class Uservservice {
    @Reference(timeout = 3000,retries = 2,version = "1.0.0",stub = "",url = "",loadbalance = "random",
    mock = "")
    //dubbo直连:提供者的url地址直连的方式是绕过注册中心的
      //loadbalance负载均衡配置   [random]
            //mock服务降级逻辑类的全路径名
    Uservservice uservservice;

    @HystrixCommand(fallbackMethod = "fallback")

    public String method(){
        return "zc";
    }


    public String fallback(){
        return "error";
    }
}
```

