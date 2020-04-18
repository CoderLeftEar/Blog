---
title: "SpringAnnotation笔记03-Servlet30"
date: 2019-10-13T21:45:04+08:00
author: CoderLeftEar
tags: ["SpringAnnotation"]
categories: ["SpringAnnotation"]
draft: true
---



# SpringAnnotation笔记03-Servlet30

---

## @WebService("/hello")

拦截指定路径名称



## Shared libraries（共享库）/ runtimes pluggability（运行时插件）

### 1、

前提条件：需要闯将META-INF.services文件夹，并在其中创建名为javax.servlet.ServletContainerInitializer的文件，其内容为需要加载类的全类名com.wang.servelt.MyServletContainerInitializer。然后便会在项目启动时，会加载META-INF.services下的文件，找到对应的类，并加载它。



**@HandlesTypes(value = {HelloService.class})**：容器在启动的时候会将@HandlesTypes指定的这个类型下面指定的子类（实现类、子类）传递过来。

`c`     @HandlesTypes中指定的类型的所有子类
`ctx` 代表当前Web应用的ServletContxt，一个Web应用一个

```java
/**
 * 容器在启动的时候会将@HandlesTypes指定的这个类型
 * 下面指定的子类（实现类、子类）传递过来。
 */
@HandlesTypes(value = {HelloService.class})
public class MyServletContainerInitializer implements ServletContainerInitializer {

    /**
     * 启动时会运行此方法
     * 
     * @Date 2019/10/14 9:01
     * @param c   @HandlesTypes中指定的类型的所有子类
     * @param ctx 代表当前Web应用的ServletContxt，一个Web应用一个ServletContext
     * @return void
     */
    @Override
    public void onStartup(Set<Class<?>> c, ServletContext ctx) throws ServletException {
        for (Class<?> cla : c) {
            System.out.println(cla);
        }
    }
}
```

### 2、注册组件

- 使用ServletContext注册Web组件（Servlet、Filter、Listener）

- 使用编码的方式，在项目启动的时候给ServletContext里面添加组件；

  必须在项目启动的时候来添加；

  - ServletContainerInitializer得到的ServletContext；
  - ServletContextListener得到的ServletContext；

```java
@HandlesTypes(value = {HelloService.class})
public class MyServletContainerInitializer implements ServletContainerInitializer {

    @Override
    public void onStartup(Set<Class<?>> c, ServletContext ctx) throws ServletException {
        for (Class<?> cla : c) {
            System.out.println(cla);
        }

        //注册组件 ServletRegistration
        ServletRegistration.Dynamic userServlet = ctx.addServlet("userServlet", UserServlet.class);
        //配置servlet的映射信息
        userServlet.addMapping("/user");

        //注册组件 Listener
        ctx.addListener(UserListener.class);

        //注册组件 FilterRegistration
        FilterRegistration.Dynamic userFilter = ctx.addFilter("userFilter", UserFilter.class);
        //配置Filter映射信息
        userFilter.addMappingForUrlPatterns(EnumSet.of(DispatcherType.REQUEST), true, "/*");

    }
}
```

UserServlet：

```java
public class UserServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("UserServlet...");
    }
}
```

UserFilter：

```java
public class UserFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("filter...init...");
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        //过滤请求
        System.out.println("UserFilter...doFilter...");
        //放行
        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {
        System.out.println("filter...destory...");
    }
}
```

UserListener：

```java
public class UserListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("Listener...init...");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        ServletContext servletContext = sce.getServletContext();
        System.out.println("Listener...destory...");
    }
}
```



