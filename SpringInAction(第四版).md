SpringInAction
==============  
# 第一章 Sping之旅  
初步了解Spring DI和AOP  
Dependency Injection(依赖注入)  
AOP 面向切面
# 第二章 装配Bean  
* 声明Bean
* 构造器注入和setter方法注入
* 装备Bean
* 控制Bean的创建和销毁  
### 自动化装配Bean  
组件扫描(component scanning)：spring会自动发现应用上下文所创建的bean  
自动装配(autowiring):spring自动满足bean之间的依赖  
    
* @Component注解表示了改类会作为组件类，并且告知spring为该类创建bean  
* @ConponentScan注解启用了组件扫描，寻找该类包内带有@Component的注解的类并且为其创建bean，也可以通过xml配置开启<context:component-scan base-package="需要扫描的包" />  
* @Autowired自动装配，可用在成员变量注入、set注入、构造器注入。
### 通过java代码装配bean  
* @Configuration注解，表明这个类是一个配置类，该类应该包含在spring应用上下文中如何创建bean的细节  
* @Bean注解，声明bean的时候需要写一个方法，方法创建所需类型的实例，再给这个方法添加上@Bean注解。@Bean注解会告诉spring这个方法会返回一个对象，该对象要注册为spring应用上下文中的bean。方法体包含了最终产出bean实例的逻辑
### 通过xml装配bean  
< bean id="Bean的ID" class="全限定的类名" />
# 第三章 高级装配  
* Spring prifile  
* 条件化的bean声明
* 自动装配与歧义性
* bean的作用域
* spring表达式语言  
### 环境与profile
@Profile注解，指定某个bean属于哪个profile  
激活profile需要配置两个属性：spring.profiles.active和profiles.default  
### 条件化的bean  
@Conditional注解，用于带有@Bean注解的方法上，给定条件计算是否创建这个bean  
设置给@Conditional的类可以是任意实现Conditional接口的类型。@Profile也是基于@Conditional实现的。  
### 处理自动装配的歧义性  
@Primary注解标识装配时的首选bean首选bean的数量不能超过一个  
  
* @Qualifier注解是使用限定符的主要方式。它可以与@Autowired、@Inject协同使用，在注入的时候指定要注入的是哪个bean。  
* @Qualifier在bean上使用添加自定义限定符，和@Component协同使用  
* @Qualifier创建自定义限定符注解，创建一个注解它本身用@Qualifier标注。这样就可以装配的时候增加多个限定符了    
### bean的作用域  
* 单例(singleton)：在整个应用中，只创建bean的一个实例  
* 原型(prototype)：每次注入或通过spring应用上下文获取的时候，都会创建一个新的bean实例  
* 会话(session)：在web应用中为每一个会话创建一个bean实例  
* 请求(request)：在web应用中为每一个请求创建一个bean实例  
@Scope注解，在bean的类上使用，设置bean的作用域，协同@Component使用  
### 运行时值注入  
${}占位符  
 #{}SqEL表达式  
# 第四章 面向切面的Spring  
* 面向切面编程的基本原理
* 通过POJO创建切面
* 使用@AspectJ注解
* 为AspectJ切面注入依赖  
### 什么是面向切面编程  
切点定义了哪些连接点会得到通知  
### 通过切点来选择连接点  
execution()表达式配置切点，表达式中可以通过bean()指定匹配特定的bean  
### 使用注解创建切面  
@Pointuct注解,可以在一个@AspectJ切面内定义可重用的切点  
### 在XML中声明切面  
  
# 第五章 构建Spring Web应用  
* 映射请求到Spring控制器
* 透明的绑定表单参数
* 校验表单提交  
### Spring MVC起步  
![Alt text](/image/image1.png "MVC流程图")  
  
![Alt text](/image/image2.png "MVC流程图")  
  
1. 请求离开浏览器到达DispatcherServlet  
2. DispatcherServlet会查询一个或者多个映射处理器(handler mapping),来确定请求的下一站是那个控制器(Controller)  
3. DispatcherServlet会将请求发送给选中的控制器(Controller)  
4. 控制器(Controller)处理完请求以后，将模型数据打包，并且标示出用于渲染输出的视图名，最后连同模型和视图名发送给DispatcherServlet  
5. DispatcherServlet会使用视图解析器(view resolver)来将逻辑视图名匹配一个特定的视图实现，它可能不是JSP  
6. DispatcherServlet实现视图，交付模型数据
7. 视图使用模型数据渲染输出，传递给客户端  

@Componet通用  
@Repository DAO层  
@Service  Service层  
@Controller Controller层  


 


