## beanfactory和factorybean的区别

> beanfactory本质是factory, 是顶级接口, 规定最基础的IoC容器所需要的方法, ApplicationContext扩展beanFactory的接口, 并在beanFactory的基础上添加了许多功能
>
> factoryBean本质是bean, 该bean是可以生产bean的, 用于生产一些特别的bean

## 什么是依赖注入?

> 依赖注入(Dependency Injection, DI), 不需要手动创建对象, 通过IoC自动装配, 就可以直接使用

## Spring中提供了多少种IoC容器

> Spring提供了两种IoC容器: BeanFactory和ApplicationContext

## IoC的实现机制

> IoC的本质是工厂模式+反射, 通过构造BeanDefinition, 由工厂实例化对象, 再根据BeanDefinition注入属性, 然后一般根据对象类型进行自动装配

## Spring中的注入方式有哪些?

> 构造器注入, setter注入, 接口注入. Spring用的是构造器注入, setter注入

## Spring中有哪些配置方式

> XML配置, @Configuration+@Bean, XML+@Bean三种方式

## Spring支持几种Bean Scope

> singleton: 一个IoC容器只有一个bean实例
>
> prototype: 每次请求都会产生一个新的bean实例(请求创建)
>
> Request: 每个Request都会新建一个新的bean实例
>
> session: 每个session都会新建一个新的bean实例
>
> Application: 每个新的webApplication都会产生一个新的bean实例

## Bean的生命周期

> 1. 实例化bean对象, 容器根据beanDefinition实例化Bean对象, 使用依赖注入填充bean的属性
>
>    在这里要注意: 还有一个InstantiationAwareBeanPostProcessor, 在实例化前后做操作
>
> 2. 如果bean实现了Aware接口, 要把Aware中设置的属性注入到Bean对象中, aware方法都是在初始化阶段之前调用的
>
>    - 如果Bean实现了BeanNameAware, 则工厂会调用BeanNameAware中的setBeanName()方法, 设置BeanName
>    - 如果Bean实现了BeanFactoryAware, 则工厂会把自身factory对象注入到Bean中, 后面可以通过Bean来获取factory, 然后再从这个factory中getBean()
>    - 如果Bean实现了ApplicationContextAware, 则工厂会注入ApplicationContext
>
> 3. Bean初始化, 调用相应的方法, 进一步初始化Bean对象
>
>    - 如果Bean关联了BeanPostProcessor, 则调用preProcessBeforeInitialization(Object bean, String beanName)方法
>    - 如果对象实现了init()方法, 执行对应的初始化方法
>    - 如果Bean实现了InitializingBean接口, 则会调用`afterPropertiesSet()` 方法
>
> 4. Bean可以对外装配使用了
>
> 5. Bean的销毁
>
>    Bean在容器关闭的时候会进行销毁, 如果bean指定了destroy()方法, 则会执行相应的destroy()方法

## Spring的延迟加载

> 在使用ApplicationContext这个工厂时, 默认是实时加载, 即在Spring启动时, IoC把所有的Bean对象实例化完成. 如果使用BeanFactory容器, 则会使用延时加载, 即要使用该Bean的时候, 才进行实例化, 也可以通过配置来设置延时加载

## Spring中的单例Bean是线程安全的吗?

> Spring中的单例并不是线程安全的, 但是Spring中的单例大部分是没有状态的, 所以一般不存在共享资源的问题, 如果有共享资源, 就需要开发者去保护, 如: JDBC连接的获取和使用, 使用ThreadLocal来维护

## Spring如何解决循环依赖?

> 三级缓存

## Spring常用注解

> - @Bean, @Component, @Service, @Repository, @Controller有什么不一样
>
>   @Bean: 显式标明这是一个Bean, 如果要使用第三方的类, 手上没有源码, 就可以手动实例化一个, 用@Bean显式声明, 放入容器
>
>   @Component: 用于自动检测扫描去配置bean, 声明作用比较大
>
>   @Service, @Repository, @Controller: 都是@Component的包装, 分别声明一个是业务层的Bean, 持久层的Bean, 表现层的Bean

## SpringMVC工作组件

| 组件              | 说明                                                         |
| :---------------- | :----------------------------------------------------------- |
| DispatcherServlet | Spring MVC 的核心组件，是请求的入口，负责协调各个组件工作    |
| HandlerMapping    | 内部维护了一些 <访问路径, 处理器> 映射，负责为请求找到合适的处理器 |
| HandlerAdapter    | 处理器的适配器。Spring 中的处理器的实现多变，比如用户处理器可以实现 Controller 接口，也可以用 @RequestMapping 注解将方法作为一个处理器等，这就导致 Spring 不知道怎么调用用户的处理器逻辑。所以这里需要一个处理器适配器，由处理器适配器去调用处理器的逻辑 |
| ViewResolver      | 视图解析器的用途不难理解，用于将视图名称解析为视图对象 View。 |
| View              | 视图对象用于将模板渲染成 html 或其他类型的文件。比如 InternalResourceView 可将 jsp 渲染成 html。 |

handlerMapping返回的是HandlerExecutionChain对象, 里面包含着一堆拦截器和handler对象,

通过HandlerAdapter执行HandlerExecutionChain里的方法,  一般先执行preHandle, 执行拦截器, 如果拦截都通过了, 再执行handler, handler里包装着Controller类和请求方法的处理方法