---
title: "SpringAnnotation笔记01-IOC"
date: 2019-09-29T23:54:18+08:00
author: CoderLeftEar
tags: ["SpringAnnotation"]
categories: ["SpringAnnotation"]
draft: true
---



# SpringAnnotation笔记01-IOC

---

## 注解类

### 配置文件方式：

```java
//配置文件
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="person" class="com.wang.bean.Person">
        <property name="name" value="zhangsan"/>
        <property name="age" value="20"/>
    </bean>
</beans>

-----------------------------------------------------

ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean.xml"); //配置文件的位置
Person person = (Person) applicationContext.getBean("person");
System.out.println(person);
```



### 配置类方式：

`@Configuration`：告诉spring这是一个配置类。

`@Bean(value = "person")`：给容器中注册一个Bean；类型为返回值类型，id默认使用方法名，还可通过value设置id。

```java
//告诉spring这是一个配置类
@Configuration
public class PersonConfig {
    //给容器中注册一个Bean；class类型为返回值类型，默认id为方法名
    @Bean
    public Person person () {
        return new Person("lisi", 25);
    }
}

AnnotationConfigApplicationContext applicationContext2 = new AnnotationConfigApplicationContext(PersonConfig.class);
Person person2 = (Person) applicationContext2.getBean("person");
System.out.println(person2);
```



```java
//打印Bean的定义信息
//String[] beanDefinitionNames = applicationContext2.getBeanDefinitionNames();
//打印Person类型在ioc中的名称
String[] beanNamesForType = applicationContext2.getBeanNamesForType(Person.class);
for (String name : beanNamesForType) {
            System.out.println(name);
}
```



## 包扫描

>  包扫描，只要标注了@Controller、@Service、@Repository、@Component，那么就会自动扫描加入IOC容器中

**excludeFilters = Filter[]**：排除扫描（黑名单），排除指定的组件

```
@ComponentScan(value = "com.wang",
	excludeFilters = {
		@ComponentScan.Filter(type = FilterType.ANNOTATION, value = {Controller.class, Service.class, Repository.class})
})
```

**includeFilters= Filter[]**：指定扫描（百名单），扫描指定的组件，但需要配合useDefaultFilters使用，true默认扫描所有，false关闭默认扫描

    @ComponentScan(value = "com.wang", useDefaultFilters = false,
    	includeFilters = {
            @ComponentScan.Filter(type = FilterType.ANNOTATION, value = {Controller.class})
        }
    )
**@ComponentScans**：进行多个@ComponentScan的配置（java8以前），java8之后可以直接使用多个@ComponentScan()

```
@ComponentScans(value = {
	@ComponentScan()
	...
})
```

FilterType.ANNOTATION：按照注解（Controller.class）

FilterType.ASSIGNABLE_TYPE：按照类型（PersonService.class）

FilterType.ASPECTJ: 使用aspectj表达式

FilterType.REGEX: 使用正则表达式

FilterType.CUSTOM: 自定义表达式

- 自定义表达式需要实现TypeFilter接口，并实现match方法，通过MetadataReader类型的参数获取相应的信息，不设置过滤规则，默认返回false（什么都没有），设置过滤规则，可通过String.contains方法匹配类的全路径名中是否包含对应的字符串。

```java
@ComponentScan.Filter(type = FilterType.CUSTOM, classes = {MyTypeFilter.class})
    
public class MyTypeFilter implements TypeFilter {
    /**
     * @Date 2019/9/30 10:56
     * @param metadataReader 读取到的当前正在扫描档类的信息
     * @param metadataReaderFactory 获取到其他任何类的信息
     * @return boolean
     */
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        //获取当前类注解的信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        //获取当前正在扫描的类的信息
        ClassMetadata classMetadata = metadataReader.getClassMetadata();
        //获取当前类资源路径
        Resource resource = metadataReader.getResource();

        String className = classMetadata.getClassName();
        System.out.println("------>" + className);

        //过滤规则，匹配的是类的全路径名
        if (className.contains("contro")) {
            return true;
        }
        return false;
    }
}
```



## 作用域

@**Scope**

```
ConfigurableBeanFactory#SCOPE_PROTOTYPE  prototype
ConfigurableBeanFactory#SCOPE_SINGLETON  singletonorg.springframework.web.context.WebApplicationContext#SCOPE_REQUEST  request
org.springframework.web.context.WebApplicationContext#SCOPE_SESSION  session

prototype: 多例，ioc容器启动后不会调用方法创建对象放到IOC容器中，每次获取的时候回调用方法创建对象。singleton: 单例，在AnnotationConfigApplicationContext创建时，就将注解类中的数据加载
           ioc容器启动会调用方法创建对象放到IOC容器中，以后直接从容器中拿（map.get()）
           在内存中的地址都是一样的
           默认就是单例的
request: 同一次request创建一个实例
session: 同一个session创建一个实例
```



## 懒加载

@**Lazy**

容器创建的时候不加载，第一次获取的时候才加载



## @conditional({})

可用在方法和类上。

- 在类上使用：必须满足条件才会加载该类中的所有bean

- 在方法上使用：......

conditional注解中传入的是数组，传入`WindowsCondition.class`

下例是根据判断系统类型是否加载，获取当前环境信息，通过getProperty获取系统名称，使用contains判断

可根据其他方式判断，只要返回`true`便会加载，所有条件多样

```java
public class WindowsCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        //获取IOC使用的beanfactory
        ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
        //获取类加载器
        ClassLoader classLoader = context.getClassLoader();
        //获取当前环境信息
        Environment environment = context.getEnvironment();
        //获取到bean定义的注册类
        BeanDefinitionRegistry registry = context.getRegistry();

        //获取到当前系统
        String property = environment.getProperty("os.name");

        if (property.contains("Windows")) {
            return true;
        }

        return false;
    }
}
```



该注解通过以数组的方式制定类判断是否windows来判断是否进行注册

```java
@Conditional({WindowsCondition.class})
@Bean
public Person billPereson () {
    return new Person("bill", 64);
}
```



## 容器中注册组件的几种方法

- 包扫描 + 组件标记注解（@Controller/@Service/@Repository/@Component）
- @Bean(导入第三方刚包的组件)
- @Import（快速给容器导入一个组件）
  - @Import(组件类名)： 容器中会自动注册这个组件，id默认是全类名
  - ImportSeletor：创建一个类并实现ImportSeletor，返回需要导入的组建的全类名数组
  - ImportBeanDefinitionRegistrar：手动注册bean到容器中
- 使用Spring提供的FactoryBean（工厂Bean）
  - 默认获取到的是工厂bean调用getObject创建的对象
  - 获取工厂bean本身，需要在id前加`&`（getBean("&ColorFactoryBean")）



**标注在注解类上：**

> @Import({Red.class, MyImportSelector.class})

### ImportSeletor

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
//        String className = importingClassMetadata.getClassName();
//        System.out.println(className);
        //没有返回数据，最好返回空数组，避免包空指针异常
        return new String[]{"com.wang.springannotation.bean.Blue", "com.wang.springannotation.bean.Yellow"};
    }
}
```

测试结果：成功获取到Blue类型的组件（Bean）

```java
//使用@Import注解测试
@Test
public void importTest () {
    String[] beanDefinitionNames = applicationContext.getBeanDefinitionNames();
    for (String name : beanDefinitionNames) {
        System.out.println(name);
    }

    //使用@Import注册组件后，检测是否真的注册了，获取Blue类型的组件，能够成功获取到
    Blue blue = applicationContext.getBean(Blue.class);
    System.out.println(blue);

}

结果：
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
scopeConfig
com.wang.springannotation.bean.Red
com.wang.springannotation.bean.Blue
com.wang.springannotation.bean.Yellow
scopePerson
billPereson
com.wang.springannotation.bean.Blue@5906ebcb
```



### ImportBeanDefinitionRegistrar



AnnotationMetadata：当前类注解信息 

BeanDefinitionRegistry：BeanDefinition注册类，把所需要添加到容器中的bean，调用BeanDefinitionRegistry.registerBeanDefinition进行手动注册

> @Import({Red.class, MyImportSelector.class, MyImportBeanDefinitionRegistrar.class})

```java
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata,
                                        BeanDefinitionRegistry registry) {
        boolean red = registry.containsBeanDefinition("com.wang.springannotation.bean.Red");
        boolean blue = registry.containsBeanDefinition("com.wang.springannotation.bean.Blue");

        if (red && blue) {
            //指定bean名
            RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(RainBow.class);
            //可以将bean的名称改为rainBow
            registry.registerBeanDefinition("rainBow", rootBeanDefinition);
        }
    }
}
```

运行测试方法`importTest`的结果：

```java
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
scopeConfig
com.wang.springannotation.bean.Red
com.wang.springannotation.bean.Blue
com.wang.springannotation.bean.Yellow
scopePerson
billPereson
rainBow
com.wang.springannotation.bean.Blue@3d299e3
```

### Spring提供的FactoryBean

创建类并事项FactoryBean，重写三个方法，getObject, getObjectType, isSingleton

```jaca
public class ColorFactoryBean implements FactoryBean<Color> {

    /**
     *
     * @return 返回Color对象，会将这个对象添加到容器中
     * @throws Exception
     */
    @Override
    public Color getObject() throws Exception {
        return new Color();
    }

    /**
     *
     * @return 返回对象类型
     */
    @Override
    public Class<?> getObjectType() {
        return Color.class;
    }

    /**
     * false：多例，每次使用创建一份
     * true：单例进行，在容器中保存一份
     * @return
     */
    @Override
    public boolean isSingleton() {
        return true;
    }
}
```

再在配置类中创建ColorFactoryBean类型的Bean，返回ColorFactoryBean类型。

```java
 @Bean
public ColorFactoryBean colorFactoryBean() {
    return new ColorFactoryBean();
}
```

最后在测试方法中得到的是`colorFactoryBean`，但通过`getClass`方法可获取它真正的类为`Color`

```java
colorFactoryBean
=========分割线=========
colorFactoryBean的类型：class com.wang.springannotation.bean.Color
```

## 生命周期

### 1.指定初始化和销毁方法

- 在`@Bean`中指定`initMethod`和`destoryMethod`方法

```java
@Bean(initMethod = "init", destroyMethod = "destory")
public Cat cat() {
    return new Cat();
}

public class Cat {
    public Cat() {
        System.out.println("constructor...cat...");
    }
    public void init() {
        System.out.println("init...cat...");
    }
    public void destory() {
        System.out.println("destory...cat...");
    }
}
```



### 2.通过然Bean事项`InitalizingBean`（定义初始化逻辑）和`DisposableBean`（定义销毁逻辑）

```java
public class Dog implements InitializingBean, DisposableBean {

    public Dog() {
        System.out.println("dog...constructor...");
    }

    /**
     * 销毁
     * @throws Exception
     */
    @Override
    public void destroy() throws Exception {
        System.out.println("dog...destory...");
    }

    /**
     * 初始化
     * @throws Exception
     */
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("dog...afterPropertiesSet...");
    }
}
```

测试方法

```java
@Test
public void lcTest() {
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
    applicationContext.close();
}

结果：
constructor...cat...
init...cat...
    
dog...constructor...
dog...afterPropertiesSet...
    
dog...destory...
destory...cat...
```

### 3.使用JSR250

- `@PostConstruct`：在bean创建完成并且属性赋值已完成，执行初始化方法
- `@PreDestory`：在容器销毁bean之前通知进行清理工作

在实体类上`@Component`和在配置类上添加`@ComponentScan("com.wang.springannotation.bean")`也可自动扫描。

```java
@Component
public class Mouse {
    public Mouse() {
        System.out.println("mouse...constructor...");
    }

    @PostConstruct
    public void init() {
        System.out.println("init...@PostConstruct...");
    }

    @PreDestroy
    public void destory() {
        System.out.println("destory...@PreDestroy...");
    }
}

结果：
mouse...constructor...
init...@PostConstruct...
destory...@PreDestroy...
```

### 4.创建类并实现BeanPostProcessor，bean后置处理器(后置处理器，初始化前后进行处理工作)

- 在bean初始化前后进行一些处理工作
- postProcessBeforeInitializtion：在初始化前工作
- postProcessAfterInitializtion：在初始化后工作

<font size=5 color="red">使用时要像bean一样，将后置处理器放到容器中</font>

```java
@Component
public class MyBeanPostProcess implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessBeforeInitialization..." + beanName + "==>" + bean);
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessAfterInitialization..." + beanName + "==>" + bean);
        return bean;
    }
}

结果：
constructor...cat...
postProcessBeforeInitialization...cat==>com.wang.springannotation.bean.Cat@1b083826
init...cat...
postProcessAfterInitialization...cat==>com.wang.springannotation.bean.Cat@1b083826
```



## 属性赋值

`$({person.nickName})`：从person.properties配置文件中获取，但有前提，在配置类上添加注解`@PropertySource(value = {"classpath:/person.properties"})`加载配置文件。

```java
@Value("zhangsan")
private String name;
@Value("#{20}")
private Integer age;
@Value("${person.nickName}")
private String nickName;
```

- 获取名为`person`的bean，通过`@Value`注解赋值，properties配置文件，`@PropertySource`加载配置文件，成功得到Person数据。
- 还可通过`getEnvironment`方法获取当前上下文所有信息，直接获取properties文件中的person.nickName信息。

```java
Person person = (Person) applicationContext.getBean("person");
System.out.println(person);

ConfigurableEnvironment environment = applicationContext.getEnvironment();
String property = environment.getProperty("person.nickName");
System.out.println(property);

结果：
Person{name='zhangsan', age=20, nickName='xiaosan'}
========================
xiaosan
```



## 自动装配：

> `Spring利用依赖注入（DI），完成对IOC容器中各个组件的依赖关系赋值`

### @Atuowired：自动注入：

#### 1、默认优先按照类型取容器中找对应的组件：PersonService bean = applicationContext.getBean(PersonService.class);

通过`@ComponentScan`将三层包扫描，在PersonService（已重写toString，打印PersonDao对象）层使用`@Autowired`注解自动注入，所以自动获取到的是容器中的同一个PersonDao类型的bean。

```java
@Test
public void test01() {
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(AutowiredConfig.class);

    PersonService bean = applicationContext.getBean(PersonService.class);
    System.out.println(bean);

    PersonDao bean1 = applicationContext.getBean(PersonDao.class);
    System.out.println(bean1);

}

结果：是同一个对象
PersonService{personDao=com.wang.springannotation.dao.PersonDao@2c34f934}
com.wang.springannotation.dao.PersonDao@2c34f934
```

#### 2、如果找到多个相同类型的条件，在通过依赖注入时的变量名称进行匹配。

在容器中有personDao、personDao2两个，类型相同，根据名称匹配，personDao2，所有打印lable值为2的对象。相当于调用`applicationContext.getBean("personDao2");`

```java
public Class PersonService {
    @Autowired
    //@Qualifier("personDao")
    private PersonDao personDao2;

    @Override
    public String toString() {
        return "PersonService{" +
                "personDao=" + personDao2 +
                '}';
    }
}


public class AutowiredConfig {
    @Bean("personDao2")
    public PersonDao personDao() {
        PersonDao personDao = new PersonDao();
        personDao.setLable("2");
        return personDao;
    }
}

结果：
PersonService{personDao=PersonDao{lable=2}}
```

#### 3、使用`@Qualifier("personDao")`，优先级大于变量名称（上述第二种）

案例在上面（2.），结果：

```java
PersonService{personDao=PersonDao{lable=1}}
```

#### 4、容器中没有任何bean时（注释掉@Bean("PersonDao2")和@Repository）

此时容器中没有任何PersonDao类型的bean，此时在@Autowored注解上添加@Autowired(required = false)表示不必须找到PersonDao类型的bean

<font color="red">此处不贴代码，是正确的，相信我！</font>

#### 5、@Primary：让Spring进行自动装配，默认首选，同时使用@Qualifier("personDao")的话，后者优先级更大。

```java
public class AutowiredConfig {
    @Primary
    @Bean("personDao2")
    public PersonDao personDao() {
        PersonDao personDao = new PersonDao();
        personDao.setLable("2");
        return personDao;
    }
}
```



### @Resource(JSR250)、@Inject(JSR330)

- @Resource(name = "name")：
  - 与autowired功能相同，默认使用变量名称的bean，可在该注解中设置装配的bean名称。
  - 但不能支持@Primary和@Autowired(reqiured = false)。
- @Inject
  - 需要导入javax.inject的jar包，与autowired功能相同，支持@Primary，没有reqiured = false。



@Autowired是Spring中定义的；@Resource和@Inject是JSR中定义的。

```java
//@Autowired(required = false)
//@Qualifier("personDao")
//@Resource(name = "personDao2")
@Inject
private PersonDao personDao;
```

## @Atuowired标注位置

- 标注在方法上：
  - @Bean + 方法数：参数从容器中获取；
  - 默认不写@Autowired注解效果一样的，是自动的；
- 标注在构造器上：
  - 如果只有一个有参构造器，那么可以省略@Autowired，
- 标注在参数前：
  - 从容器中直接后获取对应的bean

- 自定义组件先要使用Spring容器底层的一些组件（ApplicationContext，BeanFactory，xxx）
  - 自定义组件事项xxxAware；在创建对象的时候，会调用规定的方法注入相关组件；Aware
  - 把Spring底层一些组件注入到自定义的Bean中；
  - xxxWare：功能使用xxxProcessor（ApplicationContextAware ==> ApplicationContextAwareProcessor）

## @Profile

可以卸载配置类上，只有使用标注的环境才能使用此配置类中的所有bean。

Spring为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能。

动态切换数据源（test、dev、prod）

```java
@Configuration
//加载数据库配置文件
@PropertySource("classpath:/db.properties")
public class ProfileConfig implements EmbeddedValueResolverAware {

    //从配置文件中获取username
    @Value("${db.username}")
    private String username;

    //不知道是个啥
    private StringValueResolver resolver;

    //从EmbeddedValueResolverAware实现的方法中通过StringValueResolver获取driverClass
    private String driverClass;


    @Bean
    @Profile("test")
    public Red red() {
        return new Red();
    }


    /**
     * 测试环境
     *
     * @Date 2019/10/10 11:53
     * @param pwd
     * @return javax.sql.DataSource
     */
    @Profile("test")
    @Bean
    public DataSource dataSourceTest(@Value("${db.password}") String pwd) throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();

        dataSource.setUser(username);
        dataSource.setPassword(pwd);
        dataSource.setJdbcUrl("jdbc://mysql://localhost:3306/taotao");

        String value = resolver.resolveStringValue("${db.driverClass}");
        dataSource.setDriverClass(value);

        return dataSource;
    }

    /**
     * 开发环境
     *
     * @Date 2019/10/10 11:53
     * @param pwd
     * @return javax.sql.DataSource
     */
    @Profile("dev")
    @Bean
    public DataSource dataSourceDev(@Value("${db.password}") String pwd) throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();

        dataSource.setUser(username);
        dataSource.setPassword(pwd);
        dataSource.setJdbcUrl("jdbc://mysql://localhost:3306/taotao1");

        dataSource.setDriverClass(driverClass);

        return dataSource;
    }

    /**
     * 生产环境
     *
     * @Date 2019/10/10 11:53
     * @param pwd
     * @return javax.sql.DataSource
     */
    @Profile("prod")
    @Bean
    public DataSource dataSourceProd(@Value("${db.password}") String pwd) throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();

        dataSource.setUser(username);
        dataSource.setPassword(pwd);
        dataSource.setJdbcUrl("jdbc://mysql://localhost:3306/taotao2");

        dataSource.setDriverClass(driverClass);

        return dataSource;
    }


    @Override
    public void setEmbeddedValueResolver(StringValueResolver resolver) {
        this.resolver = resolver;
        driverClass = resolver.resolveStringValue("${db.driverClass}");
    }
}
```



@Profile：指定组件在哪个环境的情况下才能注册到容器中，任何情况都不注册组件

- 在添加了@Bean，再添加此标记是才能注册到容器当中，默认使用@Profile(default)环境
- 使用@Profile指定环境时，才进行注册
- 没有标识环境时，任何情况都不加载

### 使用测试方法时

#### 1、使用命令行动态参数：

使用有参AnnotationConfigApplicationContext构造器

```java
public AnnotationConfigApplicationContext(Class<?>... annotatedClasses) {
    this();
    register(annotatedClasses);
    refresh();
}
```

<font color="red">没有使用@Profile注解的方法，都会被加载到调用的环境中。</font>

在虚拟机参数位置加载 `-Dspring.profiles.active=test`（ 优先级大于@Profile("default") ）

#### 2、代码方式激活某种环境：

<font color="red">没有使用@Profile注解的方法，都会被加载到调用的环境中。</font>

无参AnnotationConfigApplicationContext构造器，相当于自己重新写有参构造方法中的代码。自己设置使用哪些开发环境，注册哪个配置类，刷新。

```java
AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
applicationContext.getEnvironment().setActiveProfiles("test", "dev");
applicationContext.register(ProfileConfig.class);
applicationContext.refresh();

结果：
red
dataSourceTest
dataSourceDev
```









### 













