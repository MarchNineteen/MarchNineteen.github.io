---
title: springmvc整合swagger（非springboot项目）
date: 2019-11-27 
desc:
keywords: swagger 
categories: [日常问题记录]
---

# 项目需求

公司原来的项目是struts2，然后想要改成前后端分离项目，又不想完全抛去原来的一些东西，选择了单独的springmvc，没有使用springboot，这就开启了我的难受之旅。

# 需要依赖

万万没想到啊，还不是maven项目，jar包需要自己去探索到底需要哪些jar包，然后还得避免jar包冲突，我太南了...
过程中guava库需要升级 jackson库必须存在

最后根据maven项目的所引入的依赖分析，然后自己测试，需要jar包如下：

![swagger所需jar](/uploads/日常问题记录/swagger所需jar.jpg)

[下载](/uploads/日常问题记录/swagger.rar)

# 过程遇到的问题

上述过程完成后，好不容易项目启动不报错了，但是访问swagger-ui.html，会出现弹框提示错误，这个错误网上随便一搜就会知道是swagger-ui的页面没有映射出来，需要加个静态资源映射。
但是并没有那么简单。

网上搜索大部分搜索出来的都是在xml文件中添加swagger文件的映射，例如：

```
<mvc:resources mapping="swagger-ui.html" location="classpath*:/META-INF/resources/"/>
<mvc:resources mapping="webjars/**" location="classpath*:/META-INF/resources/webjars/"/>
```

但是在我的工程中没有效果，我也不知道为什么。

解决办法一：

由于项目还是采用web.xml格式配置，dispatcherServlet需拦截所有请求，如果加*.htm这种类型，生成的文档接口也必须手动修改访问路径，因为swagger是根据mapping去生成请求路径的。

这样配置之后静态资源也会访问springMVC的servlet，这是我们不需要的，所以我们需要在springmvc.xml添加<mvc:default-servlet-handler/>,添加一个默认servlet，会自动过滤静态资源请求。


解决办法二：

去swagger的网站下载zip包，把dist文件目录copy至自己的页面下，修改index.htm文件中的url地址, /v2/api-docs即为swagger访问数据的地址。

```
    window.onload = function() {
      // Begin Swagger UI call region
      const ui = SwaggerUIBundle({
        // url: "https://petstore.swagger.io/v2/swagger.json",
        url: "http://xx.xx.xx/v2/api-docs",
        dom_id: '#swagger-ui',
        deepLinking: true,
        presets: [
          SwaggerUIBundle.presets.apis,
          SwaggerUIStandalonePreset
        ],
        plugins: [
          SwaggerUIBundle.plugins.DownloadUrl
        ],
        layout: "StandaloneLayout"
      })
      // End Swagger UI call region

      window.ui = ui
    }
```

这样添加之后，我们需要去直接访问自己的页面地址，而不是访问swagger-ui.html，例如/swagger/index.html。
如果采用*.htm或者*.do这种方式，可能会出现页面访问得到的，提示/v2/api-docs接口无法请求。解决方案是配置把swagger或者数据的接口置入dispatcherServlet中

```
    <servlet-mapping>
        <servlet-name>SpringmvcDispatcherServlet</servlet-name>
        <url-pattern>/v2/api-docs</url-pattern>
    </servlet-mapping>
```

