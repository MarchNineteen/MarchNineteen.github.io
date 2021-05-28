---
title: webXml和spring初始化顺序问题
date: 2019-10-17 
desc:
keywords: webXml 
categories: [日常问题记录]
---

# 问题产生

同事想要在项目初始化完成后，开启线程池执行业务逻辑的代码，其中需要注入spring的bean，他想到使用servlet的listener，但是无法在listener中无法直接通过@Resource注解获取实例。

# 原因分析
在tomcat容器中，listener，filter，servlet等都不是交给spring容器管理，两者环境不一致当然就获取不到了。

# 问题解决
如果需要在tomcat等web容器环境中获取spring实例需要获取ServletContext，通过webContext去获取springIOC容器从而获取spring实例。

```
//ServletContext,是一个全局的储存信息的空间，服务器开始，其就存在，服务器关闭，其才释放。request，一个用户可有多个；session，一个用户一个；而servletContext，所有用户共用一个。
//所以，为了节省空间，提高效率，ServletContext中，要放必须的、重要的、所有用户需要共享的线程又是安全的一些信息。
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        WebApplicationContext springContext = WebApplicationContextUtils.getWebApplicationContext(servletContextEvent.getServletContext());
        Init init = (Init) springContext.getBean("Init");

        System.out.println(init);
        System.out.println("listener init");
    }
```

# 问题延伸

## web.xml文件中各个节点参数的启动顺序

可以明确的是初始化顺序context-param -> listener-> filter -> servlet。

在代码中分别自定义listener，filter，spring bean（Init）。启动项目可以看出先打印 listener init,再打印filter init。

那么spring容器的bean呢，在什么时候会进行初始化。新建Init类实现InitializingBean，然后在xml中配置该bean，这样spring容器初始化是会去执行afterPropertiesSet方法。

```
@Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitializingBean");
    }
```

创建完java文件后，咱们先来看看 web.xml。

一般springIOC容器在context-param进行初始化，**此时spring就已经在初始化xml文件中的实例了**。

```
<!--在web.xml中通过contextConfigLocation配置spring，contextConfigLocation-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>
            classpath:spring-context.xml
        </param-value>
    </context-param>
```

spring的xml其实也可以放在其它地方进行加载，比如说listener，servlet中，例如springMVC的xml文件，会配置在servlet中，进行路由转发。
```
<!-- 请求转发器 -->
  <servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
  <servlet-name>dispatcher</servlet-name>
  <url-pattern>/</url-pattern>
  </servlet-mapping>
```

如若此时在contextConfigLocation下增加classpath:spring-context.xml，spring同样也会去初始化配置文件。所以可以得出结论，springIOC环境的
初始化与web context的初始化顺序无直接关联。

那么问题来了，在listener，filter中获取WebApplicationContext可以获得spring bean吗？

我们先把spring-context.xml放在context-param中，让它在lister之前进行spring初始化，看看效果
```
    修改xml文件
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>
            classpath:spring-context.xml
        </param-value>
    </context-param>
    
 修改自定义CustomListener   
 @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        WebApplicationContext springContext = WebApplicationContextUtils.getWebApplicationContext(servletContextEvent.getServletContext());
        Init init = (Init) springContext.getBean("init");

        System.out.println(init);
        System.out.println("listener init");
    }
```

此时InitializingBean最先打印出来，说明其在listener，filter之前执行了。
控制台输出 com.wyb.web.config.Init@e082c17，说明在listen之前，spring的Context已经初始化，可以获取到bean，那么我们把spring-context.xml
放在servlet中去初始化，即在listener之后初始化会怎么样呢？

修改web.xml代码

```
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml,classpath:spring-context.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
  <servlet-name>dispatcher</servlet-name>
  <url-pattern>/</url-pattern>
  </servlet-mapping>
```

此时InitializingBean最后打印，说明其在listener，filter之后执行了。
CustomListener抛出NoSuchBeanDefinitionException异常，即无法获得该实例，由此得出结论，spring bean的初始化顺序由配置文件在web.xml中什么时候加载有关，与listener，filter无直接关系。
**若listener想获得spring bean，该bean需要在listener之前
进行初始化**。

两个spring bean之间进行相互注入依赖，则要看xml文件在web.xml进行加载的顺序。绝对了spring bean初始化的顺序。
