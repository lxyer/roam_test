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
- SpringMVC 执行流程
    - 用户向服务器发送请求，请求被Spring前端控制Servelt DispatcherServlet捕获；
    - DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI）。然后根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及Handler对象对应的拦截器），最后以HandlerExecutionChain对象的形式返回；
    - DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。（附注：如果成功获得HandlerAdapter后，此时将开始执行拦截器的preHandler(…)方法）
    - 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)。 在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：
    - HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息
    - 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等
    - 数据根式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等
    - 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中
    - Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象；
    - 根据返回的ModelAndView，选择一个适合的ViewResolver（必须是已经注册到Spring容器中的ViewResolver)返回给DispatcherServlet ；
    - ViewResolver 结合Model和View，来渲染视图
    - 将渲染结果返回给客户端。

# Backlinks
## [CV](<CV.md>)
- ## [Spring](<Spring.md>)

## [July 29th, 2020](<July 29th, 2020.md>)
- [Spring](<Spring.md>)[

## [延迟任务](<延迟任务.md>)
- 使用[Spring](<Spring.md>)[

