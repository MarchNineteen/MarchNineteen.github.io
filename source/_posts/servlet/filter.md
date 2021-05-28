---
title: Filter详解 
date: 2017-02-28 
desc:
keywords: filter
categories: [servlet]
---

# web.xml中元素执行的顺序listener->filter->struts拦截器->servlet。

- 1.过滤器的概念

Java中的Filter 并不是一个标准的Servlet ，它不能处理用户请求，也不能对客户端生成响应。 主要用于对HttpServletRequest 进行预处理，也可以对HttpServletResponse 进行后处理，是个典型的处理链。

优点：过滤链的好处是，执行过程中任何时候都可以打断，只要不执行chain.doFilter()就不会再执行后面的过滤器和请求的内容。而在实际使用时，就要特别注意过滤链的执行顺序问题

- 2.过滤器的作用描述

在HttpServletRequest 到达Servlet 之前，拦截客户的HttpServletRequest 。根据需要检查HttpServletRequest ，也可以修改HttpServletRequest 头和数据。在HttpServletResponse 到达客户端之前，拦截HttpServletResponse 。根据需要检查HttpServletResponse ，可以修改HttpServletResponse 头和数据。

- 3.过滤器的执行流程

![](http://upload-images.jianshu.io/upload_images/4942449-1824dc9cb75ad1b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 4.Filter接口

4.1 如何驱动

在 web 应用程序启动时，web 服务器将根据 web.xml 文件中的配置信息来创建每个注册的 Filter 实例对象，并将其保存在服务器的内存中

4.2 方法介绍

init()  Init 方法在 Filter 生命周期中仅执行一次，web 容器在调用 init 方法时   tomcat启动时出调用

doFilter()  Filter 链的执行  过滤方法在请求发出后立即调用，过滤特定的URL

destory()  在Web容器卸载 Filter 对象之前被调用。该方法在Filter的生命周期中仅执行一次。在这个方法中，可以释放过滤器使用的资源。

![](http://upload-images.jianshu.io/upload_images/4942449-cdddc0cc56b94c13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 5.FilterChain接口

5.1 如何实例化    代表当前 Filter 链的对象。由容器实现，容器将其实例作为参数传入过滤器对象的doFilter()方法中
    
5.2 作用   调用过滤器链中的下一个过滤器

- filter实例：

web.xml配置:
![](http://upload-images.jianshu.io/upload_images/4942449-d4cbf63d54949495.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

编码拦截器：
![](http://upload-images.jianshu.io/upload_images/4942449-8e160d51f7cc6291.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

日志拦截器：
![](http://upload-images.jianshu.io/upload_images/4942449-37a534181fd5c6ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

HelloServlet类：
![](http://upload-images.jianshu.io/upload_images/4942449-19a6c30a4c2d5be0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

结果：
![](http://upload-images.jianshu.io/upload_images/4942449-38eda2225f71a42c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 总结：

1.过滤器执行流程

2.常用过滤器

# FilterConfig接口

1、与普通的Servlet程序一样，Filter程序也很可能需要访问Servlet容器。Servlet规范将代表ServletContext对象和Filter的配置参数信息都封装到一个称为FilterConfig的对象中。

2、FilterConfig接口则用于定义FilterConfig对象应该对外提供的方法，以便在Filter程序中可以调用这些方法来获取ServletContext对象，以及获取在web.xml文件中为Filter设置的友好名称和初始化参数。

3、FilterConfig接口定义的各个方法：

```
public interfaceFilterConfig {

    String getFilterName();

    ServletContext getServletContext();

    String getInitParameter(String var1);

    Enumeration getInitParameterNames();

}
```
getFilterName方法，返回元素的设置值。

getServletContext方法，返回FilterConfig对象中所包装的ServletContext对象的引用。

getInitParameter方法，用于返回在web.xml文件中为Filter所设置的某个名称的初始化的参数值。

getInitParameterNames方法，返回一个Enumeration集合对象。
