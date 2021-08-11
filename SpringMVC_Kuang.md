# 总结

https://blog.csdn.net/Number_oneEngineer/article/details/82775419?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162864758316780261927546%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=162864758316780261927546&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-82775419.pc_search_result_control_group&utm_term=SpringMVC&spm=1018.2226.3001.4187



## 为什么要使用SpringMVC？

很多应用程序的问题在于处理业务数据的对象和显示业务数据的视图之间存在紧密耦合，通常，更新业务对象的命令都是从视图本身发起的，使视图对任何业务对象更改都有高度敏感性。而且，当多个视图依赖于同一个业务对象时是没有灵活性的。

SpringMVC是一种基于Java，实现了Web MVC设计模式，请求驱动类型的轻量级Web框架，即使用了MVC架构模式的思想，将Web层进行职责解耦。基于请求驱动指的就是使用请求-响应模型，框架的目的就是帮助我们简化开发，SpringMVC也是要简化我们日常Web开发。

## MVC设计模式

MVC设计模式的任务是将包含业务数据的模块与显示模块的视图解耦。这是怎样发生的？在模型和视图之间引入重定向层可以解决问题。此重定向层是控制器，控制器将接收请求，执行更新模型的操作，然后通知视图关于模型更改的消息

![image-20210811111957005](SpringMVC_Kuang.assets/image-20210811111957005.png)

## SpringMVC的核心架构

![image-20210811104455902](SpringMVC_Kuang.assets/image-20210811104455902.png)



## 具体流程：

- （1）用户发送请求至前端控制器DispatcherServlet；
- （2）DispatcherServlet收到请求后，调用HandlerMapping处理器映射器，请求获取Handler；
- （3）处理器映射器根据请求url找到具体的处理器Handler，生成处理器对象及处理器拦截器(如果有则生成)，一并返回给DispatcherServlet；
- （4）DispatcherServlet 调用 HandlerAdapter处理器适配器，请求执行Handler；
- （5）HandlerAdapter 经过适配调用 具体处理器进行处理业务逻辑；
- （6）Handler执行完成返回ModelAndView；
- （7）HandlerAdapter将Handler执行结果ModelAndView返回给DispatcherServlet；
- （8）DispatcherServlet将ModelAndView传给ViewResolver视图解析器进行解析；
- （9）ViewResolver解析后返回具体View；
- （10）DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）
- （11）DispatcherServlet响应用户。

## 各个组件的左右

- 前端控制器 DispatcherServlet：接收请求、响应结果，相当于转发器，有了DispatcherServlet 就减少了其它组件之间的耦合度。
- 处理器映射器 HandlerMapping：根据请求的URL来查找Handler
- 处理器适配器 HandlerAdapter：负责执行Handler
- 处理器 Handler：处理器，需要程序员开发
- 视图解析器 ViewResolver：进行视图的解析，根据视图逻辑名将ModelAndView解析成真正的视图（view）
- 视图View：View是一个接口， 它的实现类支持不同的视图类型，如jsp，freemarker，pdf等等
  

## Springmvc的优点:

（1）可以支持各种视图技术，而不仅仅局限于JSP；

（2）与Spring框架集成（如IoC容器、AOP等）；

（3）清晰的角色分配：前端控制器(dispatcherServlet) ，请求到处理器映射（handlerMapping)，处理器适配器（HandlerAdapter)，视图解析器（ViewResolver）。

（4） 支持各种请求资源的映射策略。

## SpringMVC怎么样设定重定向和转发的？

（1）转发：在返回值前面加"forward:"，譬如"forward:user.do?name=method4"

（2）重定向：在返回值前面加"redirect:"，譬如"redirect:http://www.baidu.com"


## SpringMVC常用注解

- @Controller
  - 负责注册一个bean 到spring 上下文中
  - 也可以使用@RestController，@RestController注解相当于@ResponseBody ＋ @Controller，表示是表现层，除此之外，一般不用别的注解代替。
- @RequestMapping
  -  注解为控制器指定可以处理哪些 URL 请求
- @RequestBody
  - 该注解用于读取Request请求的body部分数据，使用系统默认配置的HttpMessageConverter进行解析，然后把相应的数据绑定到要返回的对象上 ,再把HttpMessageConverter返回的对象数据绑定到 controller中方法的参数上
- @ResponseBody
  - 该注解用于将Controller的方法返回的对象，通过适当的HttpMessageConverter转换为指定格式后，写入到Response对象的body数据区
- @ModelAttribute 　　　
  - 在方法定义上使用 @ModelAttribute 注解：Spring MVC 在调用目标处理方法前，会先逐个调用在方法级上标注了@ModelAttribute 的方法
  - 在方法的入参前使用 @ModelAttribute 注解：可以从隐含对象中获取隐含的模型数据中获取对象，再将请求参数 –绑定到对象中，再传入入参将方法入参对象添加到模型中
- @RequestParam　
  - 在处理方法入参处使用 @RequestParam 可以把请求参 数传递给请求方法
- @PathVariable
  - 绑定 URL 占位符到入参
- @ExceptionHandler
  - 注解到方法上，出现异常时会执行该方法　　
- @ControllerAdvice
  - 使一个Contoller成为全局的异常处理类，类中用@ExceptionHandler方法注解的方法可以处理所有Controller发生的异常









## SpringMVC-annotation版

### 由于Maven可能存在资源过滤的问题，我们将配置完善

```
<build>
   <resources>
       <resource>
           <directory>src/main/java</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
       <resource>
           <directory>src/main/resources</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
   </resources>
</build>
```

### 在pom.xml文件引入相关的依赖：

主要有Spring框架核心库、Spring MVC、servlet , JSTL等。我们在父依赖中已经引入了！(确认)

![image-20210523144032538](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210523144032538.png)

项目artifacts下面WEB-INF添加lib包, 导入所有的依赖

![image-20210523144116845](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210523144116845.png)



### **添加Spring MVC配置文件**

1. 在resource目录下添加springmvc-servlet.xml配置文件，
2. 配置的形式与Spring容器配置基本类似，
3. 为了支持基于注解的IOC，设置了自动扫描包的功能，具体配置信息如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.kuang.controller"/>
    <!-- 让Spring MVC不处理静态资源 .css .js .html .mp3 .mp4-->
    <mvc:default-servlet-handler />
    <!--
    支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping
        和一个AnnotationMethodHandlerAdapter实例
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。

        等于之前的
        <!-处理器映射器-->
<!--    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>-->

        <!--处理器适配器-->
<!--    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>->
     -->
    <mvc:annotation-driven />

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```

1. 在视图解析器中我们把所有的视图都存放在/WEB-INF/目录下，这样可以保证视图安全，因为这个目录下的文件，客户端不能直接访问。

2. - 让IOC的注解生效
   - 静态资源过滤 ：HTML . JS . CSS . 图片 ， 视频 .....
   - MVC的注解驱动
   - 配置视图解析器



### **创建Controller**

1. 编写一个Java控制类：com.kuang.controller.HelloController , 注意编码规范
2. @Controller是为了让Spring IOC容器初始化时自动扫描到；
3. @RequestMapping是为了映射请求路径，这里因为类与方法上都有映射所以访问时应该是/HelloController/hello；
4. 方法中声明Model类型的参数是为了把Action中的数据带到视图中；
5. 方法返回的结果是视图的名称hello，加上配置文件中的前后缀变成WEB-INF/jsp/**hello**.jsp。

```java
@Controller
@RequestMapping("/HelloController")
public class HelloController {

    @RequestMapping("/hello")
    public String hello(Model model){
        //封装数据
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg", "hello, SpringMVCAnnotation!");

        //会被视图解析器处理
        //web-inf/jsp/hello.jsp
        return "hello";
    }
}
```



### **创建视图层**

1. 在WEB-INF/ jsp目录中创建hello.jsp ， 
2. 视图可以直接取出并展示从Controller带回的信息；
3. 可以通过EL表示取出Model中存放的值，或者对象；

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>SpringMVC</title>
</head>
<body>
${msg}
</body>
</html>
```



### **配置Tomcat运行**

配置Tomcat ，  开启服务器 ， 访问 对应的请求路径！

### 小结

1. 新建一个web项目

2. 导入相关jar包

3. 编写web.xml 注册 DispatcherServlet

   1. 向Spring注册我们的DispatcherServlet, 所有的请求都会先经过这个dispatcher
      1. 然后根据请求路径处理器映射器, 处理器适配器
      2. 得到视图名称后解析视图,
      3. 跳转或者重定向到视图
   2. 编写springmvc拦截路径
      1. /  所有的请求都被springmvc拦截

4. 编写springMVC配置文件

   1. 处理器映射器和适配器可以通过 <mvc:annotation-driven /> mvc注解驱动代替
      1. 注册处理器映射器 DefaultAnnotationHandlerMapping
      2. 注册处理器适配器 AnnotationMethodHandlerAdapter
   2. 注册视图解析器 InternalResourceViewResolver 需要手动配置
      1. 设置视图前缀 /WEB-INF/jsp/
      2. 设置视图后缀 .jsp

5. 创建对应的控制类 controller

   1. 类上 @Controller注解 

      1. ```
         //代表这个类是一个控制器, 并且会被spring接管
         //这个注解类中所有方法 如果返回值是String, 并且有页面可以跳转, 就会被视图解析器解析
         ```

   2. 方法 @RequestMapping("/hello") 表示可以映射的请求路径

   3. ModelAndView.addObject("msg", "ControllerTest1"); 添加key-value 可以在前端解析出数据

   4. ModelAndView.setViewName("test"); 添加视图的名称, 视图解析器可以根据视图名称找到对应的视图并转发或者重定向

6. 转发与重定向

   1. ```
              //转发 浏览器地址不会变化
      //        return "forward:/WEB-INF/jsp/test.jsp";
              //重定向 浏览器会变地址
              //文.件[/WEB-INF/jsp/redirect1:/index.jsp.jsp] 未找到
      //        return "redirect1:/index.jsp";
      ```

7. 完善前端视图和controller之间的对应

8. 测试运行调试























# 问题

## Web文件夹没有小地球不识别为web项目

找到 Facets -> Web -> Web Resource Directories，修改下面的路径为当前webapp的实际路径即可。

![image-20210525223739957](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525223739957.png)

## 控制台乱码

```
Tomcat服务器 VM: -Dfile.encoding=UTF-8
```









# SpringMVC概述

ssm: mybatis + spring+springMVC MVC 三层架构

SSM = javaweb做项目

Spring: IOC 和 AOP

SpringMVC: SpringMVC的执行流程!

SpringMVC: SSM框架整合

## 什么是MVC

1. MVC是模型(Model)、视图(View)、控制器(Controller)的简写，是一种软件设计规范。
   1. MVC: 模型(dao, service) 视图(jsp) 控制器(servlet)
   2. 是将业务逻辑、数据、显示分离的方法来组织代码。
   3. MVC主要作用是**降低了视图与业务逻辑间的双向偶合**。
   4. MVC不是一种设计模式，**MVC是一种架构模式**。当然不同的MVC存在差异。
2. **Model（模型）：**
   1. 数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据Dao） 和 服务层（行为Service）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。
3. **View（视图）：**
   1. 负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。
4. **Controller（控制器）：**
   1. 接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。也就是说控制器做了个调度员的工作。
5. 最典型的MVC就是JSP+servlet+javabean的模式
   1. ![image-20210522234820845](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522234820845.png)

## Model1

![image-20210522234838076](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522234838076.png)

## Model2

![image-20210522234905912](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522234905912.png)

1. 过程
   1. 用户发送请求
   2. Servlet接收请求数据, 并调用对应的业务逻辑方法
   3. 业务处理完毕, 返回更新后的数据给servlet
   4. servlet转向JSP, 由JSP来渲染页面
   5. 响应给前端更新后的页面
2. 职责分析
   1. Controller 
      1. 取得表单数据
      2. 调用业务逻辑
      3. 转向指定页面
   2. Model
      1. 业务逻辑
      2. 保存数据的状态
   3. view
      1. 显示页面
3. Model2这样不仅提高的代码的复用率与项目的扩展性，且大大降低了项目的维护成本。
4. Model 1模式的实现比较简单，适用于快速开发小规模项目，Model1中JSP页面身兼View和Controller两种角色，将控制逻辑和表现逻辑混杂在一起，从而导致代码的重用性非常低，增加了应用的扩展性和维护的难度。Model2消除了Model1的缺点。

## 回顾servlet

1. 新建一个Maven工程当做父工程！

   1. 删除src目录

   2. 导入依赖

   3. ```xml
      <properties>
          <java.version>1.8</java.version>
          <maven.compiler.source>1.8</maven.compiler.source>
          <maven.compiler.target>1.8</maven.compiler.target>
          <encoding>UTF-8</encoding>
      </properties>
      <!--依赖-->
          <dependencies>
              <dependency>
                  <groupId>junit</groupId>
                  <artifactId>junit</artifactId>
                  <version>4.12</version>
              </dependency>
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-webmvc</artifactId>
                  <version>5.1.9.RELEASE</version>
              </dependency>
              <dependency>
                  <groupId>javax.servlet</groupId>
                  <artifactId>servlet-api</artifactId>
                  <version>2.5</version>
              </dependency>
              <dependency>
                  <groupId>javax.servlet.jsp</groupId>
                  <artifactId>jsp-api</artifactId>
                  <version>2.2</version>
              </dependency>
              <dependency>
                  <groupId>javax.servlet</groupId>
                  <artifactId>jstl</artifactId>
                  <version>1.2</version>
              </dependency>
          </dependencies>
      ```

2. 建立一个Moudle：springmvc-01-servlet ， 添加Web app的支持！

   1. 模块右击add framework support --> web application

   2. 导入servlet和jsp的依赖

   3. ```xml
      <dependencies>
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>servlet-api</artifactId>
              <version>2.5</version>
          </dependency>
          <dependency>
              <groupId>javax.servlet.jsp</groupId>
              <artifactId>jsp-api</artifactId>
              <version>2.2</version>
          </dependency>
      </dependencies>
      ```

   4. 编写servlet类处理用户请求

      1. ```java
         public class HelloServlet extends HttpServlet {
             @Override
             protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
                 //1.获取前端参数
                 String method = req.getParameter("method");
                 if("add".equals(method)){
                     req.getSession().setAttribute("msg", "执行了add方法");
                 }
                 if("delete".equals(method)){
                     req.getSession().setAttribute("msg", "执行了delete方法");
                 }
                 //2.调用业务层
                 //3.视图转发或者重定向
                 req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req, resp);
             }
         
             @Override
             protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
                 doGet(req, resp);
             }
         }
         ```

   5. 编写Hello.jsp，在WEB-INF目录下新建一个jsp的文件夹，新建hello.jsp

      1. ```
         <%@ page contentType="text/html;charset=UTF-8" language="java" %>
         <html>
           <head>
             <title>$Title$</title>
           </head>
           <body>
           $END$
           </body>
         </html>
         ```

      2. 

   6. 在web.xml中注册Servlet

      1. ```xml
         <servlet>
             <servlet-name>hello</servlet-name>
             <servlet-class>com.kuang.servlet.HelloServlet</servlet-class>
         </servlet>
         <servlet-mapping>
             <servlet-name>hello</servlet-name>
             <url-pattern>/hello</url-pattern>
         </servlet-mapping>
         
         <!--    设置session超时时间-->
         <session-config>
             <session-timeout>15</session-timeout>
         </session-config>
         
         <welcome-file-list>
             <welcome-file>index.jsp</welcome-file>
         </welcome-file-list>
         ```

   7. form.jsp测试访问servlet

   8. 配置Tomcat服务器

      1. \+ -> Tomcat local server 配置服务器版本 添加artifact

      2. ![image-20210522234444588](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522234444588.png)

      3. 配置Tomcat，并启动测试

      4. - localhost:8080/user?method=add
         - localhost:8080/user?method=delete

## MVC 要做的事情

1. 将url映射到java类和java类的方法
2. 封装用户提交的数据
3. 处理请求--调用相关的业务处理--封装响应数据
4. 将响应的数据渲染 .jsp/html 等表示层数据



# SpringMVC

![image-20210523143108204](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210523143108204.png)

```
官方文档
https://repo.spring.io/release/org/springframework/spring/
https://docs.spring.io/spring-framework/docs/4.3.24.RELEASE/spring-framework-reference/html/mvc.html
```

# 新建一个SpringMVC-xml项目

## 新建一个Moudle ， springmvc-02-hello ， 添加web的支持！

## 确定导入了SpringMVC 的依赖！

## 配置web.xml  ， 注册DispatcherServlet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
        version="4.0">

   <!--1.注册DispatcherServlet-->
   <servlet>
       <servlet-name>springmvc</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!--启动级别-1-->
       <load-on-startup>1</load-on-startup>
   </servlet>

   <!--/ 匹配所有的请求；（不包括.jsp）-->
   <!--/* 匹配所有的请求；（包括.jsp）-->
   <servlet-mapping>
       <servlet-name>springmvc</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>

</web-app>
```



## 编写SpringMVC 的 配置文件

名称：springmvc-servlet.xml  : [servletname]-servlet.xml; 说明，这里的名称要求是按照官方来的

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

### 添加 处理映射器

```
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
```

### 添加 处理器适配器

```
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
```



### 添加 视图解析器

```xml
<!--视图解析器:DispatcherServlet给他的ModelAndView-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
   <!--前缀-->
   <property name="prefix" value="/WEB-INF/jsp/"/>
   <!--后缀-->
   <property name="suffix" value=".jsp"/>
</bean>
```



### 编写我们要操作业务Controller 

1. 方法一:实现Controller接口，

2. 方法二:增加注解；

3. 装数据

   1. addObject("key", "value")

4. 封视图；

   1. setViewName("viewname")

5. 需要返回一个ModelAndView，

6. ```java
   package com.kuang.controller;
   
   
   import org.springframework.web.servlet.ModelAndView;
   import org.springframework.web.servlet.mvc.Controller;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   
   /**
    * @author Chaoqun Cheng
    * @date 2021-05-2021/5/23-14:16
    */
   
   public class HelloController implements Controller {
       public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
           ModelAndView mv = new ModelAndView();
   
           //业务代码
           String res = "helloSpringMVC";
   
           mv.addObject("msg", res);
   
           //视图跳转
           mv.setViewName("test");
   
           return mv;
       }
   }
   ```



### 将自己的类交给SpringIOC容器，注册bean

```
<!--Handler-->
<bean id="/hello" class="com.kuang.controller.HelloController"/>
```

### 写要跳转的jsp页面，显示ModelandView存放的数据，以及我们的正常页面；

test.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>Kuangshen</title>
</head>
<body>
${msg}
</body>
</html>
```

### 配置Tomcat 启动测试！

### 404报错

**可能遇到的问题：访问出现404，排查步骤：**

1. 查看控制台输出，看一下是不是缺少了什么jar包。
2. 如果jar包存在，显示无法输出，就在IDEA的项目发布中，添加lib依赖！
3. 重启Tomcat 即可解决！
4. Project Setting --> Artifacts --> 对应项目 --> WEB-INF 下创建lib包, 将项目的依赖导入到lib包内
5. ![image-20210523111605881](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210523111605881.png)



HandlerMapping 处理器映射, 根据请求的URL查找对应可以处理请求的handler, 将处理器返回给DispatcherHandler

HandlerAdapter 处理器适配器, 按照特定的规则去执行handler(找到对应的实现了Controller接口的类, 并且名字符合)

## 注意点

### WEB-INF 下新建 lib包

新建test.jsp 跳转的页面 ${msg}

### 确认依赖

1. Maven有springmvc依赖
   1. ![image-20210523135620449](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210523135620449.png)
2. 确认项目的Artifacts下面有lib包 并且有所有需要的依赖
   1. ![image-20210523135712979](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210523135712979.png)



# 新建SpringMVC-annotation版

## **新建一个Moudle，springmvc-03-hello-annotation 。添加web支持！**

## 由于Maven可能存在资源过滤的问题，我们将配置完善

```
<build>
   <resources>
       <resource>
           <directory>src/main/java</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
       <resource>
           <directory>src/main/resources</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
   </resources>
</build>
```

## 在pom.xml文件引入相关的依赖：

主要有Spring框架核心库、Spring MVC、servlet , JSTL等。我们在父依赖中已经引入了！(确认)

![image-20210523144032538](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210523144032538.png)

项目artifacts下面WEB-INF添加lib包, 导入所有的依赖

![image-20210523144116845](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210523144116845.png)



### **添加Spring MVC配置文件**

1. 在resource目录下添加springmvc-servlet.xml配置文件，
2. 配置的形式与Spring容器配置基本类似，
3. 为了支持基于注解的IOC，设置了自动扫描包的功能，具体配置信息如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.kuang.controller"/>
    <!-- 让Spring MVC不处理静态资源 .css .js .html .mp3 .mp4-->
    <mvc:default-servlet-handler />
    <!--
    支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping
        和一个AnnotationMethodHandlerAdapter实例
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。

        等于之前的
        <!-处理器映射器-->
<!--    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>-->

        <!--处理器适配器-->
<!--    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>->
     -->
    <mvc:annotation-driven />

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```

1. 在视图解析器中我们把所有的视图都存放在/WEB-INF/目录下，这样可以保证视图安全，因为这个目录下的文件，客户端不能直接访问。

2. - 让IOC的注解生效
   - 静态资源过滤 ：HTML . JS . CSS . 图片 ， 视频 .....
   - MVC的注解驱动
   - 配置视图解析器



## **创建Controller**

1. 编写一个Java控制类：com.kuang.controller.HelloController , 注意编码规范
2. @Controller是为了让Spring IOC容器初始化时自动扫描到；
3. @RequestMapping是为了映射请求路径，这里因为类与方法上都有映射所以访问时应该是/HelloController/hello；
4. 方法中声明Model类型的参数是为了把Action中的数据带到视图中；
5. 方法返回的结果是视图的名称hello，加上配置文件中的前后缀变成WEB-INF/jsp/**hello**.jsp。

```java
@Controller
@RequestMapping("/HelloController")
public class HelloController {

    @RequestMapping("/hello")
    public String hello(Model model){
        //封装数据
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg", "hello, SpringMVCAnnotation!");

        //会被视图解析器处理
        //web-inf/jsp/hello.jsp
        return "hello";
    }
}
```

### **创建视图层**

1. 在WEB-INF/ jsp目录中创建hello.jsp ， 
2. 视图可以直接取出并展示从Controller带回的信息；
3. 可以通过EL表示取出Model中存放的值，或者对象；

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>SpringMVC</title>
</head>
<body>
${msg}
</body>
</html>
```



### **配置Tomcat运行**

配置Tomcat ，  开启服务器 ， 访问 对应的请求路径！

### 小结

1. 新建一个web项目
2. 导入相关jar包
3. 编写web.xml 注册 DispatcherServlet
4. 编写springMVC配置文件
5. 创建对应的控制类 controller
6. 完善前端视图和controller之间的对应
7. 测试运行调试

# 使用springMVC必须配置的三大件：

1. 处理器映射器
2. 处理器适配器
3. 视图解析器
4. 通常，我们只需要**手动配置视图解析器**，而**处理器映射器**和**处理器适配器**只需要开启**注解驱动**即可，而省去了大段的xml配置



# 控制器Controller

1. 控制器复杂提供访问应用程序的行为，通常通过接口定义或注解定义两种方法实现。
2. 控制器负责解析用户的请求并将其转换为一个模型。
3. 在Spring MVC中一个控制器类可以包含多个方法
4. 在Spring MVC中，对于Controller的配置方式有很多种

## 实现Controller接口

Controller是一个接口，在org.springframework.web.servlet.mvc包下，接口中只有一个方法；

```java
//实现该接口的类获得控制器功能
public interface Controller {
   //处理请求且返回一个模型与视图对象
   ModelAndView handleRequest(HttpServletRequest var1, HttpServletResponse var2) throws Exception;
}
```

### **测试**

1. 新建一个Moudle，springmvc-04-controller 。将刚才的03 拷贝一份, 我们进行操作！

2. - 删掉HelloController
   - mvc的配置文件只留下 视图解析器！

3. 编写一个Controller类，ControllerTest1

   1. ```java
      //定义控制器
      //注意点：不要导错包，实现Controller接口，重写方法；
      public class ControllerTest1 implements Controller {
      
         public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
             //返回一个模型视图对象
             ModelAndView mv = new ModelAndView();
             mv.addObject("msg","Test1Controller");
             mv.setViewName("test");
             return mv;
        }
      }
      ```

4. 编写完毕后，去Spring配置文件中注册请求的bean；name对应请求路径，class对应处理请求的类

   1. ```xml
      <bean name="/t1" class="com.kuang.controller.ControllerTest1"/>
      ```

5. 编写前端test.jsp，注意在WEB-INF/jsp目录下编写，对应我们的视图解析器

   1. ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
         <title>Kuangshen</title>
      </head>
      <body>
      ${msg}
      </body>
      </html>
      ```

6. 配置Tomcat运行测试

### 说明

1. 实现接口Controller定义控制器是较老的办法
2. 缺点是：一个控制器中只有一个方法，如果要多个方法则需要定义多个Controller；定义的方式比较麻烦；



# 使用注解@Controller

1. @Controller注解类型用于声明Spring类的实例是一个控制器（在讲IOC时还提到了另外3个注解）；

   1. @Component: 组件
   2. @Service: service
   3. @Controller: controller
   4. @Repository: dao

2. Spring可以使用扫描机制来找到应用程序中所有基于注解的控制器类，为了保证Spring能找到你的控制器，需要在配置文件中声明组件扫描。

   1. ```xml
      <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
      <context:component-scan base-package="com.kuang.controller"/>
      ```

3. 增加一个ControllerTest2类，使用注解实现；

   1. ```java
      //代表这个类是一个控制器, 并且会被spring接管
      //这个注解类中所有方法 如果返回值是String, 并且有页面可以跳转, 就会被视图解析器解析
      @Controller
      public class ControllerTest2 {
          //映射访问路径
          @RequestMapping("/t2")
          public String test1(Model model){
               //Spring MVC会自动实例化一个Model对象用于向视图中传值
              model.addAttribute("msg", "ControllerTest2-Annotation");
              //返回视图位置
              return "test";// /WEB-INF/jsp/test.jsp
          }
          
          @RequestMapping("/t3")
          public String test3(Model model){
              model.addAttribute("msg", "test3");
              return "test";
          }
      }
      ```

4. 运行tomcat测试

5. 可以发现，我们的两个请求都可以指向一个视图，但是页面结果的结果是不一样的，从这里可以看出视图是被复用的，而控制器与视图之间是弱偶合关系。

6. 注解方式是平时使用的最多的方式！



# RequestMapping

1. **@RequestMapping**

2. @RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法。可用于类或方法上。

3. 用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

4. 只注解在方法上面

   1. ```java
      @Controller
      public class TestController {
         @RequestMapping("/h1")
         public String test(){
             return "test";
        }
      }
      ```

   2. 访问路径：http://localhost:8080 / 项目名 / h1

5. 同时注解类与方法

   1. ```java
      @Controller
      @RequestMapping("/admin")
      public class TestController {
         @RequestMapping("/h1")
         public String test(){
             return "test";
        }
      }
      ```

   2. 访问路径：http://localhost:8080 / 项目名/ admin /h1  , 需要先指定类的路径再指定方法的路径；



# RestFul风格

## 概念

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

## 功能

1. 资源：互联网所有的事物都可以被抽象为资源
2. 资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。
3. 分别对应 添加、 删除、修改、查询。

## **传统方式操作资源**

1. 通过不同的参数来实现不同的效果！方法单一，post 和 get

2. ```
   http://127.0.0.1/item/queryItem.action?id=1 查询,GET
   
   http://127.0.0.1/item/saveItem.action 新增,POST
   
   http://127.0.0.1/item/updateItem.action 更新,POST
   
   http://127.0.0.1/item/deleteItem.action?id=1 删除,GET或POST
   ```

## **使用RESTful操作资源**

1. 可以通过不同的请求方式来实现不同的效果！如下：请求地址一样，但是功能可以不同！

2. ```
   http://127.0.0.1/item/1 查询,GET
   
   http://127.0.0.1/item 新增,POST
   
   http://127.0.0.1/item 更新,PUT
   
   http://127.0.0.1/item/1 删除,DELETE
   ```

## **学习测试**

1. 在新建一个类 RestFulController

   1. ```java
      @Controller
      public class RestFulController {
      }
      ```

2. 在Spring MVC中可以使用  @PathVariable 注解，让方法参数的值对应绑定到一个URI模板变量上。

   1. ```java
      @Controller
      public class RestFulController {
      
          //原始传参风格:   http://localhost:8080/springmvc_04_controller_war_exploded/add?a=1&b=2
      
          //安全 
          //Restful风格:    http://localhost:8080/springmvc_04_controller_war_exploded/add/a/b
      
          // http://localhost:8080/add/1/2
          @RequestMapping(value="/add/{a}/{b}", method= RequestMethod.GET)
      //    @GetMapping("/add/{a}/{b}")
      //    @DeleteMapping("/add/{a}/{b}")
      //    @PostMapping("/add/{a}/{b}")
          public String test1(@PathVariable int a, @PathVariable String b, Model model){
              String res = a+b;
              model.addAttribute("msg", "结果为1 : "+res);
              return "test";
          }
      
      
          // http://localhost:8080/add/1/2
          @PostMapping("/add/{a}/{b}")
          public String test2(@PathVariable int a, @PathVariable String b, Model model){
              String res = a+b;
              model.addAttribute("msg", "结果为2 : "+res);
              return "test";
          }
      }
      ```

3. 思考 使用路径变量的好处

   1. 使路径变得更加简洁；
   2. 获得参数更加方便，框架会自动进行类型转换。
   3. 通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法，

## **使用method属性指定请求类型**

1. 用于约束请求的类型，可以收窄请求范围。指定请求谓词的类型如GET, POST, HEAD, OPTIONS, PUT, PATCH, DELETE, TRACE等

2. ```java
   //映射访问路径,必须是Get请求    
   @RequestMapping(value="/add/{a}/{b}", method= RequestMethod.GET)
       public String test1(@PathVariable int a, @PathVariable String b, Model model){
           String res = a+b;
           model.addAttribute("msg", "结果为1 : "+res);
           return "test";
       }
   ```



## 小结

1. Spring MVC 的 @RequestMapping 注解能够处理 HTTP 请求的方法, 比如 GET, PUT, POST, DELETE 以及 PAT

2. **所有的地址栏请求默认都会是 HTTP GET 类型的。**

3. 方法级别的注解变体有如下几个：组合注解

   1. ```
      @GetMapping
      @PostMapping
      @PutMapping
      @DeleteMapping
      @PatchMapping
      ```

4. @GetMapping 是一个组合注解，平时使用的会比较多！

   1. 它所扮演的是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式。



# 转发和重定向

## ModelAndView

1. 设置ModelAndView对象 , 根据view的名称 , 和视图解析器跳到指定的页面 .

2. 页面 : {视图解析器前缀} + viewName +{视图解析器后缀}

   1. ```xml
      <!-- 视图解析器 -->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
           id="internalResourceViewResolver">
         <!-- 前缀 -->
         <property name="prefix" value="/WEB-INF/jsp/" />
         <!-- 后缀 -->
         <property name="suffix" value=".jsp" />
      </bean>
      ```

   2. 对应的controller类

   3. ```java
      public class ControllerTest1 implements Controller {
      
         public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
             //返回一个模型视图对象
             ModelAndView mv = new ModelAndView();
             mv.addObject("msg","ControllerTest1");
             mv.setViewName("test");
             return mv;
        }
      }
      ```



## ServletAPI

1. 通过设置ServletAPI , 不需要视图解析器 .

   1. 通过HttpServletResponse进行输出
   2. 通过HttpServletResponse实现重定向
   3. 通过HttpServletResponse实现转发

2. ```java
   @Controller
   public class ResultGo {
   
      @RequestMapping("/result/t1")
      public void test1(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
          rsp.getWriter().println("Hello,Spring BY servlet API");
     }
   
      @RequestMapping("/result/t2")
      public void test2(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
          rsp.sendRedirect("/index.jsp");
     }
   
      @RequestMapping("/result/t3")
      public void test3(HttpServletRequest req, HttpServletResponse rsp) throws Exception {
          //转发
          req.setAttribute("msg","/result/t3");
          req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,rsp);
     }
   
   }
   ```

## SpringMVC

### **通过SpringMVC来实现转发和重定向 - 无需视图解析器；**

测试前，需要将视图解析器注释掉

```java
@Controller
public class ResultSpringMVC {
   @RequestMapping("/rsm/t1")
   public String test1(){
       //转发
       return "/index.jsp";
  }

   @RequestMapping("/rsm/t2")
   public String test2(){
       //转发二
       return "forward:/index.jsp";
  }

   @RequestMapping("/rsm/t3")
   public String test3(){
       //重定向
       return "redirect:/index.jsp";
  }
}
```



### **通过SpringMVC来实现转发和重定向 - 有视图解析器；**

1. 重定向 , 不需要视图解析器 , 本质就是重新请求一个新地方嘛 , 所以注意路径问题.

2. 可以重定向到另外一个请求实现 .

3. ```java
   @Controller
   public class ResultSpringMVC2 {
      @RequestMapping("/rsm2/t1")
      public String test1(){
          //转发
          return "test";
     }
      @RequestMapping("/rsm2/t2")
      public String test2(){
          //重定向
          return "redirect:/index.jsp";
          //return "redirect:hello.do"; //hello.do为另一个请求/
     }
   }
   ```

# 处理提交数据

## **提交的域名称和处理方法的参数名一致**

1. 提交数据 : http://localhost:8080/hello?name=kuangshen

2. 处理方法 :

   1. ```java
      @RequestMapping("/hello")
      public String hello(String name){
         System.out.println(name);
         return "hello";
      }
      ```

   2. 后台输出 : kuangshen

## **提交的域名称和处理方法的参数名不一致**

1. 提交数据 : http://localhost:8080/hello?username=kuangshen

2. 处理方法 :

   1. ```java
      //@RequestParam("username") : username提交的域的名称 .
      @RequestMapping("/hello")
      public String hello(@RequestParam("username") String name){
         System.out.println(name);
         return "hello";
      }
      ```

   2. 后台输出 : kuangshen

   

## **提交的是一个对象**

1. 要求提交的表单域和对象的属性名一致  , 参数使用对象即可

   1. 实体类

   2. ```java
      public class User {
         private int id;
         private String name;
         private int age;
         //构造
         //get/set
         //tostring()
      }
      ```

2. 提交数据 : http://localhost:8080/mvc04/user?name=kuangshen&id=1&age=15

3. 处理方法 :

   1. ```java
      @RequestMapping("/user")
      public String user(User user){
         System.out.println(user);
         return "hello";
      }
      ```

   后台输出 : User { id=1, name='kuangshen', age=15 }

   说明：如果使用对象的话，前端传递的参数名和对象名必须一致，否则就是null。



# 数据显示到前端

## **第一种 : 通过ModelAndView**

```java
public class ControllerTest1 implements Controller {

   public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
       //返回一个模型视图对象
       ModelAndView mv = new ModelAndView();
       mv.addObject("msg","ControllerTest1");
       mv.setViewName("test");
       return mv;
  }
}
```



## **第二种 : 通过ModelMap**

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name, ModelMap model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("name",name);
   System.out.println(name);
   return "hello";
}
```



## **第三种 : 通过Model**

```java
@RequestMapping("/ct2/hello")
public String hello(@RequestParam("username") String name, Model model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("msg",name);
   System.out.println(name);
   return "test";
}
```



## 对比

1. 就对于新手而言简单来说使用区别就是：

2. Model 只有寥寥几个方法只适合用于储存数据，简化了新手对于Model对象的操作和理解；

3. ModelMap 继承了 LinkedMap ，除了实现了自身的一些方法，同样的继承 LinkedMap 的方法和特性；

4. ModelAndView 可以在储存数据的同时，可以进行设置返回的逻辑视图，进行控制展示层的跳转。

5. 当然更多的以后开发考虑的更多的是性能和优化，就不能单单仅限于此的了解。

   **请使用80%的时间打好扎实的基础，剩下18%的时间研究框架，2%的时间去学点英文，框架的官方文档永远是最好的教程。**



# 乱码问题

## 我们可以在首页编写一个提交的表单

```jsp
<form action="/e/t" method="post">
 <input type="text" name="name">
 <input type="submit">
</form>
```

## 后台编写对应的处理类

```java
@Controller
public class Encoding {
   @RequestMapping("/e/t")
   public String test(Model model,String name){
       model.addAttribute("msg",name); //获取表单提交的值
       return "test"; //跳转到test页面显示输入的值
  }
}
```

## 配置SpringMVC提供的过滤器

1. 不得不说，乱码问题是在我们开发中十分常见的问题，也是让我们程序猿比较头大的问题！
2. 以前乱码问题通过过滤器解决 , 而SpringMVC给我们提供了一个过滤器 , 可以在web.xml中配置 .
3. 修改了xml文件需要重启服务器！

```xml
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>
<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

但是我们发现 , 有些极端情况下.这个过滤器对get的支持不好 .

处理方法 :

1、修改tomcat配置文件 ：设置编码！

```
<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
          connectionTimeout="20000"
          redirectPort="8443" />
```



2、自定义过滤器

```java
package com.kuang.filter;

/**
 * @author Chaoqun Cheng
 * @date 2021-05-2021/5/23-20:05
 */

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Map;

/**
 * 解决get和post请求 全部乱码的过滤器
 */
public class GenericEncodingFilter implements Filter {

    public void destroy() {
    }

    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        //处理response的字符编码
        HttpServletResponse myResponse=(HttpServletResponse) response;
        myResponse.setContentType("text/html;charset=UTF-8");

        // 转型为与协议相关对象
        HttpServletRequest httpServletRequest = (HttpServletRequest) request;
        // 对request包装增强
        HttpServletRequest myrequest = new MyRequest(httpServletRequest);
        chain.doFilter(myrequest, response);
    }

    public void init(FilterConfig filterConfig) throws ServletException {
    }

}

//自定义request对象，HttpServletRequest的包装类
class MyRequest extends HttpServletRequestWrapper {

    private HttpServletRequest request;
    //是否编码的标记
    private boolean hasEncode;
    //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
    public MyRequest(HttpServletRequest request) {
        super(request);// super必须写
        this.request = request;
    }

    // 对需要增强方法 进行覆盖
    @Override
    public Map getParameterMap() {
        // 先获得请求方式
        String method = request.getMethod();
        if (method.equalsIgnoreCase("post")) {
            // post请求
            try {
                // 处理post乱码
                request.setCharacterEncoding("utf-8");
                return request.getParameterMap();
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        } else if (method.equalsIgnoreCase("get")) {
            // get请求
            Map<String, String[]> parameterMap = request.getParameterMap();
            if (!hasEncode) { // 确保get手动编码逻辑只运行一次
                for (String parameterName : parameterMap.keySet()) {
                    String[] values = parameterMap.get(parameterName);
                    if (values != null) {
                        for (int i = 0; i < values.length; i++) {
                            try {
                                // 处理get乱码
                                values[i] = new String(values[i]
                                        .getBytes("ISO-8859-1"), "utf-8");
                            } catch (UnsupportedEncodingException e) {
                                e.printStackTrace();
                            }
                        }
                    }
                }
                hasEncode = true;
            }
            return parameterMap;
        }
        return super.getParameterMap();
    }

    //取一个值
    @Override
    public String getParameter(String name) {
        Map<String, String[]> parameterMap = getParameterMap();
        String[] values = parameterMap.get(name);
        if (values == null) {
            return null;
        }
        return values[0]; // 取回参数的第一个值
    }

    //取所有值
    @Override
    public String[] getParameterValues(String name) {
        Map<String, String[]> parameterMap = getParameterMap();
        String[] values = parameterMap.get(name);
        return values;
    }
}

```



# JSON

## 什么是JSON？

1. JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式，目前使用特别广泛。

2. 采用完全独立于编程语言的**文本格式**来存储和表示数据。

3. 简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。

4. 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

5. 在 JavaScript 语言中，一切都是对象。因此，任何JavaScript 支持的类型都可以通过 JSON 来表示，例如字符串、数字、对象、数组等。看看他的要求和语法格式：

   1. 对象表示为键值对，数据由逗号分隔
   2. 花括号保存对象
   3. 方括号保存数组

6. **JSON 键值对**是用来保存 JavaScript 对象的一种方式，和 JavaScript 对象的写法也大同小异，键/值对组合中的键名写在前面并用双引号 "" 包裹，使用冒号 : 分隔，然后紧接着值：

   1. ```json
      {"name": "QinJiang"}
      {"age": "3"}
      {"sex": "男"}
      ```

7. 很多人搞不清楚 JSON 和 JavaScript 对象的关系，甚至连谁是谁都不清楚。其实，可以这么理解：

8. JSON 是 JavaScript 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串。

9. ```
   var obj = {a: 'Hello', b: 'World'}; //这是一个对象，注意键名也是可以使用引号包裹的
   var json = '{"a": "Hello", "b": "World"}'; //这是一个 JSON 字符串，本质是一个字符串
   ```

## **JSON 和 JavaScript 对象互转**

1. 要实现从JSON字符串转换为JavaScript 对象，使用 JSON.parse() 方法：

   1. ```
      var obj = JSON.parse('{"a": "Hello", "b": "World"}');
      //结果是 {a: 'Hello', b: 'World'}
      ```

2. 要实现从JavaScript 对象转换为JSON字符串，使用 JSON.stringify() 方法：

   1. ```
      var json = JSON.stringify({a: 'Hello', b: 'World'});
      //结果是 '{"a": "Hello", "b": "World"}'
      ```

3. 代码测试

   1. ```html
      <!DOCTYPE html>
      <html lang="en">
      <head>
         <meta charset="UTF-8">
         <title>JSON_秦疆</title>
      </head>
      <body>
      
      <script type="text/javascript">
         //编写一个js的对象
         var user = {
             name:"秦疆",
             age:3,
             sex:"男"
        };
         //将js对象转换成json字符串
         var str = JSON.stringify(user);
         console.log(str);
         
         //将json字符串转换为js对象
         var user2 = JSON.parse(str);
         console.log(user2.age,user2.name,user2.sex);
      
      </script>
      
      </body>
      </html>
      ```





## Controller返回JSON数据

Jackson应该是目前比较好的json解析工具了

当然工具不止这一个，比如还有阿里巴巴的 fastjson 等等。

1. 我们这里使用Jackson，使用它需要导入它的jar包；

2. ```
   <dependency>
       <groupId>com.fasterxml.jackson.core</groupId>
       <artifactId>jackson-databind</artifactId>
       <version>2.12.3</version>
   </dependency>
   ```

3. 配置SpringMVC需要的配置 web.xml

   1. ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
               version="4.0">
      
          <!--1.注册servlet-->
          <servlet>
              <servlet-name>SpringMVC</servlet-name>
              <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
              <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
              <init-param>
                  <param-name>contextConfigLocation</param-name>
                  <param-value>classpath:springmvc-servlet.xml</param-value>
              </init-param>
              <!-- 启动顺序，数字越小，启动越早 -->
              <load-on-startup>1</load-on-startup>
          </servlet>
          <!--所有请求都会被springmvc拦截 -->
          <servlet-mapping>
              <servlet-name>SpringMVC</servlet-name>
              <url-pattern>/</url-pattern>
          </servlet-mapping>
      
          <filter>
              <filter-name>encoding</filter-name>
              <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
              <init-param>
                  <param-name>encoding</param-name>
                  <param-value>utf-8</param-value>
              </init-param>
          </filter>
          <filter-mapping>
              <filter-name>encoding</filter-name>
              <url-pattern>/*</url-pattern>
          </filter-mapping>
      </web-app>
      ```

4. springmvc-servlet.xml

   1. ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:context="http://www.springframework.org/schema/context"
            xmlns:mvc="http://www.springframework.org/schema/mvc"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
             http://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/context
             https://www.springframework.org/schema/context/spring-context.xsd
             http://www.springframework.org/schema/mvc
             https://www.springframework.org/schema/mvc/spring-mvc.xsd">
      
         <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
         <context:component-scan base-package="com.kuang.controller"/>
      
         <!-- 视图解析器 -->
         <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
               id="internalResourceViewResolver">
             <!-- 前缀 -->
             <property name="prefix" value="/WEB-INF/jsp/" />
             <!-- 后缀 -->
             <property name="suffix" value=".jsp" />
         </bean>
      
      </beans>
      ```

5. 我们随便编写一个User的实体类，然后我们去编写我们的测试Controller；

   1. ```java
      package com.kuang.pojo;
      public class User {
          private String name;
          private int age;
          private String sex;
          //get/set/constructor
      }
      ```

6. 这里我们需要两个新东西，一个是@ResponseBody，一个是ObjectMapper对象，我们看下具体的用法

7. 编写一个Controller；

   1. ```java
      @Controller
      public class UserController {
      
         @RequestMapping("/json1")
         @ResponseBody
         public String json1() throws JsonProcessingException {
             //创建一个jackson的对象映射器，用来解析数据
             ObjectMapper mapper = new ObjectMapper();
             //创建一个对象
             User user = new User("秦疆1号", 3, "男");
             //将我们的对象解析成为json格式
             String str = mapper.writeValueAsString(user);
             //由于@ResponseBody注解，这里会将str转成json格式返回；十分方便
             return str;
        }
      
      }
      ```

8. 配置Tomcat ， 启动测试一下！

发现出现了乱码问题，我们需要设置一下他的编码格式为utf-8，以及它返回的类型；

通过@RequestMaping的produces属性来实现，修改下代码

```
//produces:指定响应体返回类型和编码
@RequestMapping(value = "/json1",produces = "application/json;charset=utf-8")
```

【注意：使用json记得处理乱码问题】

## **乱码统一解决**

上一种方法比较麻烦，如果项目中有许多请求则每一个都要添加，可以通过Spring配置统一指定，这样就不用每次都去处理了！

我们可以在springmvc的配置文件上添加一段消息StringHttpMessageConverter转换配置！

```xml
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       <bean class="org.springframework.http.converter.StringHttpMessageConverter">
           <constructor-arg value="UTF-8"/>
       </bean>
       <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
           <property name="objectMapper">
               <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                   <property name="failOnEmptyBeans" value="false"/>
               </bean>
           </property>
       </bean>
   </mvc:message-converters>
</mvc:annotation-driven>
```

## **返回json字符串统一解决**

1. 在类上直接使用 **@RestController** ，
2. 这样子，里面所有的方法都只会返回 json 字符串了，
3. 不用再每一个都添加@ResponseBody ！
4. 我们在前后端分离开发中，一般都使用 @RestController ，十分便捷！

### 输出单个json

1. ```java
   @RestController
   public class UserController {
   
      //produces:指定响应体返回类型和编码
      @RequestMapping(value = "/json1")
      public String json1() throws JsonProcessingException {
          //创建一个jackson的对象映射器，用来解析数据
          ObjectMapper mapper = new ObjectMapper();
          //创建一个对象
          User user = new User("秦疆1号", 3, "男");
          //将我们的对象解析成为json格式
          String str = mapper.writeValueAsString(user);
          //由于@ResponseBody注解，这里会将str转成json格式返回；十分方便
          return str;
     }
   
   }
   ```

### 输出Json集合

```java
@RequestMapping("/json2")
public String json2() throws JsonProcessingException {

   //创建一个jackson的对象映射器，用来解析数据
   ObjectMapper mapper = new ObjectMapper();
   //创建一个对象
   User user1 = new User("秦疆1号", 3, "男");
   User user2 = new User("秦疆2号", 3, "男");
   User user3 = new User("秦疆3号", 3, "男");
   User user4 = new User("秦疆4号", 3, "男");
   List<User> list = new ArrayList<User>();
   list.add(user1);
   list.add(user2);
   list.add(user3);
   list.add(user4);

   //将我们的对象解析成为json格式
   String str = mapper.writeValueAsString(list);
   return str;
}
```



### 输出时间对象

```java
@RequestMapping("/json3")
public String json3() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   //创建时间一个对象，java.util.Date
   Date date = new Date();
   //将我们的对象解析成为json格式
   String str = mapper.writeValueAsString(date);
   return str;
}
```

- 默认日期格式会变成一个数字，是1970年1月1日到当前日期的毫秒数！
- Jackson 默认是会把时间转成timestamps形式

### **解决方案：取消timestamps形式 ， 自定义时间格式**

```java
@RequestMapping("/json4")
public String json4() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   //不使用时间戳的方式
   mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
   //自定义日期格式对象
   SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
   //指定日期格式
   mapper.setDateFormat(sdf);

   Date date = new Date();
   String str = mapper.writeValueAsString(date);

   return str;
}
```

### 抽取为工具类

**如果要经常使用的话，这样是比较麻烦的，我们可以将这些代码封装到一个工具类中；我们去编写下**

```java
public class JsonUtils {

    public static String getJson(Object object){
        return getJson(object, "yyyy-MM-dd HH:mm:ss");
    }

    public static String getJson(Object object, String dateFormat){
        ObjectMapper mapper = new ObjectMapper();
        //不使用时间戳的方式, 默认将日期转换成时间戳, 改变默认为false
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
        //自定义日期格式
        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
        mapper.setDateFormat(sdf);

        try {
            return mapper.writeValueAsString(object);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

我们使用工具类，代码就更加简洁了！

```java
@RequestMapping("/json5")
public String json5() throws JsonProcessingException {
   Date date = new Date();
   String json = JsonUtils.getJson(date);
   return json;
}
```

## FastJson

fastjson.jar是阿里开发的一款专门用于Java开发的包，可以方便的实现json对象与JavaBean对象的转换，实现JavaBean对象与json字符串的转换，实现json对象与json字符串的转换。实现json的转换方法很多，最后的实现结果都是一样的。

### fastjson 的 pom依赖！

```
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>fastjson</artifactId>
   <version>1.2.60</version>
</dependency>
```

### fastjson 三个主要的类：

**JSONObject  代表 json 对象** 

- JSONObject实现了Map接口, 猜想 JSONObject底层操作是由Map实现的。
- JSONObject对应json对象，通过各种形式的get()方法可以获取json对象中的数据，也可利用诸如size()，isEmpty()等方法获取"键：值"对的个数和判断是否为空。其本质是通过实现Map接口并调用接口中的方法完成的。

**JSONArray  代表 json 对象数组**

- 内部是有List接口中的方法来完成操作的。

**JSON代表 JSONObject和JSONArray的转化**

- JSON类源码分析与使用
- 仔细观察这些方法，主要是实现json对象，json对象数组，javabean对象，json字符串之间的相互转化。



```java
package com.kuang.controller;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.kuang.pojo.User;

import java.util.ArrayList;
import java.util.List;

public class FastJsonDemo {
   public static void main(String[] args) {
       //创建一个对象
       User user1 = new User("秦疆1号", 3, "男");
       User user2 = new User("秦疆2号", 3, "男");
       User user3 = new User("秦疆3号", 3, "男");
       User user4 = new User("秦疆4号", 3, "男");
       List<User> list = new ArrayList<User>();
       list.add(user1);
       list.add(user2);
       list.add(user3);
       list.add(user4);

       System.out.println("*******Java对象 转 JSON字符串*******");
       String str1 = JSON.toJSONString(list);
       System.out.println("JSON.toJSONString(list)==>"+str1);
       String str2 = JSON.toJSONString(user1);
       System.out.println("JSON.toJSONString(user1)==>"+str2);

       System.out.println("\n****** JSON字符串 转 Java对象*******");
       User jp_user1=JSON.parseObject(str2,User.class);
       System.out.println("JSON.parseObject(str2,User.class)==>"+jp_user1);

       System.out.println("\n****** Java对象 转 JSON对象 ******");
       JSONObject jsonObject1 = (JSONObject) JSON.toJSON(user2);
       System.out.println("(JSONObject) JSON.toJSON(user2)==>"+jsonObject1.getString("name"));

       System.out.println("\n****** JSON对象 转 Java对象 ******");
       User to_java_user = JSON.toJavaObject(jsonObject1, User.class);
       System.out.println("JSON.toJavaObject(jsonObject1, User.class)==>"+to_java_user);
  }
}
```



# 整合SSM

## 数据库表

创建一个存放书籍数据的数据库表

```sql
CREATE DATABASE `ssmbuild`;

USE `ssmbuild`;

DROP TABLE IF EXISTS `books`;

CREATE TABLE `books` (
`bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
`bookName` VARCHAR(100) NOT NULL COMMENT '书名',
`bookCounts` INT(11) NOT NULL COMMENT '数量',
`detail` VARCHAR(200) NOT NULL COMMENT '描述',
KEY `bookID` (`bookID`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT  INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`)VALUES
(1,'Java',1,'从入门到放弃'),
(2,'MySQL',10,'从删库到跑路'),
(3,'Linux',5,'从进门到进牢');
```

## 基本环境搭建

### 新建一Maven项目！ssmbuild ， 添加web的支持

### 导入相关的pom依赖！

```xml
<properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <encoding>UTF-8</encoding>
    </properties>
    <!--依赖: junit 数据库驱动 连接池 servlet jsp mybatis mybatis-spring spring-->
    <dependencies>
        <!--Junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <!--数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>
        <!-- 数据库连接池 -->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>

        <!--Servlet - JSP -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <!--Mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.2</version>
        </dependency>

        <!--Spring-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.20</version>
        </dependency>
    </dependencies>
```

### Maven资源过滤设置

```xml
 <!--静态资源导出-->
    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>
```

### 建立基本结构和配置框架！

1. ![image-20210526012109011](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210526012109011.png)

2. mybatis-config.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
   </configuration>
   ```

3. applicationContext.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
   </beans>
   ```

## Mybatis层编写

### 数据库配置文件 **database.properties**

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?serverTimezone=UTC&useSSL=true&useUnicode=true&characterEncoding=UTF-8
jdbc.username=root
jdbc.password=123
```

### IDEA关联数据库

1. ![image-20210526012355365](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210526012355365.png)

### 编写MyBatis的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置数据源, 交给spring去做-->  
    <!--配置实体类的别名为类名第一个字母小写-->
    <typeAliases>
        <package name="com.kuang.pojo"/>
    </typeAliases>
    <!--绑定Mapper接口-->
    <mappers>
        <mapper resource="com/kuang/dao/BookMapper.xml"/>
    </mappers>
</configuration>
```

### 编写数据库对应的实体类 com.kuang.pojo.Books

```java
package com.kuang.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
//使用lombok插件！
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Books {
   
   private int bookID;
   private String bookName;
   private int bookCounts;
   private String detail;
   
}
```

### 编写Dao层的 Mapper接口！

```java
public interface BookMapper {
    //增加一本书
    int addBook(Books books);
    //删除一本书
    int deleteBookById(@Param("bookId") int id);
    //更新一本书
    int updateBook(Books books);
    //查询一本书
    Book queryBookById(@Param("bookId") int id);
    //查询所有的书
    List<Books> queryAllBooks();
}
```

### 编写接口对应的 Mapper.xml 文件。需要导入MyBatis的包；

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.dao.BookMapper">

    <!--add-->
    <select id="addBook" parameterType="books">
        insert into ssmbuild.books(bookName, bookCounts, detail)
        values(#{bookName}, #{bookCounts},#{detail})
    </select>

    <!--delete-->
    <delete id="deleteBookById" parameterType="_int">
        delete from ssmbuild.books where bookID=#{bookId};
    </delete>

    <!--update-->
    <update id="updateBook" parameterType="books">
        update ssmbuild.books
        set bookName=#{bookName}, bookCounts=#{bookCounts}, detail=#{detail}
        where bookID=#{bookID};
    </update>

    <!--select-->
    <select id="queryBookById" resultType="books">
        select * from ssmbuild.books
        where bookID=#{bookId};
    </select>

    <!--selectAll-->
    <select id="queryAllBooks" resultType="books">
        select * from ssmbuild.books;
    </select>
</mapper>
```

### 编写Service层的接口和实现类

1. 接口

   ```java
   public interface BookService {
       //增加一本书
       int addBook(Books books);
       //删除一本书
       int deleteBookById( int id);
       //更新一本书
       int updateBook(Books books);
       //查询一本书
       Book queryBookById( int id);
       //查询所有的书
       List<Books> queryAllBooks();
   }
   ```

2. 实现类

   ```java
   public class BookServiceImpl implements BookService {
       //service层调用Dao层, 组合Dao
       private BookMapper bookMapper;
       public void setBookMapper(BookMapper bookMapper) {
           this.bookMapper = bookMapper;
       }
       public int addBook(Books books) {
           return bookMapper.addBook(books);
       }
       public int deleteBookById(int id) {
           return bookMapper.deleteBookById(id);
       }
       public int updateBook(Books books) {
           return bookMapper.updateBook(books);
       }
       public Book queryBookById(int id) {
           return bookMapper.queryBookById(id);
       }
       public List<Books> queryAllBooks() {
           return bookMapper.queryAllBooks();
       }
   }
   ```

### **OK，到此，底层需求操作编写完毕！**

## Spring层

### 配置**Spring整合MyBatis**，我们这里数据源使用c3p0连接池；

### 我们去编写Spring整合Mybatis的相关的配置文件；spring-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--关联数据库配置文件-->
    <context:property-placeholder location="classpath:database.properties"/>

    <!--2.连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>

        <!-- c3p0连接池的私有属性 -->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!-- 关闭连接后不自动commit -->
        <property name="autoCommitOnClose" value="false"/>
        <!-- 获取连接超时时间 -->
        <property name="checkoutTimeout" value="10000"/>
        <!-- 当获取连接失败重试次数 -->
        <property name="acquireRetryAttempts" value="2"/>
    </bean>

    <!--3.sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--绑定Mybatis的配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>

    <!--4.配置dao接口扫描包, 动态的实现了Dao接口可以注入到Spring容器中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
<!--        注入sqlSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
<!--        要扫描的包-->
        <property name="basePackage" value="com.kuang.dao"/>
    </bean>
</beans>
```

### **Spring整合service层**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--1.扫描service下的包-->
    <context:component-scan base-package="com.kuang.service"/>
    <!--2.将所有的业务类注入到spring, 可以通过配置 或者注解实现-->
    <bean id="BookServiceImpl" class="com.kuang.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>
    <!--3.声明式事务配置-->
    <bean id="transcationManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--4.aop事务支持-->
</beans>
```

### SpringMVC层

#### **web.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <!--乱码过滤-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
</web-app>
```

#### **spring-mvc.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!--1.注解驱动-->
    <mvc:annotation-driven/>
    <!--2.静态资源过滤-->
    <mvc:default-servlet-handler/>
    <!--3.扫描包:controller-->
    <context:component-scan base-package="com.kuang.controller"/>
    <!--4.视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

#### **Spring配置整合文件，applicationContext.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="classpath:spring-service.xml"/>
    <import resource="classpath:spring-dao.xml"/>
    <import resource="classpath:spring-mvc.xml"/>
</beans>
```

## **Controller 和 视图层编写**

### BookController 类编写 ， 

```java
@Controller
@RequestMapping("/book")
public class BookController {
    //controller 调 service层
    @Autowired
    @Qualifier("BookServiceImpl")
    public BookService bookService;
    //==============================================查询==============================================
    @RequestMapping("/allBook")
    public String list(Model model) {
        List<Books> list = bookService.queryAllBooks();
        model.addAttribute("list", list);
        return "allBook";
    }

    //==============================================添加==============================================
    //跳转到增加书籍页面
    @RequestMapping("/toAddBook")
    public String toAddBookPage(){
        return "addBook";
    }

    //添加书籍的请求
    @RequestMapping("/addBook")
    public String addBook(Books books){
        System.out.println("addBook=>"+books);
        bookService.addBook(books);
        return "redirect:/book/allBook"; //重定向到我们的@RequestMapping("/allBook")的请求
    }

    //==============================================修改==============================================
    //跳转到修改书籍页面
    @RequestMapping("/toUpdateBook")
    public String toUpdatePage(int id, Model model){
        Books books = bookService.queryBookById(id);
        model.addAttribute("QBook", books);
        return "updateBook";
    }

    //修改书籍
    @RequestMapping("/updateBook")
    public String updateBook(Books books){
        System.out.println("updateBook==>"+books);
        bookService.updateBook(books);
        return "redirect:/book/allBook";
    }

    //==============================================删除==============================================
    //删除书籍
    @RequestMapping("/deleteBook/{bookId}")
    public String deleteBook(@PathVariable("bookId") int id){
        bookService.deleteBookById(id);
        return "redirect:/book/allBook";
    }

    //==============================================查询==============================================
    @RequestMapping("/queryBook")
    public String queryBook(String queryBookName, Model model){
        Books books = bookService.queryBookByName(queryBookName);
        if(books==null){
            model.addAttribute("error1", "Book \""+queryBookName+"\" Not Found");
        }
        System.err.println("books=>"+books);
        ArrayList<Books> list = new ArrayList<>();
        list.add(books);
        model.addAttribute("list", list);
        return "allBook";
    }
    //==============================================模糊查询==============================================
    @RequestMapping("/queryBookLike")
    public String queryBookLike(String queryBookLike, Model model){
        List<Books> list = bookService.queryBookLike(queryBookLike);
        if(list==null || list.size()==0){
            model.addAttribute("error2", "Book Like \""+queryBookLike+"\" Not Found");
        }
        model.addAttribute("list", list);
        return "allBook";

    }

}
```

### 编写首页 **index.jsp**

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE HTML>
<html>
<head>
  <title>首页</title>
  <style type="text/css">
    a {
      text-decoration: none;
      color: cadetblue;
      font-size: 18px;
    }
    h3 {
      width: 250px;
      height: 38px;
      margin: 100px auto;
      text-align: center;
      line-height: 38px;
      background: lightblue;
      border-radius: 4px;
    }
  </style>
</head>
<body>

<h3>
  <a href="${pageContext.request.contextPath}/book/allBook">Click to Enter Book Page</a>
</h3>
</body>
</html>
```

### 书籍列表页面 **allbook.jsp**

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%--
  Created by IntelliJ IDEA.
  User: Chaoq
  Date: 2021/5/26
  Time: 1:38
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title >AllBooks</title>
    <!--BootStrap cdn库-->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <link rel="icon" type="image/png" sizes="144x144" href="/img/kobe_face.JPG"/>
</head>
<body>

<div class="container" >
    <div class="row clearfix">
        <div class="col-md-12 column" >
            <div class="page-header">
                <h1>
                    <small>Book List</small>
                </h1>
            </div>
        </div>

        <div class="row">



            <%--添加--%>
            <div class="col-md-4 column">
                <%--toAddBook--%>
                <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">Add New Book</a>
                <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/allBook">Show All Books</a>
            </div>


            <%--查询--%>
            <div class="col-md-8 column">
                <%--SearchBook--%>

                    <%--根据书名查询查询--%>
                <form class="form-inline" action="${pageContext.request.contextPath}/book/queryBook" method="post" style="float:right">
                    <%--错误信息--%>
                    <span style="color:firebrick; font-weight: bold">${error1}</span>
                    <input type="text" name="queryBookName" class="form-control" placeholder="Enter the book name you want to search">
                    <input type="submit" value="SearchByName" class="btn btn-primary">
                </form>
                    <%--根据书名模糊查询--%>
                <form class="form-inline" action="${pageContext.request.contextPath}/book/queryBookLike" method="post" style="float:right">
                    <!--错误信息-->
                    <span style="color:firebrick; font-weight: bold">${error2}</span>
                    <input type="text" name="queryBookLike" class="form-control" placeholder="Enter the book name like you want to search">
                    <input type="submit" value="SearchByNameLike" class="btn btn-primary">
                </form>
            </div>

        </div>
    </div>
    <div class="row clearfix">
        <div class="col-md-12 column">
            <table class="table table-hover table-striped">
                <thead>
                    <tr>
                        <th>BookId</th>
                        <th>BookName</th>
                        <th>BookCounts</th>
                        <th>BookDetails</th>
                        <th>Modification</th>
                    </tr>
                </thead>

                <%--书籍从数据库中查询出来, 从这个list中遍历出来: foreach--%>
                <tbody>
                    <c:forEach var="book" items="${list}">
                        <tr>
                            <td>${book.bookID}</td>
                            <td>${book.bookName}</td>
                            <td>${book.bookCounts}</td>
                            <td>${book.detail}</td>
                            <td>

                                <a href="${pageContext.request.contextPath}/book/toUpdateBook?id=${book.bookID}">Modify</a>
                                &nbsp; | &nbsp;
                                <a href="${pageContext.request.contextPath}/book/deleteBook/${book.bookID}">Delete</a>
                            </td>
                        </tr>
                    </c:forEach>
                </tbody>
            </table>
        </div>
    </div>
</div>



</body>
</html>

```

### 添加书籍页面：**addBook.jsp**

```jsp
<%--
  Created by IntelliJ IDEA.
  User: Chaoq
  Date: 2021/5/26
  Time: 10:50
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>AddBook</title>
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

<div class="container" >
    <div class="row clearfix">
        <div class="col-md-12 column" >
            <div class="page-header">
                <h1>
                    <small>Add New Book</small>
                </h1>
            </div>
        </div>
    </div>

    <form action="${pageContext.request.contextPath}/book/addBook" method="post">
        <div class="form-group">
            <label>Book Name</label>
            <input type="text" name="bookName" class="form-control"  required>
        </div>
        <div class="form-group">
            <label>Book Count</label>
            <input type="text" name="bookCounts" class="form-control" required>
        </div>
        <div class="form-group">
            <label>Book Detail</label>
            <input type="text" name="detail" class="form-control" required>
        </div>
        <div class="form-group">
            <input type="submit" class="form-control" value="addBook" >
        </div>
    </form>
</div>
</body>
</html>

```

### 修改书籍页面  **updateBook.jsp**

```jsp
<%--
  Created by IntelliJ IDEA.
  User: Chaoq
  Date: 2021/5/26
  Time: 11:26
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>UpdateBook</title>
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

<div class="container" >
    <div class="row clearfix">
        <div class="col-md-12 column" >
            <div class="page-header">
                <h1>
                    <small>Update Book</small>
                </h1>
            </div>
        </div>
    </div>

    <form action="${pageContext.request.contextPath}/book/updateBook" method="post">
<%--        出现的问题, 我们提交了修改的SQL请求, 但是修改失败, 初次考虑事物的问题, 配置完事务, 依旧失败--%>
<%--        看一下SQL语句, 能否执行成功 SQL执行失败, 修改未完成--%>
<%--        前端传递隐藏域--%>
        <input type="hidden" name="bookID" value="${QBook.bookID}">
        <div class="form-group">
            <label>Book Name</label>
            <input type="text" name="bookName" value="${QBook.bookName}" class="form-control"  required>
        </div>
        <div class="form-group">
            <label>Book Count</label>
            <input type="text" name="bookCounts" value="${QBook.bookCounts}" class="form-control" required>
        </div>
        <div class="form-group">
            <label>Book Detail</label>
            <input type="text" name="detail" value="${QBook.detail}" class="form-control" required>
        </div>
        <div class="form-group">
            <input type="submit" class="form-control" value="updateBook" >
        </div>
    </form>
</div>
</body>
</html>

```



## **配置Tomcat，进行运行！**

![image-20210526145106126](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210526145106126.png)



## 项目结构

![image-20210526145211307](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210526145211307.png)

这个是同学们的第一个SSM整合案例，一定要烂熟于心！

SSM框架的重要程度是不言而喻的，学到这里，大家已经可以进行基本网站的单独开发。但是这只是增删改查的基本操作。可以说学到这里，大家才算是真正的步入了后台开发的门。也就是能找一个后台相关工作的底线。



# Ajax

## 简介

- **AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。**
- AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
- **Ajax 不是一种新的编程语言，而是一种用于创建更好更快以及交互性更强的Web应用程序的技术。**
- 在 2005 年，Google 通过其 Google Suggest 使 AJAX 变得流行起来。Google Suggest能够自动帮你完成搜索单词。
- Google Suggest 使用 AJAX 创造出动态性极强的 web 界面：当您在谷歌的搜索框输入关键字时，JavaScript 会把这些字符发送到服务器，然后服务器会返回一个搜索建议的列表。
- 就和国内百度的搜索框一样!

- 传统的网页(即不用ajax技术的网页)，想要更新内容或者提交一个表单，都需要重新加载整个网页。
- 使用ajax技术的网页，通过在后台服务器进行少量的数据交换，就可以实现异步局部更新。
- 使用Ajax，用户可以创建接近本地桌面应用的直接、高可用、更丰富、更动态的Web用户界面。



## 伪造Ajax

我们可以使用前端的一个标签来伪造一个ajax的样子。iframe标签

1、新建一个module ：sspringmvc-06-ajax ， 导入web支持！

2、编写一个 ajax-frame.html 使用 iframe 测试，感受下效果

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iframe测试体验页面无刷新</title>
    <script>
        function go(){
            var url = document.getElementById("url").value;
            document.getElementById("iframe1").src = url;
        }
    </script>
</head>
<body>

<div>
    <p>Please enter url</p>
    <p>
        <input type="text" id="url" value="https://www.layui.com/">
        <input type="button" value="submit" onclick="go()">
    </p>
</div>
    
<div>
    <iframe id="iframe1" frameborder="0" style="width:100%; height: 500px" ></iframe>
</div>

</body>
</html>
```

使用IDEA开浏览器测试一下！

## **利用AJAX可以做：**

- 注册时，输入用户名自动检测用户是否已经存在。
- 登陆时，提示用户名密码错误
- 删除数据行时，将行ID发送到后台，后台在数据库中删除，数据库删除成功后，在页面DOM中将数据行也删除。



## jQuery.ajax

- 纯JS原生实现Ajax我们不去讲解这里，直接使用jquery提供的，方便学习和使用，避免重复造轮子，有兴趣的同学可以去了解下JS原生XMLHttpRequest ！
- Ajax的核心是XMLHttpRequest对象(XHR)。XHR为向服务器发送请求和解析服务器响应提供了接口。能够以异步方式从服务器获取新数据。
- jQuery 提供多个与 AJAX 有关的方法。
- 通过 jQuery AJAX 方法，您能够使用 HTTP Get 和 HTTP Post 从远程服务器上请求文本、HTML、XML 或 JSON – 同时您能够把这些外部数据直接载入网页的被选元素中。
- jQuery 不是生产者，而是大自然搬运工。
- jQuery Ajax本质就是 XMLHttpRequest，对他进行了封装，方便调用！

```
jQuery.ajax(...)
      部分参数：
            url：请求地址
            type：请求方式，GET、POST（1.9.0之后用method）
        headers：请求头
            data：要发送的数据
    contentType：即将发送信息至服务器的内容编码类型(默认: "application/x-www-form-urlencoded; charset=UTF-8")
          async：是否异步
        timeout：设置请求超时时间（毫秒）
      beforeSend：发送请求前执行的函数(全局)
        complete：完成之后执行的回调函数(全局)
        success：成功之后执行的回调函数(全局)
          error：失败之后执行的回调函数(全局)
        accepts：通过请求头发送给服务器，告诉服务器当前客户端可接受的数据类型
        dataType：将服务器端返回的数据转换成指定类型
          "xml": 将服务器端返回的内容转换成xml格式
          "text": 将服务器端返回的内容转换成普通文本格式
          "html": 将服务器端返回的内容转换成普通文本格式，在插入DOM中时，如果包含JavaScript标签，则会尝试去执行。
        "script": 尝试将返回值当作JavaScript去执行，然后再将服务器端返回的内容转换成普通文本格式
          "json": 将服务器端返回的内容转换成相应的JavaScript对象
        "jsonp": JSONP 格式使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数
```

### **简单的测试，使用最原始的HttpServletResponse处理**

1. 配置web.xml 和 springmvc的配置文件，复制上面案例的即可 【记得静态资源过滤和注解驱动配置上】

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          https://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
      <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
      <context:component-scan base-package="com.kuang.controller"/>
      <mvc:default-servlet-handler />
      <mvc:annotation-driven />
   
      <!-- 视图解析器 -->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
            id="internalResourceViewResolver">
          <!-- 前缀 -->
          <property name="prefix" value="/WEB-INF/jsp/" />
          <!-- 后缀 -->
          <property name="suffix" value=".jsp" />
      </bean>
   
   </beans>
   ```

2. 编写一个AjaxController

   ```java
   @RequestMapping("/a1")
   public void a1(String name, HttpServletResponse response) throws IOException {
       System.out.println("a1:param=>"+name);
       if("kuangshen".equals(name)){
           response.getWriter().write("true");
       }else{
           response.getWriter().write("false");
       }
   }
   ```

3. 导入jquery ， 可以使用在线的CDN ， 也可以下载导入

   ```xml
   <script src="https://code.jquery.com/jquery-3.1.1.min.js"></script>
   <script src="${pageContext.request.contextPath}/statics/js/jquery-3.1.1.min.js"></script>
   ```

   

4. 编写index.jsp测试

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
     <head>
       <title>$Title$</title>
       <script src="${pageContext.request.contextPath}/static/js/jquery-3.6.0.js"></script>
       <script>
   
         function a(){
           $.post({
             url:"${pageContext.request.contextPath}/a1",
             data:{'name':$("#username").val()},
             success:function(data,status){
               console.log("data="+data);
               console.log("status="+status);
             }
           })
         }
       </script>
   
     </head>
     <body>
     <!--失去焦点的时候,发起一个请求(携带信息)到后台-->
     username:<input type="text" id="username" onblur="a()">
   
     </body>
   </html>
   ```

5. 启动tomcat测试！打开浏览器的控制台，当我们鼠标离开输入框的时候，可以看到发出了一个ajax的请求！是后台返回给我们的结果！测试成功！



## **Springmvc实现**

### 实体类user

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {

   private String name;
   private int age;
   private String sex;

}
```

### 我们来获取一个集合对象，展示到前端页面

```java
@RequestMapping("/a2")
public List<User> ajax2(){
   List<User> list = new ArrayList<User>();
   list.add(new User("秦疆1号",3,"男"));
   list.add(new User("秦疆2号",3,"男"));
   list.add(new User("秦疆3号",3,"男"));
   return list; //由于@RestController注解，将list转成json格式返回
}
```

### 前端页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script src="${pageContext.request.contextPath}/static/js/jquery-3.6.0.js"></script>
    <script>
        $(function(){
            $("#btn").click(function(){
                /*
                $.post(url, param[option] success)
                 */
                $.post("${pageContext.request.contextPath}/a2", function(data){
                    // console.log(data);
                    var html = "";
                    for(let i=0; i<data.length; i++){
                        html += "<tr>"+
                            "<td>" + data[i].name +"</td>"+
                            "<td>" + data[i].age +"</td>"+
                            "<td>" + data[i].sex +"</td>"+
                            "</tr>"
                    }
                    $("#content").html(html);
                })
            })
        })

    </script>
</head>
<body>
<input type="button" value="load data" id="btn">
<table>
    <tr>
        <td>name</td>
        <td>age</td>
        <td>sex</td>
    </tr>
    <tbody id="content">
    </tbody>
</table>

</body>
</html>
```

**成功实现了数据回显！可以体会一下Ajax的好处！**



## 注册提示效果

我们再测试一个小Demo，思考一下我们平时注册时候，输入框后面的实时提示怎么做到的；如何优化

### 我们写一个Controller

```java
@RequestMapping("/a3")
public String ajax3(String name,String pwd){
   String msg = "";
   //模拟数据库中存在数据
   if (name!=null){
       if ("admin".equals(name)){
           msg = "OK";
      }else {
           msg = "用户名输入错误";
      }
  }
   if (pwd!=null){
       if ("123456".equals(pwd)){
           msg = "OK";
      }else {
           msg = "密码输入有误";
      }
  }
   return msg; //由于@RestController注解，将msg转成json格式返回
}
```

### 前端页面 login.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>ajax</title>
   <script src="${pageContext.request.contextPath}/statics/js/jquery-3.1.1.min.js"></script>
   <script>

       function a1(){
           $.post({
               url:"${pageContext.request.contextPath}/a3",
               data:{'name':$("#name").val()},
               success:function (data) {
                   if (data.toString()=='OK'){
                       $("#userInfo").css("color","green");
                  }else {
                       $("#userInfo").css("color","red");
                  }
                   $("#userInfo").html(data);
              }
          });
      }
       function a2(){
           $.post({
               url:"${pageContext.request.contextPath}/a3",
               data:{'pwd':$("#pwd").val()},
               success:function (data) {
                   if (data.toString()=='OK'){
                       $("#pwdInfo").css("color","green");
                  }else {
                       $("#pwdInfo").css("color","red");
                  }
                   $("#pwdInfo").html(data);
              }
          });
      }

   </script>
</head>
<body>
<p>
  用户名:<input type="text" id="name" onblur="a1()"/>
   <span id="userInfo"></span>
</p>
<p>
  密码:<input type="text" id="pwd" onblur="a2()"/>
   <span id="pwdInfo"></span>
</p>
</body>
</html>
```

【记得处理json乱码问题】

```xml
<!-- JSON统一解决乱码问题 放在applicationContext.xml-->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="UTF-8"/>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"/>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

![image-20210526170948229](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210526170948229.png)







# 拦截器



## 概述

SpringMVC的处理器拦截器类似于Servlet开发中的过滤器Filter,用于对处理器进行预处理和后处理。开发者可以自己定义一些拦截器来实现特定的功能。

**过滤器与拦截器的区别：**拦截器是AOP思想的具体应用。

**过滤器**

- servlet规范中的一部分，任何java web工程都可以使用
- 在url-pattern中配置了/*之后，可以对所有要访问的资源进行拦截

**拦截器** 

- 拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用
- 拦截器只会拦截访问的控制器方法， 如果访问的是jsp/html/css/image/js是不会进行拦截的

## 自定义拦截器

那如何实现拦截器呢？

想要自定义拦截器，必须实现 HandlerInterceptor 接口。

### 新建一个Moudule ， springmvc-07-Interceptor  ， 添加web支持

### 配置web.xml 和 springmvc-servlet.xml 文件

### 编写一个拦截器

```java
public class MyInterceptor implements HandlerInterceptor {

   //在请求处理的方法之前执行
   //如果返回true执行下一个拦截器
   //如果返回false就不执行下一个拦截器
   public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
       System.out.println("------------处理前------------");
       return true;
  }

   //在请求处理方法执行之后执行
   public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
       System.out.println("------------处理后------------");
  }

   //在dispatcherServlet处理后执行,做清理工作.
   public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
       System.out.println("------------清理------------");
  }
}
```



### 在springmvc的配置文件中配置拦截器

```xml
<!--关于拦截器的配置-->
<mvc:interceptors>
   <mvc:interceptor>
       <!--/** 包括路径及其子路径-->
       <!--/admin/* 拦截的是/admin/add等等这种 , /admin/add/user不会被拦截-->
       <!--/admin/** 拦截的是/admin/下的所有-->
       <mvc:mapping path="/**"/>
       <!--bean配置的就是拦截器-->
       <bean class="com.kuang.interceptor.MyInterceptor"/>
   </mvc:interceptor>
</mvc:interceptors>
```

### 编写一个Controller，接收请求

```java
//测试拦截器的控制器
@Controller
public class InterceptorController {

   @RequestMapping("/interceptor")
   @ResponseBody
   public String testFunction() {
       System.out.println("控制器中的方法执行了");
       return "hello";
  }
}
```

### 前端 index.jsp

```jsp
<a href="${pageContext.request.contextPath}/interceptor">拦截器测试</a>
```

### 启动tomcat 测试一下！



## 验证用户是否登录 (认证用户)

### **实现思路**

1. 有一个登陆页面，需要写一个controller访问页面。
2. 登陆页面有一提交表单的动作。需要在controller中处理。判断用户名密码是否正确。如果正确，向session中写入用户信息。*返回登陆成功。*
3. 拦截用户请求，判断用户是否登陆。如果用户已经登陆。放行， 如果用户未登陆，跳转到登陆页面

### **测试：**

#### 编写一个登陆页面  login.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>Title</title>
</head>
<h1>登录页面</h1>
<hr>
<body>
<form action="${pageContext.request.contextPath}/user/login">
  用户名：<input type="text" name="username"> <br>
  密码：<input type="password" name="pwd"> <br>
   <input type="submit" value="提交">
</form>
</body>
</html>
```

#### 编写一个Controller处理请求

```java
@Controller
@RequestMapping("/user")
public class LoginController {
    @RequestMapping("/main")
    public String main(){
        return "main";
    }
    @RequestMapping("/goLogin")
    public String login(){
        return "login";
    }
    @RequestMapping("/login")
    public String login(HttpSession session, String username, String password, Model model){
        //把用户的信息的存在session中
        session.setAttribute("userLoginInfo", username);
        model.addAttribute("username", username);
        return "main";
    }
    @RequestMapping("/goOut")
    public String goOut(HttpSession session){
        //移除结点
        session.removeAttribute("userLoginInfo");
        return "main";
    }
}
```

#### 编写一个登陆成功的页面 success.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>Title</title>
</head>
<body>
<h1>登录成功页面</h1>
<hr>
${user}
<a href="${pageContext.request.contextPath}/user/logout">注销</a>
</body>
</html>
```

#### 在 index 页面上测试跳转！启动Tomcat 测试，未登录也可以进入主页！

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
  <h1>
    <a href="${pageContext.request.contextPath}/user/goLogin">Log In</a>
  </h1>
  <h1>
    <a href="${pageContext.request.contextPath}/user/main">Main Page</a>
  </h1>
  </body>
</html>
```

#### 编写用户登录拦截器

```java
public class LoginInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();

        //去登录页面可以放行
        if(request.getRequestURI().contains("goLogin")){
            return true;
        }
        //在登录页面可以放行
        if(request.getRequestURI().contains("login")){
            return true;
        }
        //有session 登录过, 放行
        if(session.getAttribute("userLoginInfo")!=null){
            return true;
        }
        //其他情况, 不放行, 跳转取登录页面
        request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request, response);
        return false;
    }
}
```



#### 在Springmvc的配置文件中注册拦截器

```xml
<!--关于拦截器的配置-->
<mvc:interceptors>
   <mvc:interceptor>
       <mvc:mapping path="/**"/>
       <bean id="loginInterceptor" class="com.kuang.interceptor.LoginInterceptor"/>
   </mvc:interceptor>
</mvc:interceptors>
```



# 文件上传和下载



> 准备工作

文件上传是项目开发中最常见的功能之一 ,springMVC 可以很好的支持文件上传，但是SpringMVC上下文中默认没有装配MultipartResolver，因此默认情况下其不能处理文件上传工作。如果想使用Spring的文件上传功能，则需要在上下文中配置MultipartResolver。

前端表单要求：为了能上传文件，必须将表单的method设置为POST，并将enctype设置为multipart/form-data。只有在这样的情况下，浏览器才会把用户选择的文件以二进制数据发送给服务器；

**对表单中的 enctype 属性做个详细的说明：**

- application/x-www=form-urlencoded：默认方式，只处理表单域中的 value 属性值，采用这种编码方式的表单会将表单域中的值处理成 URL 编码方式。
- multipart/form-data：这种编码方式会以二进制流的方式来处理表单数据，这种编码方式会把文件域指定文件的内容也封装到请求参数中，不会对字符编码。
- text/plain：除了把空格转换为 "+" 号外，其他字符都不做编码处理，这种方式适用直接通过表单发送邮件。

```
<form action="" enctype="multipart/form-data" method="post">
   <input type="file" name="file"/>
   <input type="submit">
</form>
```

一旦设置了enctype为multipart/form-data，浏览器即会采用二进制流的方式来处理表单数据，而对于文件上传的处理则涉及在服务器端解析原始的HTTP响应。在2003年，Apache Software Foundation发布了开源的Commons FileUpload组件，其很快成为Servlet/JSP程序员上传文件的最佳选择。

- Servlet3.0规范已经提供方法来处理文件上传，但这种上传需要在Servlet中完成。
- 而Spring MVC则提供了更简单的封装。
- Spring MVC为文件上传提供了直接的支持，这种支持是用即插即用的MultipartResolver实现的。
- Spring MVC使用Apache Commons FileUpload技术实现了一个MultipartResolver实现类：
- CommonsMultipartResolver。因此，SpringMVC的文件上传还需要依赖Apache Commons FileUpload的组件。



> 文件上传

1、导入文件上传的jar包，commons-fileupload ， Maven会自动帮我们导入他的依赖包 commons-io包；

```
<!--文件上传-->
<dependency>
   <groupId>commons-fileupload</groupId>
   <artifactId>commons-fileupload</artifactId>
   <version>1.3.3</version>
</dependency>
<!--servlet-api导入高版本的-->
<dependency>
   <groupId>javax.servlet</groupId>
   <artifactId>javax.servlet-api</artifactId>
   <version>4.0.1</version>
</dependency>
```

2、配置bean：multipartResolver

【**注意！！！这个bena的id必须为：multipartResolver ， 否则上传文件会报400的错误！在这里栽过坑,教训！**】

```
<!--文件上传配置-->
<bean id="multipartResolver"  class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
   <!-- 请求的编码格式，必须和jSP的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1 -->
   <property name="defaultEncoding" value="utf-8"/>
   <!-- 上传文件大小上限，单位为字节（10485760=10M） -->
   <property name="maxUploadSize" value="10485760"/>
   <property name="maxInMemorySize" value="40960"/>
</bean>
```

CommonsMultipartFile 的 常用方法：

- **String getOriginalFilename()：获取上传文件的原名**
- **InputStream getInputStream()：获取文件流**
- **void transferTo(File dest)：将上传文件保存到一个目录文件中**

 我们去实际测试一下

3、编写前端页面

```
<form action="/upload" enctype="multipart/form-data" method="post">
 <input type="file" name="file"/>
 <input type="submit" value="upload">
</form>
```

4、**Controller**

```
package com.kuang.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.commons.CommonsMultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.*;

@Controller
public class FileController {
   //@RequestParam("file") 将name=file控件得到的文件封装成CommonsMultipartFile 对象
   //批量上传CommonsMultipartFile则为数组即可
   @RequestMapping("/upload")
   public String fileUpload(@RequestParam("file") CommonsMultipartFile file , HttpServletRequest request) throws IOException {

       //获取文件名 : file.getOriginalFilename();
       String uploadFileName = file.getOriginalFilename();

       //如果文件名为空，直接回到首页！
       if ("".equals(uploadFileName)){
           return "redirect:/index.jsp";
      }
       System.out.println("上传文件名 : "+uploadFileName);

       //上传路径保存设置
       String path = request.getServletContext().getRealPath("/upload");
       //如果路径不存在，创建一个
       File realPath = new File(path);
       if (!realPath.exists()){
           realPath.mkdir();
      }
       System.out.println("上传文件保存地址："+realPath);

       InputStream is = file.getInputStream(); //文件输入流
       OutputStream os = new FileOutputStream(new File(realPath,uploadFileName)); //文件输出流

       //读取写出
       int len=0;
       byte[] buffer = new byte[1024];
       while ((len=is.read(buffer))!=-1){
           os.write(buffer,0,len);
           os.flush();
      }
       os.close();
       is.close();
       return "redirect:/index.jsp";
  }
}
```

5、测试上传文件，OK！



**采用file.Transto 来保存上传的文件**

1、编写Controller

```
/*
* 采用file.Transto 来保存上传的文件
*/
@RequestMapping("/upload2")
public String  fileUpload2(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {

   //上传路径保存设置
   String path = request.getServletContext().getRealPath("/upload");
   File realPath = new File(path);
   if (!realPath.exists()){
       realPath.mkdir();
  }
   //上传文件地址
   System.out.println("上传文件保存地址："+realPath);

   //通过CommonsMultipartFile的方法直接写文件（注意这个时候）
   file.transferTo(new File(realPath +"/"+ file.getOriginalFilename()));

   return "redirect:/index.jsp";
}
```

2、前端表单提交地址修改

3、访问提交测试，OK！



> 文件下载

**文件下载步骤：**

1、设置 response 响应头

2、读取文件 -- InputStream

3、写出文件 -- OutputStream

4、执行操作

5、关闭流 （先开后关）

**代码实现：**

```
@RequestMapping(value="/download")
public String downloads(HttpServletResponse response ,HttpServletRequest request) throws Exception{
   //要下载的图片地址
   String  path = request.getServletContext().getRealPath("/upload");
   String  fileName = "基础语法.jpg";

   //1、设置response 响应头
   response.reset(); //设置页面不缓存,清空buffer
   response.setCharacterEncoding("UTF-8"); //字符编码
   response.setContentType("multipart/form-data"); //二进制传输数据
   //设置响应头
   response.setHeader("Content-Disposition",
           "attachment;fileName="+URLEncoder.encode(fileName, "UTF-8"));

   File file = new File(path,fileName);
   //2、 读取文件--输入流
   InputStream input=new FileInputStream(file);
   //3、 写出文件--输出流
   OutputStream out = response.getOutputStream();

   byte[] buff =new byte[1024];
   int index=0;
   //4、执行 写出操作
   while((index= input.read(buff))!= -1){
       out.write(buff, 0, index);
       out.flush();
  }
   out.close();
   input.close();
   return null;
}
```

前端

```
<a href="/download">点击下载</a>
```

测试，文件下载OK，大家可以和我们之前学习的JavaWeb原生的方式对比一下，就可以知道这个便捷多了!

















































