springSecurity

依赖

```xml
	<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-web</artifactId>
			<version>4.1.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-config</artifactId>
			<version>4.1.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.5</version>
			<scope>provided</scope>
		</dependency>
```

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
	version="2.5">		
  	 <context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring-security.xml</param-value>
	 </context-param>
	 <listener>
		<listener-class>
			org.springframework.web.context.ContextLoaderListener
		</listener-class>
	 </listener>	
	 <filter>  
		<filter-name>springSecurityFilterChain</filter-name>  		 <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>  
	 </filter>  
	 <filter-mapping>  
		<filter-name>springSecurityFilterChain</filter-name>  
		<url-pattern>/*</url-pattern>  
	 </filter-mapping>	
</web-app>
```





spring-security

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/security"
	xmlns:beans="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
						http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security.xsd">
	<!-- 以下页面不被拦截 -->
	<http pattern="/login.html" security="none"></http>
	<http pattern="/login_error.html" security="none"></http>
	<!-- 页面拦截规则 -->
	<http use-expressions="false">
		<intercept-url pattern="/*" access="ROLE_USER" />
		<form-login login-page="/login.html" default-target-url="/index.html" authentication-failure-url="/login_error.html"/>	
		<csrf disabled="true"/>
	</http>
	<!-- 页面拦截规则 -->
	<http use-expressions="false">
		<intercept-url pattern="/**" access="ROLE_USER" />
		<form-login/>	
        <intercept-url pattern="/**" access="hasRole('ROLE_USER')" />
	</http>
    	<!-- 登出页面 -->
<logout logout-url="" logout-success-url=""/>
	<!-- 认证管理器 -->
	<authentication-manager>
		<authentication-provider>
			<user-service>
				<user name="admin" password="123456" authorities="ROLE_USER"/>
			</user-service>		
		</authentication-provider>	
	</authentication-manager>
</beans:beans>
```

```java
/**
 * 认证类
 * @author Administrator
 *
 */
public class UserDetailsServiceImpl implements UserDetailsService {
	private SellerService sellerService;
	public void setSellerService(SellerService sellerService) {
		this.sellerService = sellerService;
	}
	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		System.out.println("经过了UserDetailsServiceImpl");
		//构建角色列表
		List<GrantedAuthority> grantAuths=new ArrayList();
		grantAuths.add(new SimpleGrantedAuthority("ROLE_SELLER"));
		//得到商家对象
		TbSeller seller = sellerService.findOne(username);
		if(seller!=null){
			if(seller.getStatus().equals("1")){
				return new User(username,seller.getPassword(),grantAuths);
			}else{
				return null;
			}			
		}else{
			return null;
		}
	}
}
```



认证类

```xml
	<beans:bean id="userDetailService" class="com.pinyougou.service.UserDetailsServiceImpl">
```



加密配置

```xml
<beans:bean id="bcryptEncoder"  
        class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder" />
```

