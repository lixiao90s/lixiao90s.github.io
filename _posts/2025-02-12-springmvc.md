---
title: SpringMVC
date: 2025-01-01 10:44:29 +0800
categories:
- SpringMVC
tags:
- spring
- vue
- framework
- javascript
- java
- mvc
- maven
author: lixiao
layout: post
toc: true
---

* TOC
{:toc}

# SpringMVC

四、SpringMVC实战：构建高效表述层框架

一、SpringMVC简介和体验

1.1 介绍

​![](https://docs.spring.io/spring-framework/reference/_/img/favicon.ico)[Spring Web MVC :: Spring Frameworkdocs.spring.io](https://docs.spring.io/spring-framework/reference/web/webmvc.html)

Spring Web MVC是基于Servlet API构建的原始Web框架，从一开始就包含在Spring Framework中。正式名称“Spring Web MVC”来自其源模块的名称（ spring-webmvc ），但它通常被称为“Spring MVC”。

在控制层框架历经Strust、WebWork、Strust2等诸多产品的历代更迭之后，目前业界普遍选择了SpringMVC作为Java EE项目表述层开发的首选方案。之所以能做到这一点，是因为SpringMVC具备如下显著优势：

Spring 家族原生产品，与IOC容器等基础设施无缝对接

表述层各细分领域需要解决的问题全方位覆盖，提供全面解决方案

代码清新简洁，大幅度提升开发效率

内部组件化程度高，可插拔式组件即插即用，想要什么功能配置相应组件即可

性能卓著，尤其适合现代大型、超大型互联网项目要求

原生Servlet API开发代码片段

**Java**

**复制**

protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {  String userName = request.getParameter("userName");System.out.println("userName="+userName);}

基于SpringMVC开发代码片段

**Java**

**复制**

@RequestMapping("/user/login")public String login(@RequestParam("userName") String userName,Sting password){log.debug("userName="+userName);//调用业务即可return "result";}

1.2 主要作用

​![](https://secure2.wostatic.cn/static/bnm9zUQo34z7FgXA9vAmfm/image.png?auth_key=1741057474-8XzJ1QnPj3HauBsRnWsngX-0-ed754c6a49b3e16f6ff81f05024921be&image_process=resize,w_1224&file_size=85984)​

SSM框架构建起单体项目的技术栈需求！其中的SpringMVC负责表述层（控制层）实现简化！

SpringMVC的作用主要覆盖的是表述层，例如：

请求映射

数据输入

视图界面

请求分发

表单回显

会话控制

过滤拦截

异步交互

文件上传

文件下载

数据校验

类型转换

等等等

最终总结：

**1**

简化前端参数接收( 形参列表 )

**2**

简化后端数据响应(返回值)

**3**

以及其他......

1.3 核心组件和调用流程理解

Spring MVC与许多其他Web框架一样，是围绕前端控制器模式设计的，其中中央 Servlet  DispatcherServlet 做整体请求处理调度！

除了DispatcherServletSpringMVC还会提供其他特殊的组件协作完成请求处理和响应呈现。

SpringMVC处理请求流程：

​![](https://secure2.wostatic.cn/static/no1PDXU3JX5K4cecSAx5oL/image.png?auth_key=1741057473-pueHuHMtBmioEkduM9M7vC-0-ccecc3e013368ac4890ba5f0d9bc0d19&image_process=resize,w_1440&file_size=110526)​

SpringMVC涉及组件理解：

**1**

DispatcherServlet :  SpringMVC提供，我们需要使用web.xml配置使其生效，它是整个流程处理的核心，所有请求都经过它的处理和分发！[ CEO ]

**2**

HandlerMapping :  SpringMVC提供，我们需要进行IoC配置使其加入IoC容器方可生效，它内部缓存handler(controller方法)和handler访问路径数据，被DispatcherServlet调用，用于查找路径对应的handler！[秘书]

**3**

HandlerAdapter : SpringMVC提供，我们需要进行IoC配置使其加入IoC容器方可生效，它可以处理请求参数和处理响应数据数据，每次DispatcherServlet都是通过handlerAdapter间接调用handler，他是handler和DispatcherServlet之间的适配器！[经理]

**4**

Handler : handler又称处理器，他是Controller类内部的方法简称，是由我们自己定义，用来接收参数，向后调用业务，最终返回响应结果！[打工人]

**5**

ViewResovler : SpringMVC提供，我们需要进行IoC配置使其加入IoC容器方可生效！视图解析器主要作用简化模版视图页面查找的，但是需要注意，前后端分离项目，后端只返回JSON数据，不返回页面，那就不需要视图解析器！所以，视图解析器，相对其他的组件不是必须的！[财务]

1.4 快速体验

**1**

体验场景需求

​![](https://secure2.wostatic.cn/static/qgxZRg9rZ6DFA7t8guNqYn/image.png?auth_key=1741057472-ev1Z76eeE3XNXUrA2MnkLE-0-4ac3981866d196e467bb518350312c2d&image_process=resize,w_1080&file_size=27939)​

**2**

配置分析

**a**

DispatcherServlet，设置处理所有请求！

**b**

HandlerMapping,HandlerAdapter,Handler需要加入到IoC容器，供DS调用！

**c**

Handler自己声明（Controller）需要配置到HandlerMapping中供DS查找！

**3**

准备项目

**a**

创建项目

springmvc-base-quick

注意：需要转成maven/web程序！！

**b**

导入依赖

**XML**

**复制**

<properties><spring.version>6.0.6</spring.version><servlet.api>9.1.0</servlet.api><maven.compiler.source>17</maven.compiler.source><maven.compiler.target>17</maven.compiler.target><project.build.sourceEncoding>UTF-8</project.build.sourceEncoding></properties><dependencies><!-- springioc相关依赖  --><dependency><groupId>org.springframework</groupId><artifactId>spring-context</artifactId><version>${spring.version}</version></dependency><!-- web相关依赖  --><!-- 在 pom.xml 中引入 Jakarta EE Web API 的依赖 --><!-- 在 Spring Web MVC 6 中，Servlet API 迁移到了 Jakarta EE API，因此在配置 DispatcherServlet 时需要使用 Jakarta EE 提供的相应类库和命名空间。错误信息 “‘org.springframework.web.servlet.DispatcherServlet’ is not assignable to ‘javax.servlet.Servlet,jakarta.servlet.Servlet’” 表明你使用了旧版本的 Servlet API，没有更新到 Jakarta EE 规范。 --><dependency><groupId>jakarta.platform</groupId><artifactId>jakarta.jakartaee-web-api</artifactId><version>${servlet.api}</version><scope>provided</scope></dependency><!-- springwebmvc相关依赖  --><dependency><groupId>org.springframework</groupId><artifactId>spring-webmvc</artifactId><version>${spring.version}</version></dependency></dependencies>

**4**

Controller声明

**Java**

**复制**

@Controllerpublic class HelloController {//handlers/** * handler就是controller内部的具体方法 * @RequestMapping("/springmvc/hello") 就是用来向handlerMapping中注册的方法注解! * @ResponseBody 代表向浏览器直接返回数据! */@RequestMapping("/springmvc/hello")@ResponseBodypublic String hello(){System.out.println("HelloController.hello");return "hello springmvc!!";}}

**5**

Spring MVC核心组件配置类

声明springmvc涉及组件信息的配置类

**Java**

**复制**

//TODO: SpringMVC对应组件的配置类 [声明SpringMVC需要的组件信息]//TODO: 导入handlerMapping和handlerAdapter的三种方式//1.自动导入handlerMapping和handlerAdapter [推荐]//2.可以不添加,springmvc会检查是否配置handlerMapping和handlerAdapter,没有配置默认加载//3.使用@Bean方式配置handlerMapper和handlerAdapter@EnableWebMvc     @Configuration@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现public class SpringMvcConfig implements WebMvcConfigurer {@Beanpublic HandlerMapping handlerMapping(){return new RequestMappingHandlerMapping();}@Beanpublic HandlerAdapter handlerAdapter(){return new RequestMappingHandlerAdapter();}}

**6**

SpringMVC环境搭建

对于使用基于 Java 的 Spring 配置的应用程序，建议这样做，如以下示例所示：

**Java**

**复制**

//TODO: SpringMVC提供的接口,是替代web.xml的方案,更方便实现完全注解方式ssm处理!//TODO: Springmvc框架会自动检查当前类的实现类,会自动加载 getRootConfigClasses / getServletConfigClasses 提供的配置类//TODO: getServletMappings 返回的地址 设置DispatherServlet对应处理的地址public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {/** * 指定service / mapper层的配置类  */@Overrideprotected Class&lt;?&gt;[] getRootConfigClasses() {return null;}/** * 指定springmvc的配置类 * @return */@Overrideprotected Class&lt;?&gt;[] getServletConfigClasses() {return new Class&lt;?&gt;[] { SpringMvcConfig.class };}/* * * 设置dispatcherServlet的处理路径! * 一般情况下为 / 代表处理所有请求! */@Overrideprotected String[] getServletMappings() {return new String[] { "/" };}}

**7**

启动测试

注意： tomcat应该是10+版本！方可支持 Jakarta EE API!

​![](https://secure2.wostatic.cn/static/iQSLu8VreeyFX913Ab3QQc/image.png?auth_key=1741057472-2CQf7AKPyRBt6DASVxySS3-0-ffb342606fa7691c0248495ffe6fd925&image_process=resize,w_1080&file_size=90185)​

二、SpringMVC接收数据

2.1 访问路径设置

@RequestMapping注解的作用就是将请求的 URL 地址和处理请求的方式（handler方法）关联起来，建立映射关系。

SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的方法来处理这个请求。

**1**

精准路径匹配

在@RequestMapping注解指定 URL 地址时，不使用任何通配符，按照请求地址进行精确匹配。

**Java**

**复制**

@Controllerpublic class UserController {/** * 精准设置访问地址 /user/login  */@RequestMapping(value = {&quot;/user/login&quot;})@ResponseBodypublic String login(){System.out.println(&quot;UserController.login&quot;);return &quot;login success!!&quot;;}/* * * 精准设置访问地址 /user/register */@RequestMapping(value = {"/user/register"})@ResponseBodypublic String register(){System.out.println("UserController.register");return "register success!!";}}

**2**

模糊路径匹配

在@RequestMapping注解指定 URL 地址时，通过使用通配符，匹配多个类似的地址。

**Java**

**复制**

@Controllerpublic class ProductController {/** *  路径设置为 /product/*   *    /* 为单层任意字符串  /product/a  /product/aaa 可以访问此handler   *    /product/a/a 不可以 *  路径设置为 /product/**  *   /** 为任意层任意字符串  /product/a  /product/aaa 可以访问此handler   *   /product/a/a 也可以访问  */@RequestMapping(&quot;/product/* ")@ResponseBodypublic String show(){System.out.println("ProductController.show");return "product show!";}}

**纯文本**

**复制**

单层匹配和多层匹配： / *：只能匹配URL地址中的一层，如果想准确匹配两层，那么就写“/* /*”以此类推。 /**：可以匹配URL地址中的多层。 其中所谓的一层或多层是指一个URL地址字符串被“/”划分出来的各个层次 这个知识点虽然对于@RequestMapping注解来说实用性不大，但是将来配置拦截器的时候也遵循这个规则。

**3**

类和方法级别区别

@RequestMapping 注解可以用于类级别和方法级别，它们之间的区别如下：

**a**

设置到类级别：@RequestMapping 注解可以设置在控制器类上，用于映射整个控制器的通用请求路径。这样，如果控制器中的多个方法都需要映射同一请求路径，就不需要在每个方法上都添加映射路径。

**b**

设置到方法级别：@RequestMapping 注解也可以单独设置在控制器方法上，用于更细粒度地映射请求路径和处理方法。当多个方法处理同一个路径的不同操作时，可以使用方法级别的 @RequestMapping 注解进行更精细的映射。

**Java**

**复制**

//1.标记到handler方法@RequestMapping("/user/login")@RequestMapping("/user/register")@RequestMapping("/user/logout")//2.优化标记类+handler方法//类上@RequestMapping("/user")//handler方法上@RequestMapping("/login")@RequestMapping("/register")@RequestMapping("/logout")

**4**

附带请求方式限制

HTTP 协议定义了八种请求方式，在 SpringMVC 中封装到了下面这个枚举类：

**Java**

**复制**

public enum RequestMethod {GET, HEAD, POST, PUT, PATCH, DELETE, OPTIONS, TRACE}

默认情况下：@RequestMapping("/logout") 任何请求方式都可以访问！

如果需要特定指定：

**Java**

**复制**

@Controllerpublic class UserController {/** * 精准设置访问地址 /user/login * method = RequestMethod.POST 可以指定单个或者多个请求方式! * 注意:违背请求方式会出现405异常!  */@RequestMapping(value = {&quot;/user/login&quot;} , method = RequestMethod.POST)@ResponseBodypublic String login(){System.out.println(&quot;UserController.login&quot;);return &quot;login success!!&quot;;}/* * * 精准设置访问地址 /user/register */@RequestMapping(value = {"/user/register"},method = {RequestMethod.POST,RequestMethod.GET})@ResponseBodypublic String register(){System.out.println("UserController.register");return "register success!!";}}

注意：违背请求方式，会出现405异常！！！

**5**

进阶注解

还有 @RequestMapping 的 HTTP 方法特定快捷方式变体：

@GetMapping

@PostMapping

@PutMapping

@DeleteMapping

@PatchMapping

**Java**

**复制**

@RequestMapping(value="/login",method=RequestMethod.GET)||@GetMapping(value="/login")

注意：进阶注解只能添加到handler方法上，无法添加到类上！

**6**

常见配置问题

出现原因：多个 handler 方法映射了同一个地址，导致 SpringMVC 在接收到这个地址的请求时该找哪个 handler 方法处理。

There is already 'demo03MappingMethodHandler' bean method com.atguigu.mvc.handler.Demo03MappingMethodHandler#empGet() mapped.

2.2 接收参数（重点）

2.2.1 param 和 json参数比较

在 HTTP 请求中，我们可以选择不同的参数类型，如 param 类型和 JSON 类型。下面对这两种参数类型进行区别和对比：

**1**

参数编码：

param 类型的参数会被编码为 ASCII 码。例如，假设 name=john doe，则会被编码为 name=john%20doe。而 JSON 类型的参数会被编码为 UTF-8。

**2**

参数顺序：

param 类型的参数没有顺序限制。但是，JSON 类型的参数是有序的。JSON 采用键值对的形式进行传递，其中键值对是有序排列的。

**3**

数据类型：

param 类型的参数仅支持字符串类型、数值类型和布尔类型等简单数据类型。而 JSON 类型的参数则支持更复杂的数据类型，如数组、对象等。

**4**

嵌套性：

param 类型的参数不支持嵌套。但是，JSON 类型的参数支持嵌套，可以传递更为复杂的数据结构。

**5**

可读性：

param 类型的参数格式比 JSON 类型的参数更加简单、易读。但是，JSON 格式在传递嵌套数据结构时更加清晰易懂。

总的来说，param 类型的参数适用于单一的数据传递，而 JSON 类型的参数则更适用于更复杂的数据结构传递。根据具体的业务需求，需要选择合适的参数类型。在实际开发中，常见的做法是：在 GET 请求中采用 param 类型的参数，而在 POST 请求中采用 JSON 类型的参数传递。

2.2.2 param参数接收

**1**

直接接值

客户端请求

​![](https://secure2.wostatic.cn/static/a3oaA2ZHewign7gd5xUG6D/image.png?auth_key=1741057483-ateBG4SbvnqNzPXUiKR9D4-0-7f0f5f11af010fc01a78b05479f008b0&image_process=resize,w_1296&file_size=73349)​

handler接收参数

只要形参数名和类型与传递参数相同，即可自动接收!

**Java**

**复制**

@Controller@RequestMapping("param")public class ParamController {/** * 前端请求: http://localhost:8080/param/value?name=xx&age=18 * * 可以利用形参列表,直接接收前端传递的param参数! *    要求: 参数名 = 形参名 *          类型相同 * 出现乱码正常，json接收具体解决！！ * @return 返回前端数据 */@GetMapping(value="/value")@ResponseBodypublic String setupForm(String name,int age){System.out.println("name = " + name + ", age = " + age);return name + age;}}

**2**

@RequestParam注解

可以使用 @RequestParam 注释将 Servlet 请求参数（即查询参数或表单数据）绑定到控制器中的方法参数。

@RequestParam使用场景：

指定绑定的请求参数名

要求请求参数必须传递

为请求参数提供默认值

基本用法：

**Java**

**复制**

 /** * 前端请求: http://localhost:8080/param/data?name=xx&stuAge=18 *  *  使用@RequestParam注解标记handler方法的形参 *  指定形参对应的请求参数@RequestParam(请求参数名称) */@GetMapping(value="/data")@ResponseBodypublic Object paramForm(@RequestParam("name") String name, @RequestParam("stuAge") int age){System.out.println("name = " + name + ", age = " + age);return name+age;}

默认情况下，使用此批注的方法参数是必需的，但您可以通过将 @RequestParam 批注的 required 标志设置为 false！

如果没有没有设置非必须，也没有传递参数会出现：

​![](https://secure2.wostatic.cn/static/rdbdJyYUSsMtSsANx5icFq/image.png?auth_key=1741057483-swZQLRZLDpEEBhy5cdMTH9-0-e90cbec6e1d046265394fe9c0d233eea&image_process=resize,w_1296&file_size=166066)​

将参数设置非必须，并且设置默认值：

**Java**

**复制**

@GetMapping(value="/data")@ResponseBodypublic Object paramForm(@RequestParam("name") String name, @RequestParam(value = "stuAge",required = false,defaultValue = "18") int age){System.out.println("name = " + name + ", age = " + age);return name+age;}

**3**

特殊场景接值

**a**

一名多值

多选框，提交的数据的时候一个key对应多个值，我们可以使用集合进行接收！

**Java**

**复制**

  /** * 前端请求: http://localhost:8080/param/mul?hbs=吃&hbs=喝 * *  一名多值,可以使用集合接收即可!但是需要使用@RequestParam注解指定 */@GetMapping(value="/mul")@ResponseBodypublic Object mulForm(@RequestParam List<String> hbs){System.out.println("hbs = " + hbs);return hbs;}

**b**

实体接收

Spring MVC 是 Spring 框架提供的 Web 框架，它允许开发者使用实体对象来接收 HTTP 请求中的参数。通过这种方式，可以在方法内部直接使用对象的属性来访问请求参数，而不需要每个参数都写一遍。下面是一个使用实体对象接收参数的示例：

定义一个用于接收参数的实体类：

**Java**

**复制**

public class User {private String name;private int age = 18;// getter 和 setter 略}

在控制器中，使用实体对象接收，示例代码如下：

**Java**

**复制**

@Controller@RequestMapping("param")public class ParamController {@RequestMapping(value = "/user", method = RequestMethod.POST)@ResponseBodypublic String addUser(User user) {// 在这里可以使用 user 对象的属性来接收请求参数System.out.println("user = " + user);return "success";}}

在上述代码中，将请求参数name和age映射到实体类属性上！要求属性名必须等于参数名！否则无法映射！

使用postman传递参数测试：

​![](https://secure2.wostatic.cn/static/p9AtC4uV8sMvSkgcUKbGTa/image.png?auth_key=1741057483-5Zf1LvtVUAa6uAfdi8kwjK-0-da869586cf45e97a8347d4f4c2ee88ba&image_process=resize,w_1296&file_size=222142)​

2.2.3 路径 参数接收

路径传递参数是一种在 URL 路径中传递参数的方式。在 RESTful 的 Web 应用程序中，经常使用路径传递参数来表示资源的唯一标识符或更复杂的表示方式。而 Spring MVC 框架提供了 @PathVariable 注解来处理路径传递参数。

@PathVariable 注解允许将 URL 中的占位符映射到控制器方法中的参数。

例如，如果我们想将 /user/{id} 路径下的 {id} 映射到控制器方法的一个参数中，则可以使用 @PathVariable 注解来实现。

下面是一个使用 @PathVariable 注解处理路径传递参数的示例：

**Java**

**复制**

 /** * 动态路径设计: /user/{动态部分}/{动态部分}   动态部分使用{}包含即可! {}内部动态标识! * 形参列表取值: @PathVariable Long id  如果形参名 = {动态标识} 自动赋值! *              @PathVariable("动态标识") Long id  如果形参名 != {动态标识} 可以通过指定动态标识赋值! * * 访问测试:  /param/user/1/root  -> id = 1  uname = root */@GetMapping("/user/{id}/{name}")@ResponseBodypublic String getUser(@PathVariable Long id, @PathVariable("name") String uname) {System.out.println("id = " + id + ", uname = " + uname);return "user_detail";}

2.2.4 json参数接收

前端传递 JSON 数据时，Spring MVC 框架可以使用 @RequestBody 注解来将 JSON 数据转换为 Java 对象。@RequestBody 注解表示当前方法参数的值应该从请求体中获取，并且需要指定 value 属性来指示请求体应该映射到哪个参数上。其使用方式和示例代码如下：

**1**

前端发送 JSON 数据的示例：（使用postman测试）

**JSON**

**复制**

{"name": "张三","age": 18,"gender": "男"}

**2**

定义一个用于接收 JSON 数据的 Java 类，例如：

**Java**

**复制**

public class Person {private String name;private int age;private String gender;// getter 和 setter 略}

**3**

在控制器中，使用 @RequestBody 注解来接收 JSON 数据，并将其转换为 Java 对象，例如：

**Java**

**复制**

@PostMapping("/person")@ResponseBodypublic String addPerson(@RequestBody Person person) {// 在这里可以使用 person 对象来操作 JSON 数据中包含的属性return "success";}

在上述代码中，@RequestBody 注解将请求体中的 JSON 数据映射到 Person 类型的 person 参数上，并将其作为一个对象来传递给 addPerson() 方法进行处理。

**4**

完善配置

测试：

​![](https://secure2.wostatic.cn/static/jGHVNLJs2adytDeM5VG7Kk/image.png?auth_key=1741057482-eLb1THi7u9n9w85e2jZfzx-0-66bd7e552bb46cc6640a8147150bed0b&image_process=resize,w_1296&file_size=386561)​

问题：

org.springframework.web.HttpMediaTypeNotSupportedException: Content-Type 'application/json;charset=UTF-8' is not supported]

​![](https://secure2.wostatic.cn/static/tojUpG6VkVj5XG41zqA7XU/image.png?auth_key=1741057482-nV4KXLkdoqEHC8hXsVv6L9-0-a43cb7375a67672429ae6b1f61a75dc4&image_process=resize,w_1224&file_size=64206)​

原因：

不支持json数据类型处理

没有json类型处理的工具（jackson）

解决：

springmvc handlerAdpater配置json转化器,配置类需要明确：

**Java**

**复制**

//TODO: SpringMVC对应组件的配置类 [声明SpringMVC需要的组件信息]//TODO: 导入handlerMapping和handlerAdapter的三种方式//1.自动导入handlerMapping和handlerAdapter [推荐]//2.可以不添加,springmvc会检查是否配置handlerMapping和handlerAdapter,没有配置默认加载//3.使用@Bean方式配置handlerMapper和handlerAdapter@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器@Configuration@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫描//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现public class SpringMvcConfig implements WebMvcConfigurer {}

pom.xml 加入jackson依赖

**XML**

**复制**

<dependency><groupId>com.fasterxml.jackson.core</groupId><artifactId>jackson-databind</artifactId><version>2.15.0</version></dependency>

**5**

@EnableWebMvc注解说明

@EnableWebMvc注解效果等同于在 XML 配置中，可以使用 [mvc:annotation-driven](mvc:annotation-driven) 元素！我们来解析[mvc:annotation-driven](mvc:annotation-driven)对应的解析工作！

让我们来查看下[mvc:annotation-driven](mvc:annotation-driven)具体的动作！

先查看[mvc:annotation-driven](mvc:annotation-driven)标签最终对应解析的Java类

​![](https://secure2.wostatic.cn/static/8WWABqUcDmjVia69uGVyNY/image.png?auth_key=1741057482-56xJDR4jLVGz2dJy5TnHmb-0-86dedf67f7b2909c01f89eb803a38609&image_process=resize,w_1296&file_size=1221774)​

查看解析类中具体的动作即可

打开源码：org.springframework.web.servlet.config.MvcNamespaceHandler

​![](https://secure2.wostatic.cn/static/pyTLTV8syHWz4hGCm25CFb/image.png?auth_key=1741057482-oLxtmj17CgHYfiLCGre2fX-0-79620c0f6d57fd18580286e8629747c4&image_process=resize,w_1296&file_size=910505)​

打开源码：org.springframework.web.servlet.config.AnnotationDrivenBeanDefinitionParser

**Java**

**复制**

class AnnotationDrivenBeanDefinitionParser implements BeanDefinitionParser {public static final String HANDLER_MAPPING_BEAN_NAME = RequestMappingHandlerMapping.class.getName();public static final String HANDLER_ADAPTER_BEAN_NAME = RequestMappingHandlerAdapter.class.getName();static {ClassLoader classLoader = AnnotationDrivenBeanDefinitionParser.class.getClassLoader();javaxValidationPresent = ClassUtils.isPresent("jakarta.validation.Validator", classLoader);romePresent = ClassUtils.isPresent("com.rometools.rome.feed.WireFeed", classLoader);jaxb2Present = ClassUtils.isPresent("jakarta.xml.bind.Binder", classLoader);jackson2Present = ClassUtils.isPresent("com.fasterxml.jackson.databind.ObjectMapper", classLoader) &&ClassUtils.isPresent("com.fasterxml.jackson.core.JsonGenerator", classLoader);jackson2XmlPresent = ClassUtils.isPresent("com.fasterxml.jackson.dataformat.xml.XmlMapper", classLoader);jackson2SmilePresent = ClassUtils.isPresent("com.fasterxml.jackson.dataformat.smile.SmileFactory", classLoader);jackson2CborPresent = ClassUtils.isPresent("com.fasterxml.jackson.dataformat.cbor.CBORFactory", classLoader);gsonPresent = ClassUtils.isPresent("com.google.gson.Gson", classLoader);}@Override@Nullablepublic BeanDefinition parse(Element element, ParserContext context) {//handlerMapping加入到ioc容器readerContext.getRegistry().registerBeanDefinition(HANDLER_MAPPING_BEAN_NAME, handlerMappingDef);//添加jackson转化器addRequestBodyAdvice(handlerAdapterDef);addResponseBodyAdvice(handlerAdapterDef);//handlerAdapter加入到ioc容器readerContext.getRegistry().registerBeanDefinition(HANDLER_ADAPTER_BEAN_NAME, handlerAdapterDef);return null;}//具体添加jackson转化对象方法protected void addRequestBodyAdvice(RootBeanDefinition beanDef) {if (jackson2Present) {beanDef.getPropertyValues().add("requestBodyAdvice",new RootBeanDefinition(JsonViewRequestBodyAdvice.class));}}protected void addResponseBodyAdvice(RootBeanDefinition beanDef) {if (jackson2Present) {beanDef.getPropertyValues().add("responseBodyAdvice",new RootBeanDefinition(JsonViewResponseBodyAdvice.class));}}

2.3 接收Cookie数据

可以使用 @CookieValue 注释将 HTTP Cookie 的值绑定到控制器中的方法参数。

考虑使用以下 cookie 的请求：

**Java**

**复制**

JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84

下面的示例演示如何获取 cookie 值：

**Java**

**复制**

@GetMapping("/demo")public void handle(@CookieValue("JSESSIONID") String cookie) { //...}

2.4 接收请求头数据

可以使用 @RequestHeader 批注将请求标头绑定到控制器中的方法参数。

请考虑以下带有标头的请求：

**Java**

**复制**

Host                    localhost:8080Accept                  text/html,application/xhtml+xml,application/xml;q=0.9Accept-Language         fr,en-gb;q=0.7,en;q=0.3Accept-Encoding         gzip,deflateAccept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7Keep-Alive              300

下面的示例获取 Accept-Encoding 和 Keep-Alive 标头的值：

**Java**

**复制**

@GetMapping("/demo")public void handle(@RequestHeader("Accept-Encoding") String encoding, @RequestHeader("Keep-Alive") long keepAlive) { //...}

2.5 原生Api对象操作

​![](https://docs.spring.io/spring-framework/reference/_/img/favicon.ico)[Method Arguments :: Spring Frameworkdocs.spring.io](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html)

下表描述了支持的控制器方法参数

| Controller method argument 控制器方法参数                                  | Description                                                                                |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| jakarta.servlet.ServletRequest,jakarta.servlet.ServletResponse             | 请求/响应对象                                                                              |
| jakarta.servlet.http.HttpSession                                           | 强制存在会话。因此，这样的参数永远不会为null。                                             |
| java.io.InputStream,java.io.Reader                                         | 用于访问由 Servlet API 公开的原始请求正文。                                                |
| java.io.OutputStream,java.io.Writer                                        | 用于访问由 Servlet API 公开的原始响应正文。                                                |
| @PathVariable                                                              | 接收路径参数注解                                                                           |
| @RequestParam                                                              | 用于访问 Servlet 请求参数，包括多部分文件。参数值将转换为声明的方法参数类型。              |
| @RequestHeader                                                             | 用于访问请求标头。标头值将转换为声明的方法参数类型。                                       |
| @CookieValue                                                               | 用于访问Cookie。Cookie 值将转换为声明的方法参数类型。                                      |
| @RequestBody                                                               | 用于访问 HTTP 请求正文。正文内容通过使用HttpMessageConverter实现转换为声明的方法参数类型。 |
| java.util.Map,org.springframework.ui.Model,org.springframework.ui.ModelMap | 共享域对象，并在视图呈现过程中向模板公开。                                                 |
| Errors,BindingResult                                                       | 验证和数据绑定中的错误信息获取对象！                                                       |

获取原生对象示例：

**Java**

**复制**

/** * 如果想要获取请求或者响应对象,或者会话等,可以直接在形参列表传入,并且不分先后顺序! * 注意: 接收原生对象,并不影响参数接收! */@GetMapping("api")@ResponseBodypublic String api(HttpSession session , HttpServletRequest request,HttpServletResponse response){String method = request.getMethod();System.out.println("method = " + method);return "api";}

2.6 共享域对象操作

三、SpringMVC响应数据

3.1 handler方法分析

理解handler方法的作用和组成：

**Java**

**复制**

/** * TODO: 一个controller的方法是控制层的一个处理器,我们称为handler * TODO: handler需要使用@RequestMapping/@GetMapping系列,声明路径,在HandlerMapping中注册,供DS查找! * TODO: handler作用总结: *       1.接收请求参数(param,json,pathVariable,共享域等)  *       2.调用业务逻辑  *       3.响应前端数据(页面（不讲解模版页面跳转）,json,转发和重定向等) * TODO: handler如何处理呢 *       1.接收参数: handler(形参列表: 主要的作用就是用来接收参数) *       2.调用业务: { 方法体  可以向后调用业务方法 service.xx() } *       3.响应数据: return 返回结果,可以快速响应前端数据 */@GetMappingpublic Object handler(简化请求参数接收){调用业务方法 返回的结果 （页面跳转，返回数据（json））return 简化响应前端数据;}

总结： 请求数据接收，我们都是通过handler的形参列表

             前端数据响应，我们都是通过handler的return关键字快速处理！

        springmvc简化了参数接收和响应！

3.2 页面跳转控制

3.2.1 快速返回模板视图

**1**

开发模式回顾

在 Web 开发中，有两种主要的开发模式：前后端分离和混合开发。

前后端分离模式：[重点]

指将前端的界面和后端的业务逻辑通过接口分离开发的一种方式。开发人员使用不同的技术栈和框架，前端开发人员主要负责页面的呈现和用户交互，后端开发人员主要负责业务逻辑和数据存储。前后端通信通过 API 接口完成，数据格式一般使用 JSON 或 XML。前后端分离模式可以提高开发效率，同时也有助于代码重用和维护。

混合开发模式：

指将前端和后端的代码集成在同一个项目中，共享相同的技术栈和框架。这种模式在小型项目中比较常见，可以减少学习成本和部署难度。但是，在大型项目中，这种模式会导致代码耦合性很高，维护和升级难度较大。

对于混合开发，我们就需要使用动态页面技术，动态展示Java的共享域数据！！

**2**

jsp技术了解

JSP（JavaServer Pages）是一种动态网页开发技术，它是由 Sun 公司提出的一种基于 Java 技术的 Web 页面制作技术，可以在 HTML 文件中嵌入 Java 代码，使得生成动态内容的编写更加简单。

JSP 最主要的作用是生成动态页面。它允许将 Java 代码嵌入到 HTML 页面中，以便使用 Java 进行数据库查询、处理表单数据和生成 HTML 等动态内容。另外，JSP 还可以与 Servlet 结合使用，实现更加复杂的 Web 应用程序开发。

JSP 的主要特点包括：

**a**

简单：JSP 通过将 Java 代码嵌入到 HTML 页面中，使得生成动态内容的编写更加简单。

**b**

高效：JSP 首次运行时会被转换为 Servlet，然后编译为字节码，从而可以启用 Just-in-Time（JIT）编译器，实现更高效的运行。

**c**

多样化：JSP 支持多种标准标签库，包括 JSTL（JavaServer Pages 标准标签库）、EL（表达式语言）等，可以帮助开发人员更加方便的处理常见的 Web 开发需求。

总之，JSP 是一种简单高效、多样化的动态网页开发技术，它可以方便地生成动态页面和与 Servlet 结合使用，是 Java Web 开发中常用的技术之一。

**3**

准备jsp页面和依赖

pom.xml依赖

**XML**

**复制**

<!-- jsp需要依赖! jstl--><dependency><groupId>jakarta.servlet.jsp.jstl</groupId><artifactId>jakarta.servlet.jsp.jstl-api</artifactId><version>3.0.0</version></dependency>

jsp页面创建

建议位置：/WEB-INF/下，避免外部直接访问！

位置：/WEB-INF/views/home.jsp

**Java**

**复制**

<%@ page contentType="text/html;charset=UTF-8" language="java" %><html><head><title>Title</title></head><body><!-- 可以获取共享域的数据,动态展示! jsp== 后台vue -->${msg}</body></html>

**4**

快速响应模版页面

**a**

配置jsp视图解析器

**Java**

**复制**

@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器@Configuration@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫描//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现public class SpringMvcConfig implements WebMvcConfigurer {//配置jsp对应的视图解析器@Overridepublic void configureViewResolvers(ViewResolverRegistry registry) {//快速配置jsp模板语言对应的registry.jsp("/WEB-INF/views/",".jsp");}}

**b**

handler返回视图

**Java**

**复制**

/** *  跳转到提交文件页面  /save/jump *   *  如果要返回jsp页面! *     1.方法返回值改成字符串类型 *     2.返回逻辑视图名即可     *         <property name="prefix" value="/WEB-INF/views/"/> *            + 逻辑视图名 + *         <property name="suffix" value=".jsp"/> */@GetMapping("jump")public String jumpJsp(Model model){System.out.println("FileController.jumpJsp");model.addAttribute("msg","request data!!");return "home";}

3.2.2 转发和重定向

在 Spring MVC 中，Handler 方法返回值来实现快速转发，可以使用 redirect 或者 forward 关键字来实现重定向。

**Java**

**复制**

@RequestMapping("/redirect-demo")public String redirectDemo() {// 重定向到 /demo 路径 return "redirect:/demo";}@RequestMapping("/forward-demo")public String forwardDemo() {// 转发到 /demo 路径return "forward:/demo";}//注意： 转发和重定向到项目下资源路径都是相同，都不需要添加项目根路径！填写项目下路径即可！

总结：

将方法的返回值，设置String类型

转发使用forward关键字，重定向使用redirect关键字

关键字: /路径

注意：如果是项目下的资源，转发和重定向都一样都是项目下路径！都不需要添加项目根路径！

3.3 返回JSON数据（重点）

3.3.1 前置准备

3.3.2 @ResponseBody

**1**

方法上使用@ResponseBody

可以在方法上使用 @ResponseBody注解，用于将方法返回的对象序列化为 JSON 或 XML 格式的数据，并发送给客户端。在前后端分离的项目中使用！

测试方法：

**Java**

**复制**

@GetMapping("/accounts/{id}")@ResponseBodypublic Object handle() {// ...return obj;}

具体来说，@ResponseBody 注解可以用来标识方法或者方法返回值，表示方法的返回值是要直接返回给客户端的数据，而不是由视图解析器来解析并渲染生成响应体（viewResolver没用）。

测试方法：

**Java**

**复制**

@RequestMapping(value = "/user/detail", method = RequestMethod.POST)@ResponseBodypublic User getUser(@RequestBody User userParam) {System.out.println("userParam = " + userParam);User user = new User();user.setAge(18);user.setName("John");//返回的对象,会使用jackson的序列化工具,转成json返回给前端!return user;}

返回结果：

​![](https://secure2.wostatic.cn/static/b8AAMNoaVABerV8BGsqNTo/image.png?auth_key=1741057459-f6ye1FPX2mW8ncLafJftJA-0-0e64b5d688d2adeeb07acf0f3684d2e9&image_process=resize,w_1296&file_size=252984)​

**2**

类上使用@ResponseBody

如果类中每个方法上都标记了 @ResponseBody 注解，那么这些注解就可以提取到类上。

**Java**

**复制**

@ResponseBody  //responseBody可以添加到类上,代表默认类中的所有方法都生效!@Controller@RequestMapping("param")public class ParamController {

3.3.3 @RestController

类上的 @ResponseBody 注解可以和 @Controller 注解合并为 @RestController 注解。所以使用了 @RestController 注解就相当于给类中的每个方法都加了 @ResponseBody 注解。

RestController源码:

**Java**

**复制**

@Target(ElementType.TYPE)@Retention(RetentionPolicy.RUNTIME)@Documented@Controller@ResponseBodypublic @interface RestController {/** * The value may indicate a suggestion for a logical component name, * to be turned into a Spring bean in case of an autodetected component. * @return the suggested component name, if any (or empty String otherwise) * @since 4.0.1 */@AliasFor(annotation = Controller.class)String value() default "";}

3.4 返回静态资源处理

**1**

静态资源概念

资源本身已经是可以直接拿到浏览器上使用的程度了，不需要在服务器端做任何运算、处理。典型的静态资源包括：

纯HTML文件

图片

CSS文件

JavaScript文件

……

**2**

静态资源访问和问题解决

web应用加入静态资源

​![](https://secure2.wostatic.cn/static/2ADk35v3kBWvGQSwL99Z3S/image.png?auth_key=1741057459-5DJBNNaE3xpveXiFqMdA1Z-0-c32031dcb9566d765bedf30ca28090a6&image_process=resize,w_1008&file_size=65577)​

手动构建确保编译

​![](https://secure2.wostatic.cn/static/gBRmLxNhoWEd4o2eC2xamA/image.png?auth_key=1741057459-h87ebCh3Nbr3isWNVCRA2W-0-409d02622ef591ace553f0347dcdc96d&image_process=resize,w_792&file_size=161855)​

​![](https://secure2.wostatic.cn/static/rKa2VeGDteC7Xk18LTZGWu/image.png?auth_key=1741057459-mf3nrsmanuLwQbgGQjHXg5-0-e0bee75f2670bbebf5b83513deb36763&image_process=resize,w_792&file_size=209486)​

​![](https://secure2.wostatic.cn/static/jmvurwN5HSB44eVFPaNJa4/image.png?auth_key=1741057459-no1qdkUtxdvCVwARDoD4o8-0-b8d6665dc74ddcdfd167d386b5335c9a&image_process=resize,w_864&file_size=250075)​

访问静态资源

​![](https://secure2.wostatic.cn/static/2Ux86Et6qs3TsDkEg1NQTn/image.png?auth_key=1741057459-4ofNqVSEVzSAz7s7qJ84tc-0-335b8f05f5758fd6077954d08ff34e9b&image_process=resize,w_864&file_size=532187)​

问题分析

DispatcherServlet 的 url-pattern 配置的是“/”

url-pattern 配置“/”表示整个 Web 应用范围内所有请求都由 SpringMVC 来处理

对 SpringMVC 来说，必须有对应的 @RequestMapping 才能找到处理请求的方法

现在 images/mi.jpg 请求没有对应的 @RequestMapping 所以返回 404

问题解决

在 SpringMVC 配置配置类：

**Java**

**复制**

@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器@Configuration@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫描//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现public class SpringMvcConfig implements WebMvcConfigurer {//配置jsp对应的视图解析器@Overridepublic void configureViewResolvers(ViewResolverRegistry registry) {//快速配置jsp模板语言对应的registry.jsp("/WEB-INF/views/",".jsp");}//开启静态资源处理 [mvc:default-servlet-handler/](mvc:default-servlet-handler/)@Overridepublic void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {configurer.enable();}}

再次测试访问图片：

​![](https://secure2.wostatic.cn/static/srYmS8iD3rXGdSx7a7wC6o/image.png?auth_key=1741057459-rR22RogBUy3Lt6349UzUW-0-e9a6ae5ff4d5a6ea566af4920c77f54f&image_process=resize,w_1296&file_size=1228416)​

新的问题：其他原本正常的handler请求访问不了了

handler无法访问

解决方案：

**XML**

**复制**

@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器

四、RESTFul风格设计和实战

4.1 RESTFul风格概述

4.1.1 RESTFul风格简介

4.1.2 RESTFul风格特点

4.1.3 RESTFul风格设计规范

4.1.4 RESTFul风格好处

4.2 RESTFul风格实战

五、SpringMVC其他扩展

5.1 全局异常处理机制

5.2 拦截器使用

5.3 参数校验

六、SpringMVC总结

| 核心点          | 掌握目标                                   |
| --------------- | ------------------------------------------ |
| springmvc框架   | 主要作用、核心组件、调用流程               |
| 简化参数接收    | 路径设计、参数接收、请求头接收、cookie接收 |
| 简化数据响应    | 模板页面、转发和重定向、JSON数据、静态资源 |
| restful风格设计 | 主要作用、具体规范、请求方式和请求参数选择 |
| 功能扩展        | 全局异常处理、拦截器、参数校验注解         |

标题目录

一、SpringMVC简介和体验

1.1 介绍

1.2 主要作用

1.3 核心组件和调用流程理解

1.4 快速体验

二、SpringMVC接收数据

2.1 访问路径设置

2.2 接收参数（重点）

2.2.1 param 和 json参数比较

2.2.2 param参数接收

2.2.3 路径 参数接收

2.2.4 json参数接收

2.3 接收Cookie数据

2.4 接收请求头数据

2.5 原生Api对象操作

2.6 共享域对象操作

2.6.1 属性（共享）域作用回顾

2.6.2 Request级别属性（共享）域

2.6.3 Session级别属性（共享）域

2.6.4 Application级别属性（共享）域

三、SpringMVC响应数据

3.1 handler方法分析

3.2 页面跳转控制

3.2.1 快速返回模板视图

3.2.2 转发和重定向

3.3 返回JSON数据（重点）

3.3.1 前置准备

3.3.2 @ResponseBody

3.3.3 @RestController

3.4 返回静态资源处理

四、RESTFul风格设计和实战

4.1 RESTFul风格概述

4.1.1 RESTFul风格简介

4.1.2 RESTFul风格特点

4.1.3 RESTFul风格设计规范

4.1.4 RESTFul风格好处

4.2 RESTFul风格实战

4.2.1 需求分析

4.2.2 RESTFul风格接口设计

4.2.3 后台接口实现

五、SpringMVC其他扩展

5.1 全局异常处理机制

5.1.1 异常处理两种方式

5.1.2 基于注解异常声明异常处理

5.2 拦截器使用

5.2.1 拦截器概念

5.2.2 拦截器使用

5.3 参数校验

六、SpringMVC总结

本页内容由用户通过 wolai 发布，并不代表 wolai 立场，如有违规侵权，请**投诉/举报** 或提交 **侵权通知**

​![Monica](/assets/images/263cfa60.png)Ctrl+M

解释
