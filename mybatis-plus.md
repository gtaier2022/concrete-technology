依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.1.1</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId> 	           <version>5.1.47</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId> 					           <artifactId>druid</artifactId>
    <version>1.0.11</version>
</dependency>
```

注解:

```java
@TableName("")//表名称
@TableField("")//属性名与字段不一致的问题

```

log4j.properties

```pro
log4j.rootLogger=DEBUG,A1 log4j.appender.A1=org.apache.log4j.ConsoleAppender log4j.appender.A1.layout=org.apache.log4j.PatternLayout log4j.appender.A1.layout.ConversionPattern=[%t] [%c]-[%p] %m%n
```

application.properties

```properties
spring.application.name = itcast-mp-springboot
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/mp? useUnicode=true&characterEncoding=utf8&autoReconnect=true&allowMultiQueries=true&useSSL =false 
spring.datasource.username=root 
spring.datasource.password=root
```



分页插件

```java
@Configuration @MapperScan("cn.itcast.mp.mapper") //设置mapper接口的扫描包 
public class MybatisPlusConfig { /*** 分页插件 */ @Bean public PaginationInterceptor paginationInterceptor() {
    return new PaginationInterceptor();
  }
}
```

mp的基本配置

```properties
mybatis-plus.config-location = classpath:mybatis-config.xml
mybatis-plus.mapper-locations = classpath*:mybatis/*.xml
mybatis-plus.type-aliases-package = cn.itcast.mp.pojo
mybatis-plus.global-config.db-config.id-type=auto
mybatis-plus.configuration.cache-enabled=false

#关闭自动驼峰映射，该参数不能和mybatis-plus.config-location同时存在 
mybatis-plus.configuration.map-underscore-to-camel-case=false

mybatis-plus.global-config.db-config.table-prefix=tb_
```

执行分析插件

```java
@Bean
public SqlExplainInterceptor sqlExplainInterceptor(){
SqlExplainInterceptor sqlExplainInterceptor = new SqlExplainInterceptor();
List<ISqlParser> sqlParserList = new ArrayList<>();
// 攻击 SQL 阻断解析器、加入解析链
sqlParserList.add(new BlockAttackSqlParser());
sqlExplainInterceptor.setSqlParserList(sqlParserList);
return sqlExplainInterceptor;
}

```

性能分析插件

```java
PerformanceInterceptor
    maxTime 100
    format true
```

乐观锁插件

```java
@Bean
public OptimisticLockerInterceptor optimisticLockerInterceptor() {
return new OptimisticLockerInterceptor();
}


@Version
private Integer version;

@TableField(fill = FieldFill.INSERT) //插入数据时进行填充
private String password;

```

```properties
# 枚举包扫描
mybatis-plus.type-enums-package=cn.itcast.mp.enums
```

代码生成器

```xml
<dependency>
<groupId>com.baomidou</groupId>
<artifactId>mybatis-plus-boot-starter</artifactId>
<version>3.1.1</version>
</dependency>
<dependency>
<groupId>com.baomidou</groupId>
<artifactId>mybatis-plus-generator</artifactId>
<version>3.1.1</version>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
<!--mysql驱动-->
<dependency>
<groupId>mysql</groupId>
<artifactId>mysql-connector-java</artifactId>
<version>5.1.47</version>
</dependency>
<dependency>
<groupId>org.slf4j</groupId>
<artifactId>slf4j-log4j12</artifactId>
</dependency>

```

MySQL代码生成器

```java
public class MysqlGenerator {
/**
* <p>
* 读取控制台内容
* </p>
*/
public static String scanner(String tip) {
Scanner scanner = new Scanner(System.in);
StringBuilder help = new StringBuilder();
help.append("请输入" + tip + "：");
System.out.println(help.toString());
if (scanner.hasNext()) {
String ipt = scanner.next();
    
    if (StringUtils.isNotEmpty(ipt)) {
return ipt;
}
}
throw new MybatisPlusException("请输入正确的" + tip + "！");
}
/**
* RUN THIS
*/
public static void main(String[] args) {
// 代码生成器
AutoGenerator mpg = new AutoGenerator();
// 全局配置
GlobalConfig gc = new GlobalConfig();
String projectPath = System.getProperty("user.dir");
gc.setOutputDir(projectPath + "/src/main/java");
gc.setAuthor("itcast");
gc.setOpen(false);
mpg.setGlobalConfig(gc);
// 数据源配置
DataSourceConfig dsc = new DataSourceConfig();
dsc.setUrl("jdbc:mysql://127.0.0.1:3306/mp?
useUnicode=true&useSSL=false&characterEncoding=utf8");
// dsc.setSchemaName("public");
dsc.setDriverName("com.mysql.jdbc.Driver");
dsc.setUsername("root");
dsc.setPassword("root");
mpg.setDataSource(dsc);
// 包配置
PackageConfig pc = new PackageConfig();
pc.setModuleName(scanner("模块名"));
pc.setParent("cn.itcast.mp.generator");
mpg.setPackageInfo(pc);
// 自定义配置
InjectionConfig cfg = new InjectionConfig() {
@Override
public void initMap() {
// to do nothing
}
};
List<FileOutConfig> focList = new ArrayList<>();
focList.add(new FileOutConfig("/templates/mapper.xml.ftl") {
@Override
public String outputFile(TableInfo tableInfo) {
// 自定义输入文件名称
return projectPath + "/itcast-mpgenerator/src/main/resources/mapper/" + pc.getModuleName()
    + "/" + tableInfo.getEntityName() + "Mapper" +
StringPool.DOT_XML;
}
});
cfg.setFileOutConfigList(focList);
mpg.setCfg(cfg);
mpg.setTemplate(new TemplateConfig().setXml(null));
// 策略配置
StrategyConfig strategy = new StrategyConfig();
strategy.setNaming(NamingStrategy.underline_to_camel);
strategy.setColumnNaming(NamingStrategy.underline_to_camel);
//
strategy.setSuperEntityClass("com.baomidou.mybatisplus.samples.generator.common.BaseE
ntity");
strategy.setEntityLombokModel(true);
//
strategy.setSuperControllerClass("com.baomidou.mybatisplus.samples.generator.common.B
aseController");
strategy.setInclude(scanner("表名"));
strategy.setSuperEntityColumns("id");
strategy.setControllerMappingHyphenStyle(true);
strategy.setTablePrefix(pc.getModuleName() + "_");
mpg.setStrategy(strategy);
// 选择 freemarker 引擎需要指定如下加，注意 pom 依赖必须有！
mpg.setTemplateEngine(new FreemarkerTemplateEngine());
mpg.execute();
}
}
```



