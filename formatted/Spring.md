- 说说常用的 Spring Boot注解,及其实现
    - 1.@ Spring BootApplication注解:这个注解标识了一个 Spring Boot工程,它实际上是另外三个注解的组合,这三个注解是
        - @Spring Bootconfiguration:这个注解实际就是一个@Configuration,表示启动类也是一个配置类
        - @EnableAutoConfiguration:向 Spring容器中导入了一个 Selector,用来加载 Class path下 Spring Factories中所定义的自动配置类,将这些白动载为配置Bean
        - @Componentscan:标识扫路径,因为默认是没有配置实际扫描路径,所以 Spring Boot扫描的路径是启动类所在的当前目录
    - 2.@Bean注解:用来定义Bean,类似于XML中的<bean>标签, Spring在启动时,会对加了@Bean注解的方法进行解析,将方法的名字做为 bean Name,并通过执行方法得到bean对象
    - 3.@Controller、@Service,@ResponseBody,@Autowire都可以说
- spring事务什么时候会失效?
    - 1、bean对象没有被 spring容器管理
    - 2、方法的访问修饰符不是 public
    - 3、自身调用问题
    - 4、数据源没有配置事务管理器
    - 5、数据库不支持事务
    - 6、异常被捕获
    - 7、异常类型错误或者配置错误
- 

# Backlinks
## [CV](<CV.md>)
- ## [Spring](<Spring.md>)

## [July 29th, 2020](<July 29th, 2020.md>)
- [Spring](<Spring.md>)[

## [延迟任务](<延迟任务.md>)
- 使用[Spring](<Spring.md>)[

