# Spring

## AOP

### 简述
1. 概念：Aspect-Oriented Programming，一般称为面向切面编程。 
2. 目的：用于将那些与业务无关的，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被称为切面（Aspect） 
3. 用途：权限认证、日志、事务处理等，作为面向对象的一种补充 
4. 好处：减少重复代码，提高系统可维护性，降低模块间的耦合度 
5. 原理：使用的是动态代理

### 动态代理

#### JDK动态代理
1. **只提供接口代理，不支持类代理**
2. 运行时生成动态代理类 $proxy.class
3. 代理类实现了目标类接口，并且实现接口类所有方法增强代码
4. 调用时，通过代理类先去调用处理类进行增强，再通过反射方式调用目标类的方法，从而实现AOP

#### CGLIB动态代理
1. **没有实现接口**
2. 通过**ASM**在运行时动态生成目标类的一个**子类**，(还有相关类，主要是为了增强调用时效率)会生成多个
3. 会重写**父类**所有的方法增强代码
4. 调用时，先代理类进行增强，再**直接调用父类对应方法**，从而实现AOP
   1. 类被标记为final，无法使用CGLIB做动态代理的
   2. 除了目标子类代理类，还有个FastClass（路由类），可以（但不是必须）让本类方法调用进行增强，不会像jdk代理
   
> jdk生成类速度快，调用慢，cglib生成类速度慢，调用快。但jdk版本升级优化，cglib止步不前。 jdk性能比cglib好

#### 相关方法
1. 是否是代理对象: ``AopUtils.isAopProxy(AopContext.currentProxy());``
2. 是否是cglib代理对象: ``AopUtils.isCglibProxy(AopContext.currentProxy())``
3. 是否是jdk代理对象: ``AopUtils.isJdkDynamicProxy(AopContext.currentProxy());``

### 相关名词
1. 切面 Aspect 切面类，管理切点和通知
2. 连接点 Join Point 被增强的业务方法
3. 通知 Advice 需要增加到业务方法中的公共代码和逻辑
4. 切点 Pointcut 决定那些方法需要增强，那些不需要。结合切点表达式
5. 目标对象 Target Object 增强的对象
6. 织入 Weaving Aspectj独有的，Spring aop织入方式是动态代理。
   1. 编译器
   2. 类加载期
   3. 运行期
7. 其他
   1. Advisor 顾问。是Pointcut和Advice的一个结合

### AspectJ

#### Spring AOP使用
1. Spring AOP 提供了AspectJ的支持，
2. 但只用到了AspectJ的切点解析和匹配，及@Aspect、@Before等注解
3. 在容器启动时需要生成代理实例，在方法的调用上也会增加栈的深度，使得Spring AOP 性能没有AspectJ那么好

#### AspectJ本身
> AspectJ 是静态代理增强，编译阶段生成AOP代理类，编译时增强。 
> 编译阶段将切面织入到Java字节码中，运行的是增强后的AOP对象
> 使用的话，需要使用AspectJ编译器。 ``xxx.aj; aspect``

## SpringIOC

### 控制反转
1. new实例类，耦合度太高，维护不方便
2. 引入IOC，将创建对象控制权交给了Spring，控制了对象的创建权利，Spring IOC去创建
3. 要使用对象：需要通过DI（依赖注入）@Autowired，从容器中自动注入

### 优点
> 集中管理对象，方便维护，降低耦合度

### 实现机制

#### 工厂模式
``BeanFactory.getBean(String beanName)``
#### 反射
通过反射，实例化创建Bean对象 ``BeanFactory.getBean(String className)``


----



## Bean

### 作用域
作用域：Prototype/Singleton/Request/Session/Global Session

- Prototype：原型模式，每次获取bean都会创建以一个新的实例，因此不存在线程安全的问题。
- Singleton：不同的线程访问同一个bean，如果这个bean中含有实例变量，并且线程具有对实例变量的写操作时，就会产生线程安全问题
  - 解决：使用[ThreadLocal](Concurrence.md)，为每个线程创建独立的变量副本，互相隔离互不影响
- 只要Bean是无状态的则一定是线程安全的

### 加载方式

1. 使用``@Component``注解 和``@ComponentScan``对其进行扫描
2. 在配置类中(``@Configuration``)，使用``@Bean``注解，将方法返回值加载到容器中
3. 类上声明``@Import(User.class)``

----

## 注解

### Autowired

#### required属性
+ **@Autowired(required = true)**
  + 默认是true，表示注入的时候，该bean必须存在，否则会注入失败
+ **@Autowired(required = false)**
  + 忽略当前要注入bean，如果有直接注入，没有跳过，不会报错
+ 容器启动过程中，会初始化bean，spring核心之一（IOC）
+ ~~当前容器不能注入自己，这样就会不停的注入自己，陷入死循环，从而找不到要注入的bean~~
  + Spring框架提供的三级缓存来专门解决循环依赖
  + 构造函数注入会进入死循环

#### 依赖注入方式

##### 一、Field Injection

> **@Autowired**
>
> 通过Java反射机制实现，所以private成员也可以被注入具体的对象
>
> ```java
> @Controller
> public class UserController {
>  @Autowired
>  private UserService userService;
> }
> ```

##### 二、Construction Injection （推荐）

> 通过对象构造的时候建立关系，所以对对象创建的顺序有要求。当然Spring会为你搞定这样的先后顺序，除非出现循环依赖，然后就会抛出异常
>
> ```java
> @Controller
> public class UserController {
>  private final UserService userService;
>  public UserController(UserService userService) {
>      this.userService = userService;
>  }
> }
> ```

##### 三、Setter Injection

> 1. 也会用到@Autowired注解，
> 2. 但使用方式与**Filed Injection**有所不同，Field Injection是用在成员变量上的，而Setter Injection是用在Setter函数上的。
> 3. 就是通过调用成员变量的set方法来注入想要使用的依赖对象
> 4. 相较于 **Field Injection** 测试性更好
>
> ```java
> @Controller
> public class UserController {
>     private UserService userService;
> 
>     @Autowired
>     public void setUserService(UserService userService) {
>         this.userService = userService;
>     }
> }
> ```
>
> 

##### 对比

| 注入方式                  | 可靠性 | 可维护性 | 可测试性 | 灵活性 | 循环关系检测 | 性能影响 |
|-----------------------|:---:|:----:|:----:|:---:|:------:|:----:|
| Field Injection       | 不可靠 |  差   |  差   | 灵活  |  不检测   | 启动快  |
| Constructor Injection | 可靠  |  好   |  好   | 不灵活 |  自动检测  | 启动慢  |
| Setter Injection      | 不可靠 |  差   |  好   | 灵活  |  不检测   | 启动快  |

> 1. 可靠性
>    1. 对象构建和使用过程，看对象在各阶段的使用是否可靠
>    2. 构造函数有严格的构建顺序和不可变性，一旦构建就可用，且不会被更改
> 2. 可维护性
>    1. 更容易阅读，分析依赖关系
>    2. 构造函数中可以明显分析出依赖关系
> 3. 可测试性
>    1. 在复杂的依赖关系下，更容易的编写单元测试来评判
>    2. Constructor Injection和 Setter Injection的方式更容易Mock和注入对象，更容易实现单元测试
> 4. 灵活性
>    1. 开发实现时候的编码灵活
>    2. Constructor Injection对Bean依赖关系有着严格的顺序要求
> 5. 性能影响
>    1. Constructor Injection 严格的顺序要求，会拉长启动时间

### Resource

> javax.annotation.Resource

#### 和Autowire区别

> - Autowire
>   - 默认通过类型注入，如果存在多个类型则通过名称注入
>   - 可以使用@Primary定义类名，优先使用
>   - 使用@Qualifier("xxx")，指定类名注入 （合格者）
> - Resource
>   - 默认使用名称注入，如果名称找不到，则通过类型注入
> - bean默认是开头字母小写的类名

### Transactional

+ 回滚rollbackFor()。默认是RuntimeException
  + IOException不是RuntimeException子类，不会进行事务回滚
```java
@Transactional(rollbackFor = IOException.class)
public class Clazz {
    
}
```

### RequestParam & RequestBody

#### RequestParam
> 接收的参数是来自HTTP请求体或
#### RequestBody


### JavaConfig

注解的方式开发，代替xml 
1. @Configuration 
2. @Bean 
3. @ComponentScan 
4. @EnableWebMvc 
5. @ImportResource 
6. @PropertySource

## 依赖

### spring-boot-starter-parent
1. Java版本
    ```xml
    <properties>
       <java.version>1.8</java.version>
    </properties>
    ```
2. 源码文件编码
    ```xml
    <properties>
      <project.build.sourceEncoding>GBK</project.build.sourceEncoding>
    </properties>
    ```
3. 依赖管理
4. 打包支持
5. 动态识别资源
    ```xml
    <resource>
      <directory>src/main/resources</directory>
      <includes>
        <include>**/*.*</include>
      </includes>
      <filtering>true</filtering>
    </resource>
    ```
6. 识别插件设置。如exec plugin,surefire,Git commit ID等
7. 设别不同的配置。如application-dev.properties和 application-dev.yml

有些继承自其父级spring-boot-dependencies

## 事务

> Spring采用ThreadLocal方式，来保证单个线程中的数据库操作使用的是同一个数据库连接，同时采用这种方式可以使业务层使用数据库时不需要感知并管理connection对象，通过传播级别，巧妙管理多个事务配置间的切换、挂起和恢复。

> **主要用的就是ThreadLocal和AOP实现，**每个线程的链接都是靠ThreadLocal保存
> 底层核心是**动态代理**

### 不生效
1. Bean没有纳入Spring容器管理
   1. （不是动态代理的Bean），里面方法是没有事务管理的

2. 自调用
   1. 调用当前类的方法，比如用this调用当前类的的方法

   2. 原因：this.methodA()，没有走TestService的代理类，所以事务会失效

   3. 解决
      1. methodA()和methodB()分别放到不同的类中

      2. 自己注入自己，用注入的实例调用

      3. 获取动态代理类，调用自己类的方法 且设置暴露当前代理对象到本地线程(``@EnableAspectJAutoProxy(expostProxy=true)``)

         ```java
         @Service
         public class TestService{ 
             
             @Transactional
             public void methodA(){
                 
             }
             public void methodB() {
               ((TestService)AopContext.currentProxy()).methodA();
             }
         } 
         ```

3. 异常没有抛出了，被try catch了

4. 抛出的不是RuntimeException，而且没有指定rollbackFor=异常类型

5. 事务方法不是public的，不会被动态代理

6. 事务方法内启用新线程进行异步操作

7. 数据库不支持事务
### 五种事务隔离级别

| 隔离级别                 | 说明                           |
|----------------------|------------------------------|
| **DEFAULT**          | 使用数据库默认的事务隔离级别               |
| **READ_COMMITTED**   | 读已提交：防止脏读。但会出现不可重复读和幻读       |
| **READ_UNCOMMITTED** | 读未提交：最低隔离级别事务。会产生脏读、不可重复读和幻读 |
| **REPEATABLE_READ**  | 可重复读：可以防止脏读、不可重复读。但会幻读       |
| **SERIALIZABLE**     | 串行化：最高隔离级别最可靠                |

### 七种事务传播行为

| 传播行为              | 说明                       |
|-------------------|--------------------------|
| **REQUIRED**      | 若存在事务，则支持当前事务，否则开启一个新的事务 |
| **SUPPORTS**      |                          |
| **MANDATORY**     |                          |
| **REQUIRES_NEW**  | 总是新开，挂起当前事务              |
| **NOT_SUPPORTED** |                          |
| **NEVER**         |                          |
| **NESTED**        |                          |

---

## Other

### SpringBoot属性加载顺序

> 加载顺序是从高到低

1. 命令行参数，ex: java -jar -Dspring.profile.active
2. 操作系统环境变量
3. application.properties或application.yml
4. @Configuration注解的类，@PropertySource注解的属性