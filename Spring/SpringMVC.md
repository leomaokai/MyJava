# SpringMVC

MVC是将业务逻辑、数据、显示分离的方法来组织代码

MVC主要作用是降低了视图与1业务逻辑间的双向耦合

MVC不是设计模式，而实一种架构模式

MVC:

* 模型 Modle
  * 数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：数据Dao层和服务Service层，也就是模型提供了模型数据查询和模型数据状态更新等功能，包括数据可业务

* 视图 View
  * 负责进行模型和展示，一般就是我们见到的用户界面，客户想看到的东西

* 控制器 Controller
  * 接收用户请求，委托模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示，也就是说控制器做了一个调度员的工作

典型的MVC就是： JSP + Servlet + JavaBean 模式

# 配置示例

## web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <!--注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--关联一个springmvc配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!-- / 匹配所有的请求: (不包括.jsp)-->
    <!-- /* 匹配所有的请求: (包括.jsp)-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

<!--    配置SpringMVC的乱码过滤-->
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

## springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.kai.controller"/>
    <mvc:default-servlet-handler/>
    <mvc:annotation-driven/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    
    <!--    json乱码-->
    <mvc:annotation-driven>
        <mvc:message-converters>
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

</beans>
```

# 初始SpringMVC

特点：

* 轻量级，简单易学
* 高效，基于请求响应的MVC框架
* 与Spring兼容性好，无缝结合
* 约定优于配置
* 功能强大：RESTful、数据验证、格式化、本地化、主题等
* 简介灵活

## HelloSpringMVC

![image-20210110174236635](SpringMVC.assets/image-20210110174236635.png)

### web.xml

* 配置DispatchServlet
* DispatchServlet要绑定Spring的配置文件(res下创建spring配置文件)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">


    <!--注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--关联一个springmvc配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!-- / 匹配所有的请求: (不包括.jsp)-->
    <!-- /* 匹配所有的请求: (包括.jsp)-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

### springmvc-servlet.xml

* 处理器映射器
* 处理器适配器
* 视图解析器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    <!--视图解析器:DispatcherServlet给他的ModelAndView -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--Handler-->
    <bean id="/hello" class="com.kai.controller.HelloController"/>

</beans>
```

### HelloController.java

```java
public class HelloController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        //ModelAndView 模型和视图
        ModelAndView modelAndView = new ModelAndView();
        //封装对象,放在ModeAndView中
        modelAndView.addObject("msg", "HelloSpringMVC");
        //封装要跳转的视图,放在ModelAndView中
        modelAndView.setViewName("hello");//: /WEB-INF/jsp/hello.jsp
        return modelAndView;
    }
}
```

404报错可能的问题：IDEA项目发布中，没有添加lib依赖

打开项目管理，添加lib目录，依赖全部导入

![image-20210109234618636](SpringMVC.assets/image-20210109234618636.png)

# 注解开发

## springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.kai.controller"/>
    <mvc:default-servlet-handler/>
    <mvc:annotation-driven/>
    
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

```xml
<!--自动扫描包,让指定包下的注解生效,由IOC容器统一管理-->
<context:component-scan base-package="com.kai.controller"/>
<!--让spring MVC不处理静态资源  .css .js .html .mp3 .mp4-->
<mvc:default-servlet-handler/>

<!--
支持mvc注解驱动
    在spring中一般采用@RequestMapping注解来完成映射关系
    要使@RequestMapping注解生效
    必须向上下文中注册DefaultAnnotationHandlerMapping
    和一个AnnotationMethodHandlerAdapter实例
    这两个实例分别在类级别和方法级别处理
    而annotation-driven配置帮助我们自动完成上述两个实例的注入
 -->
<mvc:annotation-driven/>
```
## web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

## HelloController.java

```java
@Controller
public class HelloController {

    @RequestMapping("/h1")
    public String hello(Model model){
        //封装数据
        model.addAttribute("msg","HelloSpring");
        return "hello"; //会被视图解析器处理 WEB-INF/jsp/hello.jsp
    }
}
```

SpringMVC必须配置的三大件:

处理器映射器、处理器适配器、视图解析器

通常只需要手动配置视图解析器，二处理器映射器和处理器适配器只需要开启注解驱动即可

# Controller

* 控制器复杂提供访问应用程序的行为，通常通过接口定义或注解两种方法实现
* 控制器负责解析用户的请求并将其转换为一个模型
* 在Spring MVC中一个控制器类可以包含多个方法
* 在Spring MVC中，对于Controller的配置方式有很多种

## 接口实现

实现接口Controller定义控制器是较老的方法

缺点：一个控制器只能有一个方法，如果要多个方法则需要定义多个Controller；定义的方法比较麻烦

```java
public class ControllerTest1 implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mv=new ModelAndView();
        mv.addObject("msg","ControllerTest1");
        mv.setViewName("test");
        return mv;
    }
```

```xml
<bean name="/t1" class="com.kai.controller.ControllerTest1"/>
```

## 注解实现

@Controller注解类型用于声明Spring类的实例是一个控制器

Spring可以使用扫描机制来找到应用程序中所有基于注解的控制器类，为了保证Spring能找到控制器，需要在配置文件中声明组件扫描

```xml
<context:component-scan base-package="com.kai.controller"/>
```

```java
//@Controller代表这个类被Spring接管
//被注解的类中,如果有方法返回String并且有具体页面跳转
//那么就会被视图器解析
@Controller
public class ControllerTest2 {

    @RequestMapping("/t2")
    public String test2(Model model){
        model.addAttribute("msg","ControllerTest2");
        return "test";
    }

    @RequestMapping("/t3")
    public String test3(Model model){
        model.addAttribute("msg","ControllerTest3");
        return "test";
    }
}

```

![image-20210111163750515](SpringMVC.assets/image-20210111163750515.png)

我们两个请求都指向一个视图，但是页面结果是不一样的，从这里可以看出视图是被复用的，而控制器与视图之间是弱耦合关系

## RequestMapping

@RequestMapping注解用于映射用于映射url到控制器类或一个特定的处理程序方法，可用于类或方法上，用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径

```java
@Controller
@RequestMapping("/hello")
public class ControllerTest3 {

    @RequestMapping("/test")
    public String test4(Model model){
        model.addAttribute("msg","test4");
        return "test";
    }
}
```

![image-20210111164958927](SpringMVC.assets/image-20210111164958927.png)

访问顺序，先访问类的路径，再访问方法的路径

## RestFul风格

RestFul就是一个资源定位及资源操作的风格，不是标准也不是协议，只是一种风格，基于这个风格设计的软件可以更加简洁，更有层次，更易于实现缓存等机制

功能：

* 资源：互联网所有事物都可以被抽象为资源
* 资源操作：使用POST、DELETE、PUT、GET等不同方法对资源进行操作
* 分别对应添加、删除、修改、查询

传统方式操作资源：通过不同的参数来实现不同的效果
```
http://127.0.0.1/item/queryitem?id=1	#查询GET
http://127.0.0.1/item/saveitem		#新增POST
```

使用RestFul操作资源：可以通过不同的请求方式来实现不同的效果

```
http://127.0.0.1/item/1		#查询GET
http://127.0.0.1/item	#新增POST
```

```java
@Controller
public class RestFulController {

    //原来的方式
    //http://localhost:8080/springmvc_04_controller_war_exploded/add?a=1&b=2
    @RequestMapping("/add")
    public String test01(int a, int b, Model model) {
        int res = a + b;
        model.addAttribute("msg", res);
        return "test";
    }

    //RestFul风格
    //http://localhost:8080/springmvc_04_controller_war_exploded/add2/5/6
    @RequestMapping("/add2/{a}/{b}")
    public String test02(@PathVariable int a, @PathVariable int b, Model model) {
        int res = a + b;
        model.addAttribute("msg", res);
        return "test";
    }
    //RestFul风格
    //@RequestMapping(value = "/add3/{a}/{b}",method = RequestMethod.GET)
    @GetMapping("/add3/{a}/{b}")
    public String test03(@PathVariable String a, @PathVariable String b, Model model) {
        String res = a + b;
        model.addAttribute("msg", res);
        return "test";
    }
}
```

![image-20210111173545244](SpringMVC.assets/image-20210111173545244.png)

## 转发和重定向

通过SpringMVC来实现转发和重定向，无需视图解析器

URL不变为转发，URL改变为重定向

```java
@Controller
public class ModeTest1 {

    @RequestMapping("/m1/t1")
    public String test1(HttpServletRequest req, HttpServletResponse resp){
        //使用视图解析器
        HttpSession session = req.getSession();
        System.out.println(session.getId());
        return "test";
        
        //return "/WEB-INF/jsp/test.jsp"
    }
    @RequestMapping("/m1/t2")
    public String test2(Model model){
        //转发,无法直接访问WEB-INF中的jsp,hello.jsp在web目录下
        model.addAttribute("msg","hello");
        return "/hello.jsp";
    }
    @RequestMapping("/m1/t3")
    public String test3(Model model){
        //转发二
        model.addAttribute("msg","hello");
        return "forward:/hello.jsp";
    }
    @RequestMapping("/m1/t4")
    public String test4(Model model){
        //重定向
        model.addAttribute("msg","hello");
        return "redirect:/hello.jsp";
    }
}
```

## 数据处理

```java
@Controller
@RequestMapping("/user")
public class UserController {

    //不加@RequestParam localhost:8080/xxx/user/t1?name=leomaokai
    //加上@RequestParam localhost:8080/xxx/user/t1?username=leomaokai
    @GetMapping("/t1")
    public String test1(@RequestParam("username") String name, Model model){
        //1.接收前端参数
        System.out.println("接收到前端的参数:"+name);
        //2.将返回的结果传递给前端
        model.addAttribute("msg",name);
        //3.跳转视图
        return "test";
    }

    //前端接受User对象: id name age
    // localhost:8080/xxx/user/t2?name=leomaokai&id=10&age=20
    /*
    1.接收前端用户传递的参数,判断参数的名字,如果名字直接在方法上,可以直接使用
    2.假设传递的是一个对象user,匹配User对象中的字段名,如果名字一致,则可匹配
     */
    @GetMapping("/t2")
    public String test2(User user){
        System.out.println(user);
        return "test";
    }

    /*
    ModeMap:继承了LinkedHashMap,拥有LinkedHashMap的全部功能
    Model:精简版
     */
    @GetMapping("/t3")
    public String test3(ModelMap modelMap){
        modelMap.addAttribute("msg","ModelMap");
        return "test";
    }
}
```

## 乱码问题

可以配置SpringMVC的乱码过滤

也可以自己写一个过滤器

注意路径映射要 **/***

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <!--注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--关联一个springmvc配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!-- / 匹配所有的请求: (不包括.jsp)-->
    <!-- /* 匹配所有的请求: (包括.jsp)-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

<!--    配置SpringMVC的乱码过滤-->
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
        <!--注意/*-->
        <url-pattern>/*</url-pattern>
    </filter-mapping>

<!--    <filter>-->
<!--        <filter-name>encoding</filter-name>-->
<!--        <filter-class>com.kai.filter.EncodingFilter</filter-class>-->
<!--    </filter>-->
<!--    <filter-mapping>-->
<!--        <filter-name>encoding</filter-name>-->
<!--        <url-pattern>/*</url-pattern>-->
<!--    </filter-mapping>-->

</web-app>
```

# JSON

## JSON

* JSON(JavaScript Object Notation,JS对象标记)是一种轻量级的数据交换格式，目前使用特别广泛

* 采用完全独立于编程语言的文本格式来储存和表示数据

* 简洁和清晰的层次结构使得JSON成为理想的数据交换语言

* 易于阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率

在JavaScript语言中，一切都是对象，因此，任何JavaScript支持的类型都可以通过JSON来表示，例如字符串、数字、对象、数组等，语法格式：

* 对象表示为键值对，数据用逗号分隔
* 花括号保存对象
* 方括号保存数组

JSON键值对用来保存JavaScript对象的一种方式，和JavaScript对象写法也大同小异，键值对组合中的键名写在前面并用双引号包裹，用冒号分隔，然后紧跟着值

```json
{"name":"leomaokai"}
{"age":"18"}
{"sex":"man"}
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <script type="text/javascript">
        //编写JavaScript对象
        var user = {
            name: "maokai",
            age: 3,
            sex: "man"
        };
        console.log(user);

        //将js对象转换为json对象
        var s = JSON.stringify(user);
        console.log(s);

        //将json对象转换为js对象
        var parse = JSON.parse(s);
        console.log(parse);
    </script>
</head>
<body>

</body>
</html>
```

## Jackson

* Jackson是目前比较好的JSON解析工具
* 还有阿里巴巴的fastjson

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.11.2</version>
</dependency>
```

```java
//解决json乱码,配置Springmvc
@RequestMapping("/j4")
@ResponseBody //不会走视图解析器,会直接返回一个字符串
public String json4() throws JsonProcessingException {
    ObjectMapper mapper=new ObjectMapper();
    User user = new User("茂凯",10,"男");
    String valueAsString = mapper.writeValueAsString(user);
    return valueAsString;
    //{"name":"茂凯","age":10,"sex":"男"}
}
```

```xml
<!--解决json乱码-->
    <mvc:annotation-driven>
        <mvc:message-converters>
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

若将@RestController注解到类上，该类下面所有方法都不走视图解析器，直接返回Json字符串 

```java
@RestController
@RequestMapping("/json")
public class UserController2 {

    @RequestMapping("/j1")
    public String test1() throws JsonProcessingException {

        ObjectMapper mapper = new ObjectMapper();
        User user1 = new User("茂凯", 5, "man");
        User user2 = new User("茂凯", 5, "man");
        User user3 = new User("茂凯", 5, "man");
        List<User> userList=new ArrayList<>();
        userList.add(user1);
        userList.add(user2);
        userList.add(user3);
        String str = mapper.writeValueAsString(userList);
        return str;
        //[{"name":"茂凯","age":5,"sex":"man"},
        // {"name":"茂凯","age":5,"sex":"man"},
        // {"name":"茂凯","age":5,"sex":"man"}]
    }
}
```

## JsonUtils

```java
public class JsonUtils {

    public static String getJson(Object object){
        return getJson(object,"yyyy-MM-dd HH:mm:ss");
    }

    public static String getJson(Object object,String dateFormat){
        ObjectMapper mapper = new ObjectMapper();
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
        mapper.setDateFormat(sdf);
        try {
            return mapper.writeValueAsString(object);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;
    }
```

```java
//使用工具类
@RequestMapping("/j5")
public String test5(){
    Date date = new Date();
    return JsonUtils.getJson(date,"yyyy-MM-dd HH:mm:ss");
    //"2021-01-11 22:35:25"
}
//使用工具类
@RequestMapping("/j6")
public String test6(){
    User user1 = new User("茂凯", 5, "man");
    User user2 = new User("茂凯", 5, "man");
    User user3 = new User("茂凯", 5, "man");
    List<User> userList=new ArrayList<>();
    userList.add(user1);
    userList.add(user2);
    userList.add(user3);
    return JsonUtils.getJson(userList);
}
```

## FastJson

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.73</version>
</dependency>
```

JSONObject 代表json对象

* JSONObject实现了Map接口
* JSONObject对应json对象，通过各种形式的get()方法获取json对象中的数据，也可利用诸如size(),isEmpty()等方法获取"键:值"对的个数和判断是否为空。其本质是通过实现Map接口并调用接口中的方法完成的

JSONArry代表json对象数组

* 内部是有List接口中的方法来完成操作的

JSON代表JSONObject和JSONArray的转化

* JSON类源码分析与使用
* 仔细观察这些方法，主要是实现json对象，json对象数组，javabean对象，json字符串之间的相互转化

```java
@RestController
@RequestMapping("/fastjson")
public class UserController3 {

    @RequestMapping("/t1")
    public String test1(){
        ArrayList<User> userList = new ArrayList<>();
        User user1 = new User("wang", 15, "man");
        User user2 = new User("wang", 15, "man");
        User user3 = new User("wang", 15, "man");
        userList.add(user1);
        userList.add(user2);
        userList.add(user3);

        //Java对象转JSON字符串
        String s = JSON.toJSONString(userList);
        String s1 = JSON.toJSONString(user1);
        //JSON转Java对象
        User user = JSON.parseObject(s1, User.class);
        System.out.println(user);

        //Java对象转JSON字符串
        JSONObject jsonObject1 = (JSONObject) JSON.toJSON(user2);
        System.out.println(jsonObject1);
        //JSON转Java对象
        User javaObject = JSON.toJavaObject(jsonObject1, User.class);
        System.out.println(javaObject);
        return s;
    }
}
```

