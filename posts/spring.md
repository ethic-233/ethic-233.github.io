---
title: 'Spring注解驱动开发'
date: 2022-10-19 12:34:57
tags: [Spring]
published: true
hideInList: false
feature: 
isTop: false
---
![](https://ethic-233.github.io/post-images/1666155456401.gif)# Spring注解驱动开发

## bean的生命周期

bean的生命周期：bean创建、初始化、销毁的过程

容器管理bean的生命周期，我们可以自定义初始化和销毁方法，容器在bean进行到当前生命周期的时候来调用我们自定义的初始化和销毁方法

- 构造（对象创建）：单实例——在容器启动的时候创建对象，多实例——在每次获取的时候创建对象
- `BeanPostProcessor.postProcessBeforeInitialization`
- 初始化：对象创建完成，并赋值好，调用初始化方法
- 销毁：单实例——容器关闭的时候，多实例——容器不会管理这个bean，容器不会调用销毁方法

applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);

invokeInitMethods(beanName, wrappedBean, mbd);  执行初始化

遍历得到容器中所有的BeanPostProcessor：挨个执行beforeInititalization，

一旦返回null，跳出for循环，不会执行后面的BeanPostProcessor，就是PostProcessorsBeforeInititalization

1. 指定初始化和销毁方法：

   通过@Bean指定`init-method`和`destroy-method` 

2. 通过让Bean实现`InitializingBean`（定义初始化逻辑），`DisposableBean`（定义销毁逻辑）

3. 可以使用JSR250，@PostConstruct：在bean创建完成并且属性赋值完成，来执行初始化方法

   @PreDestroy：在容器销毁bean之前通知我们进行清理工作

4. `BeanPostProcessor`：bean的后置处理器，在bean初始化前后进行一些处理工作：

   `postProcessBeforeInitialization`：在初始化之前工作

   `postProcessAfterInitialization`：在初始化之后工作

Spring底层对BeanPostProcessor的使用：

bean赋值，注入其他组件，@Autowired，生命周期注解功能，@Async   xxx

## 组件添加

### `@Configuration`

告诉Spring这是一个配置类

对应的IOC容器是`AnnotationConfigApplicationContext`

```java
new AnnotationConfigApplicationContext(配置类.class);
```

### `@Bean`

给容器中注册一个Bean，类型为返回值的类型，id是默认用方法名做id

```java
@Bean("person")
public Person person01(){
    return new Person("ethic",20);
}
```

注册了一个名为person的组件

### `@ComponentScan`

包扫描，只要标注了`@Controller`，`@Service`，`@Repository`，`@Component`

```xml
<context:component-scan base-package="包名"></context:component-scan>
```

在配置类里类名上面

```java
@ComponentScan(value="包名", excludeFilters = {
	@Filter(type=FilterType.ANNOTATION, classes={Controller.class}),
    @Filter(type=FilterType.ASSIGNABLE_TYPE, classes={BookService.class}),
    @Filter(type=FilterType.CUSTOM, classes={MyTypeFilter.class})
}, useDefaultFilters = false)
```

`value`：指定要扫描的包

`excludeFilters = Filter[]`：指定扫描的时候按照什么规则排除那些组件

`includeFilters = Filter[]`：指定扫描的时候只需要包含那些组件

`FilterType.ANNOTATION`：按照注解

`FilterType.ASSIGNABLE_TYPE`：按照给定的类型

`FilterType.ASPECTJ`：使用ASPECTJ表达式

`FilterType.REGEX`：使用正则指定

`FilterType.CUSTOM`：使用自定义规则

读取到的当前正在扫描的类的信息：`metadataReader`

```java
// 获取当前类注解的信息
AnnotationMetadata anno = metadataReader.getAnnotationMetadata();
// 获取当前正在扫描的类信息
ClassMetadata classMetadata = metadataReader.getClassMetadata();
// 获取当前类资源(类的路径)
Resource resource = metadataReader.getResource();
String className = classMetadata.getClassName();
```

可以获取到其他任何类信息的：`metadataReaderFactory`

### `@Controller`

### `@Service`

### `@Repository`

Dao层的东西

### `@Scope`

```java
@Scope("prototype")
```

`prototype`：多实例的，ioc容器启动并不会去调用方法创建对象放在容器中，每次获取的时候才会调用方法创建对象

`singleton`：单实例的（默认值），ioc容器启动会调用方法创建对象放到ioc容器中，以后每次获取就是直接从容器中（`map.get()`）拿

`request`：同一次请求创建一个实例

`session`：同一个session创建一个实例

### `@Lazy`

懒加载：单实例bean，默认在容器启动的时候创建对象。但懒加载是容器启动的时候不创建对象，第一次使用（获取）Bean创建对象，并初始化

### `@Component`

### `@Conditional`

按照一定的条件进行判断，满足条件给容器中注册bean

`ConditionContext`：判断条件能使用的上下文（环境）

`AnnotatedTypeMetadata`：注释信息

```java
public boolean matches(ConditionContext context, AnnotatedType anno){
    // 能获取到ioc使用的beanfactory
    ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
	// 获取类加载器
    ClassLoader classLoader = context.getClassLoader();
    // 获取当前环境信息
    Environment environment = context.getEnvironment();
    // 获取到bean定义的注册类
    BeanDefinitionRegistry registry = context.getRegistry();
    
    String property = environment.getProperty("os.name");
    if(property.contains("linux")){
        return true;
    }
    return false;
}
```

### `@Primary`

### `@Import`

### ImportSelector

### 工厂模式

## 组件赋值

### 自动装配

Spring利用依赖注入（DI），完成对IOC容器中各个组件的依赖关系赋值

- @Autowired  自动注入：

  - 默认优先按类型去容器中找对应的组件：applicationContext.getBean(BookDao.class);  找到就赋值

    ```java
    BookService{
        @Autowired
        BookDao bookDao;
    }
    ```

  - 如果找到多个相同类型的组件，再将属性的名称作为组件的id去容器中查找：applicationContext.getBean("bookDao");

  - @Qualifier("bookDao")：使用@Qualifier指定需要装配的组件的id，而不是使用属性名

  - 自动装配默认一定要将属性赋值好，没有就会报错

    可以使用`@Autowired(required=false)`

  - @Primary：让Spring进行自动装配的时候，默认使用首选的bean

    也可以继续使用@Qualifier指定需要装配的bean的名字

- Spring还支持使用@Resource(JSR250)和@Inject(JSR330)[java规范的注解]

  - `@Resource`：可以和@Autowired一样实现自动装配功能，默认是按照组件名称进行装配的；没有能支持@Primary功能没有支持@Autowired(required=false)
  - `@Inject`：需要导入javax.inject的包，和Autowired的功能一样，没有required=false的功能
  - `@Autowired`：Spring定义的，@Resource，@Inject都是java规范

  AutowiredAnnotationBeanPostProcessor：解析完成自动装配功能

- @Autowired：构造器，参数，方法，属性

  都是从容器中获取参数组件的值

  - 标注在方法位置：@Bean+方法参数；参数从容器中获取

    默认不写@Autowired效果是一样的；都能自动装配

  - 标在构造器上：如果组件只有一个有参构造器，这个有参构造器的@Autowired可以省略，参数位置的组件还是可以自动从容器中获取

  - 放在参数位置

- 自定义组件想要使用Spring容器底层的一些组件（ApplicationContext，BeanFactory，xxx），自定义组件实现xxxAware；在创建对象的时候，会调用接口规定的方法注入相关组件；把Spring底层一些组件注入到自定义的Bean中

  - xxxAware：功能是使用xxxProcessor

    ApplicationContextAware → ApplicationContextAwareProcessor

### `@Value`

使用@Value赋值：

1. 基本数值
2. 可以写SpEl：#{}
3. 可以写${}：取出配置文件中的值（在运行环境变量里面的值）

```java
@Value("张三")
private String name;
@Value("#{20-2}")
private Integer age;

```

### `@PropertySource`

读取外部配置文件中的k/v保存到运行的环境变量中

加载完外部的配置文件后使用${}取出配置文件的值

`@PropertySource(value={"classpath:/person.properties"})`

### `@PropertySources`

### `@Profile`

Spring为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能

开发环境、测试环境、生产环境

`@Profile`：指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件

- 使用命令行动态参数：在虚拟机参数位置加载`-Dspring.profiles.active=test`

  加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中，默认是default环境

  - 创建一个applicationContext

    ```java
    AnnotationConfigApplicationContext applicationContext = new AnnocationConfigApplicationContext();
    
    ```

  - 设置需要激活的环境

    ```java
    applicationContext.getEnvironment().setActiveProfiles("test","dev");
    
    ```

  - 配置主配置类

    ```java
    applicationContext.register(MainConfigOfProfile.class);
    
    ```

  - 启动刷新容器

    ```java
    applicationContext.refresh();
    
    ```

- 代码的方式激活某种环境

  写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效

  没有标注环境标识的bean在，任何环境下都是加载的

## 组件注入

### 给容器中注册组件

- 包扫描+组件标注注解（@Controller/@Service/@Repository/@Component）[自己写的类]

- @Bean[导入的第三方包里的组件]

- @Import[快速给容器中导入一个组件]

  - @Import（要导入到容器中的组件），容器中就会自动注册这个组件，id默认是全类名	
  - `ImportSelector`：返回需要导入的组件的全类名数组
  - `ImportBeanDefinitionRegister`：手动注册bean到容器中

- 使用Spring提供的`FactoryBean`（工厂bean） 

  - 默认获取到的是工厂bean调用getObject创建的对象

  - 要获取工厂bean本身，我们需要给id前面加一个&

    &colorFactoryBean

```java
/*
自定义逻辑返回需要导入的组件，返回值，就是导入到容器中的组件全类名
AnnotationMetadata：当前标注@Import注解的类的所有注解信息
*/
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImport(AnnotationMetadata importingClassMetadata){
        importingClassMetadata.
        // 方法不要返回null值
        return new String[]{"全类名1","全类名2"};
    }
}

```

```java
/* 
AnnotationMetadata：当前类的注解信息
BeanDefinitionRegistry：BeanDefinition注册类
	把所有需要添加到容器中的bean：用BeanDefinitionRegistry.registerBeanDefinition手工注册进来
*/
public class MyImportBeanDefinitionRegister implements ImportBeanDefinitionRegister {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata){
 		boolean definition1 = register.containsBeanDefinition("全类名1");       
    	boolean definition2 = register.containsBeanDefinition("全类名2");
    	if(definition1 && definition2){
            // bean的定义信息, bean的类型、作用域...
            RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(RainBow.class);
            // 注册一个bean, 指定bean名
            registry.registerBeanDefinition("rainBow",beanDefinition);
        }
    }
}

```

`MainConfig`里面

```java
@Import({类名.class, MyImportSelector.class})

```

## AnnotationConfigApplicationContext

## 声明式事务

环境搭建

1. 导入相关依赖

   数据源、数据库驱动、Spring-jdbc模块

2. 配置数据源，JdbcTemplate（Spring提供的简化数据库操作的工具）操作数据

3. 给方法上标注@Transactional表示当前方法是一个事务方法

4. @EnableTransactionManagement开启基于注解的事务管理功能

5. 配置事务管理器来控制事务

   @Bean

   public PlatformTransactionManager transactionManager()

## AOP

AOP：动态代理，指在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式

1. 导入AOP模块：Spring AOP：（spring-aspects）

2. 定义一个业务逻辑类（spring-aspects）：在业务逻辑运行的时候将日志进行打印（方法之前，方法运行结束，方法出现异常）

3. 定义一个日志切面类（LogAspects）：切面类里面的方法需要动态感知MathCalculator.div运行到哪里然后执行

   通知方法

   - 前置通知（@Before）：logStart，在目标方法（div）运行之前运行

     @Before在目标方法之前切入：切入点表达式（指定在哪个方法切入）

     ```java
     @Before("public int com.aop.MathCalculator.div.*(int, int)")
     
     ```

   - 后置通知（@After）：logEnd，在目标方法（div）运行结束之后运行（无论方法正常结束还是异常结束）

   - 返回通知（@AfterReturning）：logReturn，在目标方法（div）正常返回之后运行

   - 异常通知（@AfterThrowing）：logException，在目标方法（div）出现异常以后运行

   - 环绕通知（@Around）：动态代理，手动推进目标方法运行（joinPoint.procced()）

   抽取公共的切入点表达式

   - 本类引用
   - 其他的切面引用

4. 给切面类的目标方法标注何时何地运行（通知注解）

5. 将切面类和业务逻辑类（目标所在方法类）都加入到容器中

6. 必须告诉Spring哪个类是切面类（给切面类加上一个注解：@Aspect）

7. 给配置类中加@EnableAspectJAutoProxy（开启基于AspectJ的自动代理，基于注解的AOP模式）

   JoinPoint一定要出现在参数表的第一位

三步：

- 将业务逻辑组件和切面类都加入到容器中，告诉Spring哪个是切面类（@Aspect）
- 在切面类上的每一个通知方法上标注通知注解，告诉Spring何时何地运行（切入点表达式）
- 开启基于注解的AOP模式：@EnableAspectJAutoProxy

AOP原理（看给容器中注册了什么组件，这个组件什么时候工作，这个组件的功能是什么）：

@EnableAspectJAutoProxy

- 是什么？

  @Import(AspectJAutoProxyRegistrar.class)：给容器中导入AspectJAutoProxyRegistrar

  利用AspectJAutoProxyRegistrar自定义给容器中注册bean：

  internalAutoProxyCreator=AnnotationAwareAspectJAutoProxyCreator

  给容器注册一个AnnotationAwareAspectJAutoProxyCreator，（注解装配的切面自动代理创建器

- AnnotationAwareAspectJAutoProxyCreator

## 扩展原理

### BeanFactoryPostProcessor

BeanPostProcessor：bean后置处理器，bean创建对象初始化前后进行拦截工作的

BeanFactoryPostProcessor：beanFactory的后置处理器，在BeanFactory标准初始化之后调用，所有的bean定义已经保存加载到beanFactory，但是bean的实例还未创建

1. IOC容器创建对象

2. invokeBeanFactoryPostProcessors(beanFactory);  执行BeanFactoryPostProcessor

   如何找到所有的BeanFactoryPostProcessor并执行他们的方法

   - 直接在BeanFactory中找到所有类型是BeanFactoryPostProcessor的组件，并执行他们的方法
   - 在初始化创建其他组件前面执行

### BeanDefinitionRegistryPostProcessor

postProcessBeanDefinitionRegistry();

在所有bean定义信息将要被加载，bean实例还未创建的

优先于BeanFactoryPostProcessor执行，利用BeanDefinitionRegistryPostProcessor给容器中再额外添加一些组件

原理：

1. IOC创建对象
2. refresh() → invokeBeanFactoryPostProcessors(beanFactory);
3. 从容器中获取到所有的BeanDefinitionRegistryPostProcessor组件
   - 依次触发所有的postProcessBeanDefinitionRegistry()方法
   - 再来触发postProcessBeanFactory()方法BeanFactoryPostProcessor里的
4. 再来从容器中找到BeanFactoryPostProcessor组件，然后依次触发postProcessBeanFactory()方法

### ApplicationListener

监听容器中发布的事件，事件驱动模型开发

监听ApplicationEvent及其下面的子事件

步骤：

1. 写一个监听器（ApplicationListener实现类）来监听某个事件（ApplicationEvent及其子类）

   @EventListener

   原理：使用EventListenerMethodProcessor处理器来解析方法上的@EventListener

2. 把监听器加入到容器

3. 只要容器中有相关事件发布，我们就能监听到这个事件

   ContextRefreshedEvent：容器刷新完成（所有bean都完全创建）会发布这个事件

   ContextClosedEvent：关闭容器会发布这个事件

4. 发布一个事件：applicationContext.publishEvent();

原理：

ContextRefreshedEvent事件

1. 容器创建对象：refresh()
2. finishRefresh();  容器刷新完成会发布ContextRefreshedEvent事件
3. 自己发布事件
4. 容器关闭会发布ContextClosedEvent

publishEvent(new ContextRefreshedEvent(this));

事件发布流程：

- 获取事件的多播器：getApplicationEventMuticaster()

- muticastEvent 派发事件

- 获取到所有的ApplicationListener

  ```java
  for(final ApplicationListener<?> listener:getApplicationListeners(event,type))
  
  ```

  - 如果有Executor，可以支持使用Executor进行异步派发

    ```java
    Executor executor = getTaskExecutor();
    
    ```

  - 否则，同步的方式直接执行listener方法：invokeListener(listener, event);

    拿到listener回调onApplicationEvent方法

容器中有哪些监听器：

1. 容器创建对象：refresh()

2. initApplicationEventMulticaster(); 初始化ApplicationEventMulticaster

   - 先去容器中找有没有id="applicationEventMulticaster"的组件

   - 如果没有this.applicationEventMulticaster = new SimpleApplicationEventMulticaster(beanFactory)

     并且加入到容器中，我们就可以在其他组件要派发事件，自动注入这个applicationEventMulticaster

SmartInitializingSingleton原理：

1. IOC容器创建对象并refresh()

2. finishBeanFactoryInitialization(beanFactory);  初始化剩下的单实例bean

   - 先创建所有单实例bean，getBean()

   - 获取所有创建好的单实例bean，判断是否是SmartInitializingSingleton类型的

     如果是，就调用afterSingletonsInstantiated();

BeanFactory的创建和预准备工作：

1. prepareRefresh()  刷新前的预处理

   - initPropertySource()  初始化一些属性设置，子类自定义个性化的属性设置方法
   - getEnvironment().validateRequiredProperties();  检验属性的合法等
   - `earlyApplicationEvents = new LinkedHashSet<ApplicationEvent>();`保存容器中的一些早期事件

2. obtainFreshBeanFactory();  获取BeanFactory

   - refreshBeanFactory();  刷新BeanFactory

     创建了一个`this.beanFactory = new DefaultListableBeanFactory();`

     设置id

   - getBeanFactory(); 返回刚才GenericApplicationContext创建的BeanFactory对象

   - 将创建的BeanFactory（DefaultListableBeanFactory）返回

3. prepareBeanFactory(beanFactory);  BeanFactory的预准备工作（BeanFactory进行一些设置）

   - 设置BeanFactory的类加载器，支持表达式解析器
   - 添加部分BeanPostProcessor（ApplicationContextAwareProcessor）
   - 设置忽略的自动装配的接口EnvironmentAware，EmbeddedValueResolverAware
   - 注册可以解析的自动装配：我们能直接在任何组件中自动注入：BeanFactory，ResourceLoader，ApplicationEventPublisher，ApplicationContext
   - 添加BeanPostProcessor（ApplicationListenerDetector）
   - 添加编译时的AspectJ
   - 给BeanFactory中注册一些能用的组件：environment（ConfigurableEnvironment），systemProperties（Map<String, Object>），systemEnvironment（Map<String, Object>）

4. postProcessBeanFactory(beanFactory);  BeanFactory准备工作完成后进行的后置处理工作

   - 子类通过重写这个方法来在BeanFactory创建并预准备完成以后做进一步的设置

### SmartInitializingSingleton

### Spring容器创建过程

## 运行时插件

Shared libraries （共享库）/ runtimes pluggability（运行时插件能力）

1. Servlet容器启动会扫描，当前应用里面每一个jar包

   ServletContainerInitializer的实现

2. 提供ServletContainerInitializer的实现类

   必须绑定在，META-INF/services/javax.servlet.ServletContainerInitializer

   文件的内容就是ServletContainerInitializer实现类的全类名

总结：容器在启动应用的时候，会运行onStartup方法，会扫描当前应用每一个jar包里面

META-INF/services/javax.servlet.ServletContainerInitializer

指定的实现类，启动并运行这个实现类的方法：传入感兴趣的类型

加载这个文件指定的类SpringServletContainerInitializer

1. 使用ServletContext注册Web组件（Servlet，Filter，Listener）

2. 使用编码的方式，在项目启动时给ServletContext里面添加组件

   必须在项目启动的时候来添加

   - ServletContainerInitializer得到的ServletContext对象
   - ServletContextListener得到的ServletContext

3. Spring的应用一启动会加载感兴趣的WebApplicationInitializer接口下的所有组件

4. 并且为WebApplicationInitializer组件创建对象（组件不是接口，不是抽象类）

   - AbstractContextLoaderInitializer：创建根容器，createRootApplicationContext()

   - AbstractDispatcherServletInitializer

     创建一个web的IOC容器：createServletApplicationContext();

     创建了DispatcherServlet：createDispatcherServlet();

     将创建的DispatcherServlet添加到ServletContext中

     getServletMappings();

   - AbstractAnnotationConfigDispatcherServletInitializer：注解方式配置的DispatcherServlet初始化器

     创建根容器：createRootApplicationContext()

     ​		getRootConfigClasses();  传入一个配置类

     创建web的IOC容器：createServletApplicationContext();

     ​		获取配置类：getServletConfigClasses();

总结：以注解方式来启动SpringMVC：继承AbstractAnnotationConfigDispatcherServletInitializer

实现抽象方法指定DispatcherServlet的配置信息

定制SpringMVC：

1. @EnableWebMvc：开启SpringMVC定制配置功能

   `<mvc:annotation-driven/>`

2. 配置组件（视图解析器，视图映射，静态资源映射，拦截器）

   extends WebMvcConfigurerAdapter

## SpringMVC的异步操作

1. 控制器返回Callable
2. Spring异步处理，将Callable提交到TaskExecutor使用一个隔离的线程进行执行
3. DispatcherServlet和所有的Filter退出web容器的线程，但是response保持打开状态
4. Callable返回结果，SpringMVC将请求重新派发给容器，恢复之前的处理
5. 根据Callable返回的结果，SpringMVC继续进行视图渲染流程等（从收请求-视图渲染）