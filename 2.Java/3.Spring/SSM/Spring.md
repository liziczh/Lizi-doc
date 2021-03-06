# Spring

Spring IOC（控制反转） 和 AOP（面向切面编程）。

IOC：

- 依赖查询（DL）
- 依赖注入（DI）

AOP：面向切面编程，横向

## Spring 与 IOC

IOC（Inversion of Control，控制反转）

### 引入依赖

- 普通 Project 引入 jar 包：`spring-core`，`spring-context`，`spring-beans`，`spring-expression`，`commons-loging`，log4j，junit。
- Maven Project 引入依赖：spring-context，commons-loging（日志规范），log4j（日志实现），junit。

Maven **pom.xml**：

```xml
<dependencies>
    <!--Spring框架-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.3.18.RELEASE</version>
    </dependency>
    <!--引入Spring日志规范-->
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.2</version>
    </dependency>
    <!--引入日志实现log4j-->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    <!--引入spring的测试-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>4.3.18.RELEASE</version>
        <scope>test</scope>
    </dependency>
    <!--引入单元测试-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**log4j.properties**：

```properties
log4j.rootCategory=INFO, stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %t %c{2}:%L - %m%n
log4j.category.org.springframework.beans.factory=DEBUG
```

### Spring 配置文件

**spring-cfg.xml**：

多个配置文件：一个主配置文件，多个从配置文件；

```xml
<import resource="spring-*-cfg.xml"></import>
```

### Bean 装配

#### 默认装配

使用无参构造。

```xml
<bean id="student" class="com.lizi.pojo.Student" scope="singleton"></bean>
```

#### 动态工厂

**spring-config.xml**：指定 `factory-bean` 和 `factory-method`；

```xml
<bean id="studentFac" class="com.lizi.pojo.StudentFactory"></bean>
<bean id="student" factory-bean="studentFac" factory-method="getStudent" ></bean>
```

#### 静态工厂

**spring-config.xml**：指定 `class` 和 `factory-method`；

```xml
<bean id="student" class="com.lizi.pojo.StudentFactory" factory-method="getStudent"></bean>
```

#### Bean 作用域

**scope**：

1. singleton：单例模式。即在整个Spring容器中，使用singleton定义的Bean将是单例的，只有一个实例。默认为单态的。
2. prototype：原型模式。即每次使用getBean方法获取的同一个的实例都是一个新的实例。
3. request：对于每次HTTP请求，都将会产生一个不同的Bean实例。
4. session：对于每个不同的HTTP session，都将产生一个不同的Bean实例。
5. global session：每个全局的HTTP session对应一个Bean实例。典型情况下，仅在使用portlet 集群时有效，多个Web应用共享一个session。一般应用中，global-session与session是等同的。

### 基于 XML 的 DI

> - `<bean>`：Java Bean
>   - id：Bean 的唯一标识；
>   - name：Bean 的唯一名称，不可与 id 相同；
>   - class：Bean 的全类名；
>   - scope：Bean 的作用域；
>   - factory-bean：工厂 Bean；
>   - factory-method：调用静态工厂方法创建实例；
>   - init-method：Bean 初始化方法；
>   - destory-method：Bean 销毁方法；
>   - autowire：自动装配方式；
>     - byName：根据属性名自动装配
>     - byType：如果容器中存在一个与指定属性类型相同的bean，则自动装配；如果存在多个相同的bean，则抛出异常；如果不存在，则什么也不发生。
> - `<property>`：属性
> - `<constructor-arg>`：构造参数
>   - name：属性名
>   - value：属性值
>   - ref：属性引用

#### 属性注入

属性注入：使用 setter() 注入；

```xml
<!--属性注入：使用 setter() 注入；-->
<bean id="student" class="org.lanqiao.pojo.Student" scope="prototype">
    <property name="sid" value="1"></property>
    <property name="sname" value="张三"></property>
    <property name="age" value="21"></property>
    <property name="school" ref="school"></property>
</bean>
<bean id="school" class="org.lanqiao.pojo.School">
    <property name="schoolName" value="太原师范学院"></property>
</bean>
```

#### 构造注入

构造注入：使用 constructor 注入；

```xml
<!--构造注入：使用 constructor 注入；-->
<bean id="student2" class="com.lizi.pojo.Student" >
    <constructor-arg name="id" value="2"></constructor-arg>
    <constructor-arg name="name" value="zhangsna"></constructor-arg>
    <constructor-arg name="sex" value="男"></constructor-arg>
    <constructor-arg name="age" value="12"></constructor-arg>
    <constructor-arg name="school" ref="school2"></constructor-arg>
</bean>
<bean id="school2" class="com.lizi.pojo.School">
    <constructor-arg name="id" value="1"></constructor-arg>
    <constructor-arg name="schoolName" value="太原师范学院"></constructor-arg>
</bean>
```
#### 集合注入

```xml
<!--集合注入：List，Set，Map，Array-->
<property name="courses">
    <list>
        <value>语文</value>
        <value>数学</value>
        <value>英语</value>
    </list>
</property>
<property name="teachers">
    <set>
        <value>陈老师</value>
        <value>张老师</value>
        <value>王老师</value>
    </set>
</property>
<property name="scores">
    <map>
         <entry key="语文" value="59"></entry>
         <entry key="数学" value="89"></entry>
         <entry key="英语" value="120"></entry>
    </map>
</property>
```

#### 命名空间注入

**p 命名空间注入**：属性注入（p=property）；

```xml
<!--xml 文件头部设置-->
xmlns:p="http://www.springframework.org/schema/p"
```

```xml
<bean id="p-namespace" class="com.lizi.pojo.School" p:id="12" p:schoolName="TNU" />
```

**c 命名空间注入**：构造注入（c=constructor）;

```xml
<!--xml 文件头部设置-->
xmlns:c="http://www.springframework.org/schema/c"
```

```xml
<bean id="c-namespace" class="com.lizi.pojo.School" c:id="12" c:schoolName="TNU" />
```

#### 自动装配-autowire

`byName`：根据属性名自动装配；

```xml
<bean id="student" name="student" class="com.lizi.pojo.Student" autowire="byName">
   <property name="id" value="1"></property>
   <property name="name" value="Tom"></property>
</bean>
```

`byType`：如果容器中存在一个与指定属性类型相同的bean，则自动装配；如果存在多个相同的bean，则抛出异常；如果不存在，则什么也不发生；

```xml
<bean id="student" name="student" class="com.lizi.pojo.Student" autowire="byType">
   <property name="id" value="1"></property>
        <property name="name" value="张三"></property>
        <property name="sex" value="男"></property>
        <property name="age" value="12"></property>
        <property name="school" ref="school1"></property>
</bean>
```

#### SpEL

```xml
<bean id="student4" class="com.lizi.pojo.Student" autowire="byName">
    <property name="id" value="1"></property>
    <property name="name" value="#{'tom'.toUpperCase()"></property>
    <property name="sex" value="男"></property>
    <property name="age" value="#{T(Math).random()*100}"></property>
</bean>
```

#### 内部 Bean

```xml
<bean id="student4" class="com.lizi.pojo.Student" autowire="byName">
    <property name="id" value="1"></property>
    <property name="name" value="张三"></property>
    <property name="school">
        <bean class="com.lizi.pojo.School">
            <property name="schoolName" value="TNT"></property>
        </bean>
     </property>
</bean>
```

### 基于注解的 DI

`@Component` - 标注当前类为 Spring 的一个组件；

`@Respository` - 标注RespositoryImpl/DaoImpl；

`@Service` - 标注Service；

`@Controller` - 标注Controller/Action；

`@Scope` - 指定 Bean 作用域；默认singleton

`@Autowired` - 标注自动装配方式，默认byType；

`@Qualifier` - 指定自动装载的bean的name；

`@Value` - 基本类型属性注入；

`@Resource` - 域属性注解；

**spring-*-cfg.xml**：

（1）引入 context 命名空间；

```xml
<!--xml 头部-->
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
    <!--启用组件自动扫描-->
    <context:component-scan base-package="com.lizi.pojo"></context:component-scan>
</beans>
```

#### Bean 的定义：@Component

`@Component` - 标注当前类为 Spring 的一个组件；

`@Respository` - 标注RespositoryImpl/DaoImpl；

`@Service` - 标注Service；

`@Controller` - 标注Controller/Action；

#### Bean 的作用域：@Scope

`@Scope` - 指定 Bean 作用域；默认 `singleton`；

```java
@Scope("prototype")
```

#### 按类型自动装配：@Autowired

```java
@Autowired
```

#### 按名称自动装配：@Autowired和@Qualifier

```java
@Autowired
@Qualifier("myStudent")
```


#### 基本类型属性注入：@Value

`@Value` - 基本类型属性注入；可用于属性&setter()方法上。

```java
@Value("1")
```

### Spring 测试依赖

```xml
<!--引入spring测试依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.3.18.RELEASE</version>
    <scope>test</scope>
</dependency>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring-annotation-cfg.xml"})
```

`@Autowired` 自动装配一个对象，测试调用即可。

## Spring 与 AOP

AOP（Aspect Orient Programming，面向切面编程）。

AOP 底层是使用动态代理模式，将交叉业务逻辑织入到主业务逻辑中。

### AOP 术语

（1）**切面（Aspect）**：泛指**交叉业务逻辑**。比如事务处理、日志处理等。常用的切面有通知与顾问，用于增强主业务逻辑。

（2）**织入（Weaving）**：指将交叉业务逻辑插入主业务逻辑的过程。

（3）**连接点（JoinPoint）**：指被切面织入的方法，通常业务接口中的方法均为连接点。

（4）**切入点（Pointcut）**：指切面具体织入的方法。

（5）**目标对象（Target）**：指将要被增强的对象。

（6）**通知（Advice）**：通知是切面的一种实现，完成简单织入功能。通知定义了增强代码切入到目标代码的时间点，是目标方法执行之前执行，还是之后执行等。通知类型不同，切入时间不同。 切入点定义切入的位置，通知定义切入的时间。 

（7）**顾问（Advisor）**：顾问是切面的另一种实现，能够将通知以更为复杂的方式织入到目标对象中，是将通知包装为更复杂切面的装配器。 

### Spring AOP 规则

Spring代理规则：

1、默认使用Java动态代理来创建AOP代理，这样就可以为任何接口实例创建代理了

2、当需要代理的类不是代理接口的时候，Spring会切换为使用CGLIB代理，也可强制使用CGLIB

AOP编程：

1、定义普通业务组件

2、定义切入点，一个切入点可能横切多个业务组件

3、定义增强处理，增强处理就是在AOP框架为普通业务组件织入的处理动作

### 相关依赖与配置

Maven **pom.xml**：

```xml
<!--spring 切面编程-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>4.3.18.RELEASE</version>
</dependency>
```

**spring-*-cfg.xml**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
```

### 基于 XML 的 AOP 实现

Spring 使用 AspectJ 实现 AOP。

#### AspectJ 五种通知类型

AspectJ中常用的通知有五种类型：

`@Before`：前置通知, 在方法执行之前执行

`@After`： 后置通知（最终通知）, 在方法执行之后执行

`@After-Running`：返回通知, 在方法返回结果之后执行

`@After-Throwing`：异常通知, 在方法抛出异常之后

`@Around`：环绕通知, 围绕着方法执行

#### AspectJ 切入点表达式

```java
execution (
[modifiers-pattern] 访问权限类型 
ret-type-pattern 返回值类型 
[declaring-type-pattern] 全限定性类名 
name-pattern(param-pattern) 方法名(参数名)
throws-pattern] 抛出异常类型 
) 
```

#### AOP 配置

```xml
<!--配置AOP-->
<aop:config>
    <!--定义切入点-->
    <aop:pointcut id="doSome" expression="execution(*  *..IStudentService.doSome(..))"></aop:pointcut>
    <aop:pointcut id="doOther" expression="execution(*  *..IStudentService.doOther(..))"></aop:pointcut>
     <!--定义切面-->
     <aop:aspect ref="myAspect">
         <!--前置通知-->
         <aop:before method="recodeLog" pointcut-ref="doSome"></aop:before>
         <!--后置通知-->
         <aop:after method="doTx" pointcut-ref="doSome"></aop:after>
         <!--返回通知-->
         <aop:after-returning method="returnDo" pointcut-ref="doSome" returning="result"></aop:after-returning>
         <!--异常通知-->
         <aop:after-throwing method="exceptionDo" pointcut-ref="doSome"  throwing="ex"></aop:after-throwing>
         <!--环绕通知-->
         <aop:around method="roundDo" pointcut-ref="doSome"></aop:around>
    </aop:aspect>
</aop:config>
```

### 基于注解的 AOP 实现

**spring-*-cfg.xml**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--启用组件自动扫描-->
    <context:component-scan base-package="com.lizi.service"></context:component-scan>
    <!--启用切面自动代理-->
    <aop:aspectj-autoproxy />
</beans>
```

```
@Aspect：定义切面
```

```java
@Before("execution(*  *..IStudentService.doSome(..))")
```

```java
@After("execution(*  *..IStudentService.doSome(..))")
```

```java
@AfterReturning(value = "execution(* *..IStudentService.doSome(..))",returning = "result")
```

```java
@Around("execution(* *..IStudentService.doSome(..))")
```

```java
@AfterThrowing(value = "execution(* *..IStudentService.doSome(..))" ,throwing = "ex")
```



## Spring 与 DAO

### Spring JDBC 模板

Spring JdbcTemplate 模板

#### 相关依赖

```xml
<!--Spring JDBC-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>4.3.18.RELEASE</version>
</dependency>
```





### Spring 事务管理

编程式事务管理： 编程式事务管理使用TransactionTemplate或者直接使用底层的PlatformTransactionManager。

声明式事务管理： 建立在AOP之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。 声明式事务管理不需要入侵代码，通过@Transactional就可以进行事务操作，更快捷而且简单。推荐使用

#### 相关依赖

```xml
<!--Spring Tx-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>4.3.18.RELEASE</version>
</dependency>
```

事务隔离级别：

- `DEFAULT` ：这是默认值，表示使用底层数据库的默认隔离级别。对大部分数据库而言，通常这值就是： `READ_COMMITTED` 。
- `READ_UNCOMMITTED` ：该隔离级别表示一个事务可以读取另一个事务修改但还没有提交的数据。该级别不能防止脏读和不可重复读，因此很少使用该隔离级别。
- `READ_COMMITTED` ：该隔离级别表示一个事务只能读取另一个事务已经提交的数据。该级别可以防止脏读，这也是大多数情况下的推荐值。
- `REPEATABLE_READ` ：该隔离级别表示一个事务在整个过程中可以多次重复执行某个查询，并且每次返回的记录都相同。即使在多次查询之间有新增的数据满足该查询，这些新增的记录也会被忽略。该级别可以防止脏读和不可重复读。
- `SERIALIZABLE` ：所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

事务传播行为：

- `REQUIRED` ：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
- `SUPPORTS` ：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- `MANDATORY` ：如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
- `REQUIRES_NEW` ：创建一个新的事务，如果当前存在事务，则把当前事务挂起。
- `NOT_SUPPORTED` ：以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- `NEVER` ：以非事务方式运行，如果当前存在事务，则抛出异常。
- `NESTED` ：如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于 `REQUIRED` 。

## Spring 与 MyBatis 

基本思路：在spring中注册SqlSessionFactory

相关依赖：mybatis-spring

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis-spring</artifactId>
  <version>x.x.x</version>
</dependency>
```

**mybatis-config.xml**：

```xml
<!--别名：使用简单类名-->
<typeAliases>
    <package name="com.group3.bookstore.mapper"></package>
</typeAliases>
<!--映射文件：Mapper动态代理，映射接口-->
<mappers>
     <package name="com.group3.bookstore.mapper"></package>
</mappers>
```

**spring-config.xml**：

配置数据源：

```
<context:property-placeholder location="classpath:jdbc.properties"/>
<bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="${driver}"/>
    <property name="url" value="${url}"/>
    <property name="username" value="${username}"/>
    <property name="password" value="${password}"/>
 </bean>
```

定义 SqlSessionFactory 类：

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <!--注入数据源-->
    <property name="dataSource" ref="datasource"/>
    <!--注入mybatis-config.xml-->
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
</bean>
```

Mapper动态代理：

```xml
<bean id="mapperFactoryBean"  class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!--注入SqlSessionFactory-->
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    <!--Mapper动态扫描-->
    <property name="basePackage" value="com.group3.bookstore.mapper"/>
</bean>
```

## Spring 日志框架

| 日志-抽象层                                      | 日志-实现层                                 |
| ------------------------------------------------ | ------------------------------------------- |
| JCL(Jakarta Commons logging),SLF4J,Jboss logging | Log4j,Log4j2,Logback,JUL(java.util.logging) |

其中 SLF4j，Log4j，Logback均为一人所写，Logback是Log4j的升级版，而Log4j2则是Apache借用Log4j的名出的日志框架。

每一个日志的实现框架都有自己的配置文件。日志配置文件还是做成日志实现框架自己本身的配置文件。

Spring框架默认使用（commons-logging）即JCL，Hibernate框架默认使用（jboss-logging）。

















