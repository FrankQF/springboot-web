# SpringBoot Web #

## Spring Boot的Web开发支持 ##

使用Thymeleaf模板引擎

1. 引入  
	页面引入Thymeleaf：html页面中：
	
		<html xmlns:th="http://www.thymeleaf.org">
	
	- 通过 xmlns:th="http://www.thymeleaf.org"命名空间，将镜头页面转为动态的视图，需要进行同待处理的元素使用th：为前缀。
	- 通过@{}引用web静态资源

2. 访问modle中的数据  
	通过${}访问model中的属性，和jsp类似,如 

		<span th:text="${person.name}"></span>

3. model中的数据迭代  

		<li class="list-group-item" th:each="person:${people}">

4. 引入  
	支持>< >= <= 等作为比较条件，同时也支持SpringEL表达式语言

		  <div th:if="${not #lists.isEmpty(people)}">

5. 在js中访问model

		var single = [[${singlePerson}]];

	

## 与Spring MVC集成 ##


若需要继承一个模板引擎的话，需要定义ViewResolver，而且ViewResolver需要定义View，不过SpringBoot已经进行自动配置，无特殊情况不需更改。
默认配置为：前缀为：classpath：、templates/ 后缀默认为html，模板默认为HTML 5 模板编码默认为UTF-8 模板的媒体类型默认为 text/html  默认开启模板缓存。

关闭缓存配置：application.yml中：

	spring:
	  thymeleaf:
	    cache: false

## web相关配置 ##

1. 自动配置ViewResolver
	1. ContentNegotiating ViewResolver，SpringMVC提供的特殊ViewResolver，它是代理给不同的ViewResolver来处理不同的View，所以有最高优先级
	2. VewnNameViewResolver  
	在控制器中的一个方法的返回值的字符串（视图名）会根据BeanNameViewResolver去查找Bean的名称为返回字符串的View来渲染视图。
	3. InternalResourceViewResolver  
	设置前缀后缀，及控制器中方法来返回视图名的字符串，以得到实际页面。

2. 自动配置的静态资源  
	1. 类路径文件： 把类路径下的/static /public /resources 和 /META-INF/resources文件夹下的静态文件直接映射为/**,可以通过http：//localhost:8080/**来访问
	2. webjar  
	把webjar的/META-INF/resources/webjars/下的静态文件映射为/webjar/**，可以通过http://localhost:8080/webjar/**来访问


### 接管Spring Boot的Web配置 ###

若SpringBoot提供的Spring MVC不符合要求，可以通过一个配置类加上@EnableWebMvc注解来实现完全自己控制的MVC配置。
需要添加自己额外配置的时候，可以定义一个配置类并集成WebMvcConfigurerAdapter，无需使用@EnableWebMvc注解 ，然后添加其他配置


### 注册Servlet Filter、Listener ###

当使用嵌入式的Servlet容器（Tomcat Jetty等），我们通过Servlet，Filter和Listerer声明为Spring Bean而达到注册的效果，或者注册ServletRegistrationBean、FilterRegistrationBean和ServletListenerRegistrationBean的Bean。


# Tomcat配置（Servlet容器的配置） # 
Tomcat的所有属性都在org.springframework.boot.autoconfigure.web.ServerProrperties配置类中做了定义，我们需要在application.yml配置属性中做配置即可。通用的Servlet容器配置都以server作为前缀，二Tomcat特有配置都已server.tomcat作为前缀  
eg：配置servlet容器

	server：
		port： 8081
		session-timeout :

代码配置Tomcat，注册一个实现EmbeddedServletContainter接口的Bean，若想直接配置Tomcat、Jetty、Underrow，则可直接定义TomocatEmbeddedServletContainerFactory等。


## SSL配置 ##

1. 生成证书
2. 将证书放到项目文件夹的根目录下
3. 添加配置文件  
		
		server:
		  port: 8443
		  ssl:
		    key-store: .keystore
		    key-store-password: 111111
		    trust-store-type: JKS
		    key-alias: tomcat
4. http转为https 添加配置。

## WebSocket ##
分为广播式和点对点式