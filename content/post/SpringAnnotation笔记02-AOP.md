---
title: "SpringAnnotation笔记02-AOP"
date: 2019-10-10T14:55:12+08:00
author: CoderLeftEar
tags: ["SpringAnnotation"]
categories: ["SpringAnnotation"]
draft: true
---



# SpringAnnotation笔记02-AOP

---

## 环绕通知相关

1. 导入aop所需jar包

2. 定义一个业务逻辑类，定义除法运算方法

3. 定义一个切面类：

   - 前置通知(@Before)：logStart：在目标方法(div)运行之前运行

    * 			后置通知(@After)：在目标方法(div)运行结束之后运行（无论方法正常结束还是异常结束）
    * 			返回通知(@AfterReturning)：在目标方法(div)正常返回之后运行
    * 			异常通知(@AfterThrowing)：在目标方法(div)出现异常以后运行
    * 			环绕通知(@Around)：动态代理，手动推进目标方法运行

4. 将业务逻辑类和切面类都添加到配置类中

5. 给切面类标注@Aspect注解

6. 在配置类上标注【开启基于注解的aop模式】@EnableAspectJAutoProxy（使用@Aspect注释标记的组件，类似于Spring中xml配置文件中的<aop: AspectJ -autoproxy>）



**切面类**：通过JoinPoint joinPoint，Exception exception，Object result等参数获取方法名称、异常、返回值等。

```java
//告诉容器这个类是一个切面类
@Aspect
public class AspectSurrounding {
    //抽取公共切入点
    @Pointcut("execution(public int com.wang.springannotation.aop.Calculator.*(..))")
    public void pointCut() {}

    //在目标方法之前切入
    @Before("execution(public int com.wang.springannotation.aop.Calculator.*(..))")
//    @Before("pointCut()")
    public void brforeLog(JoinPoint joinPoint) {
        Object[] args = joinPoint.getArgs();
        System.out.println("前置通知..." + joinPoint.getSignature().getName() + "...@Before参数表：" + "{"+args+"}");
    }

    //在目标方法运行结束之后切入（无论正常结束还是异常结束）
    @After("com.wang.springannotation.aop.AspectSurrounding.pointCut()")
    public void afterLog(JoinPoint joinPoint) {
        Object[] args = joinPoint.getArgs();
        System.out.println("后置通知..." + joinPoint.getSignature().getName() + "...@After参数表：" + "{"+args+"}");
    }

    //目标方法正常返回之后运行
    @AfterReturning(value = "pointCut()", returning = "result")
    public void returnLog(JoinPoint joinPoint, Object result) {
        System.out.println("返回通知..." + joinPoint.getSignature().getName() + "...@AfterReturning运行结果：" + "{"+result+"}");
    }

    //目标方法出现异常后运行
    @AfterThrowing(value = "pointCut()", throwing = "exception")
    public void throwLog(JoinPoint joinPoint, Exception exception) {
        System.out.println("异常通知..." + joinPoint.getSignature().getName() + "...@AfterThrowing异常结果：" + "{"+exception+"}");
    }
}
```



## 事物管理和回滚

1. 导入相关依赖：数据源、数据库驱动、spring-jdbc模块

2. 配置数据源、JdbcTemplate（spring提供的简化数据库操作的工具）操作数据

3. @Transactional：表示当前方法是一个事物方法

4. @EnableTransactionManagement：开启基于注解的事物管理功能

5. 配置事务管理器来控制事物

   @Bean

   public PlaformTransactionManger transactionManager()



UserDao：

```java
@Repository
public class UserDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    public void insertUser() {
        String sql = "INSERT INTO `test`.`userinfo` (`user`, `age`) VALUES (?, ?) ;";
        jdbcTemplate.update(sql, "lisi", 25);
    }
}
```

UserService：

```java
@Service
public class UserService {
    @Autowired
    private UserDao userDao;

    //表示当前方法是一个事物方法
    @Transactional
    public void insertUser() {
        userDao.insertUser();
        System.out.println("insert succeed!");
        //发生异常时，事物回滚，将所有操作回滚到操作之前
        int a = 1 / 0;
    }
}
```

TxConfig配置类：

```java
//开启基于注解的事物管理功能
@EnableTransactionManagement
@Configuration
@ComponentScan("com.wang.springannotation.tx")
public class TxConfig {

    //数据源
    @Bean
    public DataSource dataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setUser("root");
        dataSource.setPassword("Wang12345");
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
        return dataSource;
    }

    //
    @Bean
    public JdbcTemplate jdbcTemplate() throws PropertyVetoException {
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource());
        return jdbcTemplate;
    }

    /**
     * 注册事物管理器在容器中
     *
     * spring、mybatis等等整合，要进行事物控制，都使用DataSourceTransactionManager
     * orm、hibernate、tpa，使用WebSphereUowTransactionManager
     *
     */
    @Bean
    public PlatformTransactionManager platformTransactionManager() throws PropertyVetoException {
        return new DataSourceTransactionManager(dataSource());
    }
}
```

测试方法：

```java
public void test01() {
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(TxConfig.class);
    UserService userService = applicationContext.getBean(UserService.class);
    userService.insertUser();

    applicationContext.close();

}
```

<font size=5 color="red">程序正常执行成功，就按照程序进行操作，若报错或发生异常，回滚到程序运行前。</font>





























