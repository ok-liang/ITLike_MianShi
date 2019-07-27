## 1、在Spring框架中，如何解决Web页面乱码问题?

 > 在web.xml中添加如下配置
 
``` <!--解决浏览器乱码问题-->
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>

<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>


```
如果上面的配置还不可以，在springmvc.xml中添加配置如下

```
<mvc:annotation-driven>
    <mvc:message-converters>
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes">
                <list>
                    <!--<span style="white-space:pre"></span>-->
                    <value>text/html;charset=UTF-8</value>
                    <value>application/json;charset=UTF-8</value>
                    <value>*/*;charset=UTF-8</value>
                </list>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>

```

## 2、 什么是依赖注入？什么是控制反转(IOC)？ 在 Spring 中，有几种依赖注入方式？

```
// 面试回答什么是IOC？
 1-没有IOC的话，每一个服务依赖另外一个服务的话只有两种方法：  
  1种是自己在构造方法new出来
  第2种是构建这个对象时用另外一个对象把他构建出来，然后传过去  
  无论哪一种方式，都会导致服务类的依赖关系复杂、混乱，
  初始化也混乱
 2-有IOC容器，通过容器自动的将这些服务Bean管理。
 然后通过声明的方式，将Bean注入到另外一个服务中  
  核心技术：
   1.XML技术，将Bean进行定义解析  
   2.反射技术，通过反射将bean从容器反射到另外一个服务中  
   3.模式(抽象工厂模式)

```

**DI依赖注入**

IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的。比如对象A需要操作数据库，以前我们总是要在A中自己编写代码来获得一个Connection对象，有了 spring我们就只需要告诉spring，A中需要一个Connection，至于这个Connection怎么构造，何时构造，A不需要知道。在系统运行时，spring会在适当的时候制造一个Connection，然后像打针一样，注射到A当中，这样就完成了对各个对象之间关系的控制。A需要依赖 Connection才能正常运行，而这个Connection是由spring注入到A中的，依赖注入的名字就这么来的。那么DI是如何实现的呢？ Java 1.3之后一个重要特征是反射（reflection），它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，spring就是通过反射来实现注入的。
　　理解了IoC和DI的概念后，一切都将变得简单明了，剩下的工作只是在spring的框架中堆积木而已。

**IOC 控制反转**

首先想说说IoC（Inversion of Control，控制反转）。这是spring的核心，贯穿始终。所谓IoC，对于spring框架来说，就是由spring来负责控制对象的生命周期和对象间的关系。这是什么意思呢，举个简单的例子，我们是如何找女朋友的？常见的情况是，我们到处去看哪里有长得漂亮身材又好的mm，然后打听她们的兴趣爱好、qq号、电话号、ip号、iq号………，想办法认识她们，投其所好送其所要，然后嘿嘿……这个过程是复杂深奥的，我们必须自己设计和面对每个环节。传统的程序开发也是如此，在一个对象中，如果要使用另外的对象，就必须得到它（自己new一个，或者从JNDI中查询一个），使用完之后还要将对象销毁（比如Connection等），对象始终会和其他的接口或类藕合起来。

　　那么IoC是如何做的呢？有点像通过婚介找女朋友，在我和女朋友之间引入了一个第三者：婚姻介绍所。婚介管理了很多男男女女的资料，我可以向婚介提出一个列表，告诉它我想找个什么样的女朋友，比如长得像李嘉欣，身材像林熙雷，唱歌像周杰伦，速度像卡洛斯，技术像齐达内之类的，然后婚介就会按照我们的要求，提供一个mm，我们只需要去和她谈恋爱、结婚就行了。简单明了，如果婚介给我们的人选不符合要求，我们就会抛出异常。整个过程不再由我自己控制，而是有婚介这样一个类似容器的机构来控制。Spring所倡导的开发方式就是如此，所有的类都会在spring容器中登记，告诉spring你是个什么东西，你需要什么东西，然后spring会在系统运行到适当的时候，把你要的东西主动给你，同时也把你交给其他需要你的东西。所有的类的创建、销毁都由 spring来控制，也就是说控制对象生存周期的不再是引用它的对象，而是spring。对于某个具体的对象而言，以前是它控制其他对象，现在是所有对象都被spring控制，所以这叫控制反转。

**四种注入方式**

<font color = "red">set注入</font>

通过service类中的getter方法，把UserDao对象作为参数传入

```
public class UserService {
	private UserDao userDao;
	public UserService() {
		super();
	}
	//通过set注入
	public void setUserDao(UserDao userDao) {
		this.userDao = userDao;
	}
	public void saveService(){
		userDao.saveDao();
	}
}

```

设置xml文件

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
  	<!-- set注入 -->
  	<bean id="userService" class="com. mark.service.UserService">
  		<property name="userDao" ref="userDao"></property>
  	</bean>
    <bean id="userDao" class="com.mark.dao.UserDao"></bean> 
</beans>

```
<font color = "red">构造器注入</font>

创建好service后，带构造参数，把userDao注入其中

```
	public UserService(UserDao userDao) {
		super();
		this.userDao = userDao;
	}
```

配置xml文件

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
  
	<!-- 构造器注入 -->
	<!-- <bean id="userService" class="com.shuaiwen.service.UserService">
		<constructor-arg ref="userDao"></constructor-arg>
	</bean>
	<bean id="userDao" class="com.shuaiwen.dao.UserDao"></bean> -->

</beans>

```

<font color = "red">静态工厂的方法注入</font>

静态工厂顾名思义，就是通过调用金泰工厂的方法来获取自己需要的对象，为了让spring管理所有对象，我们不能直接通过"工程类.静态方法()"来获取对象，而是依然通过spring注入的形式获取。


```

package com.bless.springdemo.factory;  
  
import com.bless.springdemo.dao.FactoryDao;  
import com.bless.springdemo.dao.impl.FactoryDaoImpl;  
import com.bless.springdemo.dao.impl.StaticFacotryDaoImpl;  
  
public class DaoFactory {  
    //静态工厂  
    public static final FactoryDao getStaticFactoryDaoImpl(){  
        return new StaticFacotryDaoImpl();  
    }  
}  

-------------------------------------------------
 public class SpringAction {  
        //注入对象  
    private FactoryDao staticFactoryDao;  
      
    public void staticFactoryOk(){  
        staticFactoryDao.saveFactory();  
    }  
    //注入对象的set方法  
    public void setStaticFactoryDao(FactoryDao staticFactoryDao) {  
        this.staticFactoryDao = staticFactoryDao;  
    }  
}  



```
Spring的IOC配置文件，注意看<bean name="staticFactoryDao">指向的class并不是FactoryDao的实现类，而是指向静态工厂DaoFactory，并且配置 factory-method="getStaticFactoryDaoImpl"指定调用哪个工厂方法

```
<!--配置bean,配置后该类由spring管理-->  
    <bean name="springAction" class="com.bless.springdemo.action.SpringAction" >  
        <!--(3)使用静态工厂的方法注入对象,对应下面的配置文件(3)-->  
        <property name="staticFactoryDao" ref="staticFactoryDao"></property>  
                </property>  
    </bean>  
    <!--(3)此处获取对象的方式是从工厂类中获取静态方法-->  
    <bean name="staticFactoryDao" class="com.bless.springdemo.factory.DaoFactory" factory-method="getStaticFactoryDaoImpl"></bean>  

```

<font color = "red">实例工厂的方法注入</font>

实例工厂的意思是获取对象实例的方法不是静态的，所以你需要首先new工厂类，再调用普通的实例方法：

```
public class DaoFactory {  
    //实例工厂  
    public FactoryDao getFactoryDaoImpl(){  
        return new FactoryDaoImpl();  
    }  
}  

```
需要通过实例工厂创建factoryDao对象

```
public class SpringAction {  
    //注入对象  
    private FactoryDao factoryDao;  
      
    public void factoryOk(){  
        factoryDao.saveFactory();  
    }  
  
    public void setFactoryDao(FactoryDao factoryDao) {  
        this.factoryDao = factoryDao;  
    }  
}
```
 
xml文件

```
<!--配置bean,配置后该类由spring管理-->  
    <bean name="springAction" class="com.bless.springdemo.action.SpringAction">  
        <!--(4)使用实例工厂的方法注入对象,对应下面的配置文件(4)-->  
        <property name="factoryDao" ref="factoryDao"></property>  
    </bean>  
      
    <!--(4)此处获取对象的方式是从工厂类中获取实例方法-->  
    <bean name="daoFactory" class="com.bless.springdemo.factory.DaoFactory"></bean>  
    <bean name="factoryDao" factory-bean="daoFactory" factory-method="getFactoryDaoImpl"></bean> 


```


<font color = "red">基于注解方式注入</font>

> 在介绍注解注入的方式前，先简单了解bean的一个属性autowire，autowire主要有三个属性值：constructor，byName，byType。

+ constructor：通过构造方法进行自动注入，spring会匹配与构造方法参数类型一致的bean进行注入，如果有一个多参数的构造方法，一个只有一个参数的构造方法，在容器中查找到多个匹配多参数构造方法的bean，那么spring会优先将bean注入到多参数的构造方法中。

+ byName：被注入bean的id名必须与set方法后半截匹配，并且id名称的第一个单词首字母必须小写，这一点与手动set注入有点不同。

+ byType：查找所有的set方法，将符合符合参数类型的bean注入。


主要有四种注解可以注册bean，每种注解可以任意使用，只是语义上有所差异：

- @Component：可以用于注册所有bean
- @Repository：主要用于注册dao层的bean
- @Controller：主要用于注册控制层的bean
- @Service：主要用于注册服务层的bean
**描述依赖关系主要有两种：**

+ @Resource：java的注解，默认以byName的方式去匹配与属性名相同的bean的id，如果没有找到就会以byType的方式查找，如果byType查找到多个的话，使用@Qualifier注解（spring注解）指定某个具体名称的bean。

```
@Resource
@Qualifier("userDaoMyBatis")
private IUserDao userDao;

public UserService(){
	
}

```
@Autowired：spring注解，默认是以byType的方式去匹配类型相同的bean，如果只匹配到一个，那么就直接注入该bean，无论要注入的 bean 的 name 是什么；如果匹配到多个，就会调用 DefaultListableBeanFactory 的 determineAutowireCandidate 方法来决定具体注入哪个bean。determineAutowireCandidate 方法的内容如下

```
// candidateBeans 为上一步通过类型匹配到的多个bean，该 Map 中至少有两个元素。
protected String determineAutowireCandidate(Map<String, Object> candidateBeans, DependencyDescriptor descriptor) {
    //  requiredType 为匹配到的接口的类型
   Class<?> requiredType = descriptor.getDependencyType();
   // 1. 先找 Bean 上有@Primary 注解的，有则直接返回
   String primaryCandidate = this.determinePrimaryCandidate(candidateBeans, requiredType);
   if (primaryCandidate != null) {
       return primaryCandidate;
   } else {
       // 2.再找 Bean 上有 @Order，@PriorityOrder 注解的，有则返回
       String priorityCandidate = this.determineHighestPriorityCandidate(candidateBeans, requiredType);
       if (priorityCandidate != null) {
           return priorityCandidate;
       } else {
           Iterator var6 = candidateBeans.entrySet().iterator();

           String candidateBeanName;
           Object beanInstance;
           do {
               if (!var6.hasNext()) {
                   return null;
               }

               // 3. 再找 bean 的名称匹配的
               Entry<String, Object> entry = (Entry)var6.next();
               candidateBeanName = (String)entry.getKey();
               beanInstance = entry.getValue();
           } while(!this.resolvableDependencies.values().contains(beanInstance) && !this.matchesBeanName(candidateBeanName, descriptor.getDependencyName()));

           return candidateBeanName;
       }
   }
}

```

determineAutowireCandidate 方法的逻辑是：

先找 Bean 上有@Primary 注解的，有则直接返回 bean 的 name。
再找 Bean 上有 @Order，@PriorityOrder 注解的，有则返回 bean 的 name。
最后再以名称匹配（ByName）的方式去查找相匹配的 bean。
可以简单的理解为先以 ByType 的方式去匹配，如果匹配到了多个再以 ByName 的方式去匹配，找到了对应的 bean 就去注入，没找到就抛出异常。

还有一点要注意：如果使用了 @Qualifier 注解，那么当自动装配匹配到多个 bean 的时候就不会进入 determineAutowireCandidate 方法（亲测），而是直接查找与 @Qualifer 指定的 bean name 相同的 bean 去注入，找到了就直接注入，没有找到则抛出异常。

>  tips：大家如果认真思考可能会发现 ByName 的注入方式和 @Qualifier 有点类似，都是在自动装配匹配到多个 bean 的时候，指定一个具体的 bean，那它们有什么不同呢？


ByName 的方式需要遍历，@Qualifier 直接一次定位。在匹配到多个 bean 的情况下，使用 @Qualifier 来指明具体装配的 bean 效率会更高一下。

博主个人觉得：@Qualifer 注解出现的意义或许就是 Spring 为了解决 JDK 自带的 ByName 遍历匹配效率低下的问题。要不然也不会出现两个容易混淆的匹配方式。

 ​​​​​​​平常的java开发中，程序员在某个类中需要依赖其它类的方法，则通常是new一个依赖类再调用类实例的方法，这种开发存在的问题是new的类实例不好统一管理，spring提出了依赖注入的思想，即依赖类不由程序员实例化，而是通过spring容器帮我们new指定实例并且将实例注入到需要该对象的类中。依赖注入的另一种说法是“控制反转”，通俗的理解是：平常我们new一个实例，这个实例的控制权是我们程序员，而控制反转是指new实例工作不由我们程序员来做而是交给spring容器来做。

## 3、 在 Spring 中，有几种配置 Bean 的方式？
> 三种<基于XML的配置方式/基于注解的配置方式/基于java类的配置方式>


![](image/Xnip2019-02-10_12-37-44.png)

## 4、 请解释一下 Spring Bean 的生命周期？

![](image/Xnip2019-02-10_12-40-35.png)

## 5、 Spring Bean 有哪些作用域，它们之间有什么区别？

- singleton ：这种 bean 范围是默认的，这种范围确保不管接受到多少个请求，每个容器中只有一个 bean 的实例，单例的模式由 bean factory 自身来维护 。
- prototype ：原形范围与单例范围相反，为每一个 bean 请求提供一个实例 。
- request ：在请求 bean 范围内会每一个来自客户端的网络请求创建一个实例，在请求完成以后， bean 会失效并被垃圾回收器回收 。
- Session ：与请求范围类似，确保每个 session 中有一个 bean 的实例，在 session 过期后， bean 会随之失效 。
- global-session ： global-session 和 Portlet 应用相关 。 当你的应用部署在 Portlet 容器中工作时，它包含很多 portlet。 如果你想要声明让所有的 portlet 共用全局的存储变量的话，那么这全局变量需要存储在 global-session 中 。


## 6、 Spring 框架中的单例 Beans 是线程安全的么？

实际上，大部分的 Spring bean 并没有可变的状态 ( 比如 Service 类和 DAO 类 ) ，所以在某种程度上说 Spring 的单例 bean 是线程安全的 。 如果你的 bean 有多种状态的话（比如 View Model 对象），就需要自行保证线程安全 。

## 7、 请解释一下，Spring 框架有哪些自动装配模式，它们之间有何区别？

- no ：这是 Spring 框架的默认设置，在该设置下自动装配是关闭的，开发者需要自行在 bean 定义中用标签明确的设置依赖关系 。
- byName ：该选项可以根据 bean 名称设置依赖关系 。 当向一个 bean 中自动装配一个属性时，容器将根据 bean 的名称自动在在配置文件中查询一个匹配的 bean。 如果找到的话，就装配这个属性，如果没找到的话就报错 。
- byType ：该选项可以根据 bean 类型设置依赖关系 。 当向一个 bean 中自动装配一个属性时，容器将根据 bean 的类型自动在在配置文件中查询一个匹配的 bean。 如果找到的话，就装配这个属性，如果没找到的话就报错 。
- constructor ：构造器的自动装配和 byType 模式类似，但是仅仅适用于与有构造器相同参数的 bean ，如果在容器中没有找到与构造器参数类型一致的 bean ，那么将会抛出异常 。
- autodetect ：该模式自动探测使用构造器自动装配或者 byType 自动装配 。 首先，首先会尝试找合适的带参数的构造器，如果找到的话就是用构造器自动装配，如果在 bean 内部没有找到相应的构造器或者是无参构造器，容器就会自动选择 byTpe 的自动装配方式 。



## 8、 Spring 框架中用到了哪些设计模式？请举例说明
> Spring 框架中使用到了大量的设计模式，下面列举了比较有代表性的：

代理模式 — 在 AOP 和 remoting 中被用的比较多 。

单例模式 — 在 spring 配置文件中定义的 bean 默认为单例模式 。

模板方法 — 用来解决代码重复的问题 。 比如 RestTemplate,   JmsTemplate,   JpaTemplate。

前端控制器 —Spring 提供了 DispatcherServlet 来对请求进行分发 。

视图帮助 (View Helper   )—Spring 提供了一系列的 JSP 标签，高效宏来辅助将分散的代码整合在视图里 。

依赖注入 — 贯穿于 BeanFactory   /   ApplicationContext 接口的核心理念 。

工厂模式 —BeanFactory 用来创建对象的实例 。

## 9 、说说 Springmvc 有哪些优点？
- 它是基于组件技术的 . 全部的应用对象 , 无论控制器和视图 , 还是业务对象之类的都是 java 组件 . 并且和 Spring 提供的其他基础结构紧密集成 .
- 不依赖于 Servlet API( 目标虽是如此 , 但是在实现时确实是依赖于 Servlet 的 )
- 可以任意使用各种视图技术 , 而不仅仅局限于 JSP
- 支持各种请求资源的映射策略
- 它应是易于扩展的



## 10 、Spring 框架的事务管理有哪些优点？

- 它为不同的事务 API 如 JTA ， JDBC ， Hibernate ， JPA 和 JDO ，提供一个不变的编程模式 。
- 它为编程式事务管理提供了一套简单的 API 而不是一些复杂的事务 API。
- 它支持声明式事务管理 。
- 它和 Spring 各种数据访问抽象层很好的集成 。

## 11、 AOP 的应用场景、原理 、AOP 好处？

AOP–Aspect Oriented Programming 面向切面编程；用来封装横切关注点，具体可以在下面的场景中使用:

- Authentication 权限
- Caching 缓存
- Context passing 内容传递
- Error handling 错误处理
- Lazy loading 懒加载
- Debugging 调试
- logging, tracing, profiling and monitoring 记录跟踪优化校准
- Performance optimization 　性能优化
- Persistence 持久化
- Resource pooling 　资源池
- Synchronization 　同步
- Transactions 事务

原理： AOP 是面向切面编程，是通过动态代理的方式为程序添加统一功能，集中解决一些公共问题 。

优点：   

- 各个步骤之间的良好隔离性耦合性大大降低 。 
- 源代码无关性，再扩展功能的同时不对源码进行修改操作。



## 12、 Spring 中常见的创建对象的注解有哪些？
- @Component
- @Controller
- @Service
- @Repository

## 13、 Spring 管理事务的方式有几种？

1、编程式事务，在代码中硬编码。(不推荐使用)

2、声明式事务，在配置文件中配置（推荐使用）

声明式事务又分为两种：

a、基于XML的声明式事务

b、基于注解的声明式事务

## 14 、spring 中的核心类有那些，各有什么作用？

BeanFactory：产生一个新的实例，可以实现单例模式。

BeanWrapper：提供统一的 get 及 set 方法。

ApplicationContext：提供框架的实现，包括 BeanFactory 的所有功能。

## 15、 spring 中有哪些代理方式？实现原理是什么？这些方式的优缺点是什么？

1、若目标对象实现了若干接口， spring 使用 JDK 的 java.lang.reflect.Proxy 类代理 。

优点：因为有接口，所以使系统更加松耦合

缺点：为每一个目标类创建接口
2、若目标对象没有实现任何接口， spring 则使用 CGLIB 库生成目标对象的子类 。

优点：因为代理类与目标类是继承关系，所以不需要有接口的存在 。

缺点：因为没有使用接口，所以系统的耦合性没有使用 JDK 的动态代理好 。

## 16、 说说 IoC 容器的初始化过程？

- Resource 定位：我们一般使用外部资源来描述 Bean 对象，所以 IOC 容器第一步就是需要定位 Resource 外部资源 。Resource 的定位其实就是 BeanDefinition 的资源定位，它是由 ResourceLoader 通过统一的 Resource 接口来完成的，这个 Resource 对各种形式的 BeanDefinition 的使用都提供了统一接口 。
- 载入：第二个过程就是 BeanDefinition 的载入 ,BeanDefinitionReader 读取 , 解析 Resource 定位的资源，也就是将用户定义好的 Bean 表示成 IOC 容器的内部数据结构也就是 BeanDefinition, 在 IOC 容器内部维护着一个 BeanDefinition Map 的数据结构，通过这样的数据结构， IOC 容器能够对 Bean 进行更好的管理 。 在配置文件中每一个都对应着一个 BeanDefinition 对象 。
- 注册：第三个过程则是注册，即向 IOC 容器注册这些 BeanDefinition ，这个过程是通过 BeanDefinitionRegistery 接口来实现的 。


## 17、Spring中ApplicationContext的重要实现是什么？

- FileSystemXmlApplicationContext ：此容器从一个XML文件中加载beans的定义，XML Bean 配置文件的全路径名必须提供给它的构造函数。
- ClassPathXmlApplicationContext：此容器也从一个XML文件中加载beans的定义，这里，你需要正确设置classpath因为这个容器将在classpath里找bean配置。
- WebXmlApplicationContext：此容器加载一个XML文件，此文件定义了一个WEB应用的所有bean。

## 18、解释一下Bean的生命周期？
- Spring容器 从XML 文件中读取bean的定义，并实例化bean。
- Spring根据bean的定义填充所有的属性。
- 如果bean实现了BeanNameAware 接口，Spring 传递bean 的ID 到 setBeanName方法。
- 如果Bean 实现了 BeanFactoryAware 接口， Spring传递beanfactory 给setBeanFactory 方法。
- 如果有任何与bean相关联的BeanPostProcessors，Spring会在postProcesserBeforeInitialization()方法内调用它们。
- 如果bean实现IntializingBean了，调用它的afterPropertySet方法，如果bean声明了初始化方法，调用此初始化方法。
- 如果有BeanPostProcessors 和bean 关联，这些bean的postProcessAfterInitialization() 方法将被调用。
- 如果bean实现了 DisposableBean，它将调用destroy()方法。


## 19、什么是Beand wiring？
?
## 20、什么是单例Bean 如何创建一个原型Bean？

## 21、说说 AOP 的实现原理？动态代理的方式有哪些？

Spring AOP 使用的是动态代理，所谓的动态代理就是说 AOP 框架不会去修改字节码，而是在内存中临时为方法生成一个 AOP 对象，这个 AOP 对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法 。

Spring AOP 中的动态代理主要有两种方式， JDK 动态代理和 CGLIB 动态代理 。 
* JDK 动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口 。JDK 动态代理的核心是 InvocationHandler 接口和 Proxy 类 。 
* 如果目标类没有实现接口，那么 Spring AOP 会选择使用 CGLIB 来动态代理目标类 。CGLIB （ Code Generation Library ），是一个代码生成的类库，可以在运行时动态的生成某个类的子类，注意， CGLIB 是通过继承的方式做的动态代理，因此如果某个类被标记为 final ，那么它是无法使用 CGLIB 做动态代理的 。


## 22、Bean 工厂和 Application contexts 有什么区别？
BeanFactory 是 Spring 里面最低层的接口，提供了最简单的容器的功能，只提供了实例化对象和获取对象的功能 。

两者装载 bean 的区别 
* BeanFactory ：在启动时不会去实例化 Bean ，只有从容器中获取 Bean 时才会去实例化。 
* ApplicationContext ：在启动的时候就把所有的 Bean 全部实例化了 。 它还可以为 Bean 配置 lazy-init=true 来让 Bean 延迟实例化。

延迟实例化 
优点： 应用启动时占用资源很少，对资源要求较高的应用，比较有优势。 
缺点：速度会相对来说慢一些 。 而且有可能会出现空指针异常的错误，而且通过 bean 工厂创建的 bean 生命周期会简单一些。 所有的 Bean 在启动的时候都加载，系统运行的速度快，而且可以尽早的发现系统中的配置问题 。

建议 web 应用，在启动的时候就把所有的 Bean 都加载了 。

## 23、、有哪些不同类型的IOC（依赖注入）方式？

- 构造器依赖注入：构造器依赖注入通过容器触发一个类的构造器来实现的，该类有一系列参数，每个参数代表一个对其他类的依赖。
- Setter方法注入：Setter方法注入是容器通过调用无参构造器或无参static工厂 方法实例化bean之后，调用该bean的setter方法，即实现了基于setter的依赖注入。

## 24、Spring beans是什么?

Spring beans 是那些形成Spring应用的主干的java对象。它们被Spring IOC容器初始化，装配，和管理。这些beans通过容器中配置的元数据创建。比如，以XML文件中<bean/> 的形式定义。

Spring 框架定义的beans都是单件beans。在bean tag中有个属性”singleton”，如果它被赋为TRUE，bean 就是单件，否则就是一个 prototype bean。默认是TRUE，所以所有在Spring框架中的beans 缺省都是单件。

## 25、 一个 Spring Bean 定义 包含什么？
一个Spring Bean 的定义包含容器必知的所有配置元数据，包括如何创建一个bean，它的生命周期详情及它的依赖。

## 26、Spring框架中的单例bean是线程安全的吗?
不，Spring框架中的单例bean不是线程安全的。

## 27、不同版本的 Spring Framework 有哪些主要功能？

## 28、 Spring Boot、Spring MVC 和 Spring 有什么区别？
![](image/Xnip2019-02-10_13-00-14.png)

1.spring 
Spring最重要的特征是依赖注入。所有 SpringModules 不是依赖注入就是 IOC 控制反转。

当我们恰当的使用 DI 或者是 IOC 的时候，我们可以开发松耦合应用。松耦合应用的单元测试可以很容易的进行。

2、Spring MVC

Spring MVC 提供了一种分离式的方法来开发 Web 应用。通过运用像 DispatcherServelet，MoudlAndView 和 ViewResolver 等一些简单的概念，开发 Web 应用将会变的非常简单。

3、SpringBoot

Spring 和 SpringMVC 的问题在于需要配置大量的参数。



## 29、spring boot自动配置原理？

在spring程序main方法中 添加@SpringBootApplication或者@EnableAutoConfiguration

        会自动去maven中读取每个starter中的spring.factories文件  该文件里配置了所有需要被创建spring容器中的bean

## 30、什么是 Spring Boot Stater ？
启动器是一套方便的依赖没描述符，它可以放在自己的程序中。你可以一站式的获取你所需要的 Spring 和相关技术，而不需要依赖描述符的通过示例代码搜索和复制黏贴的负载。

例如，如果你想使用 Sping 和 JPA 访问数据库，只需要你的项目包含 spring-boot-starter-data-jpa 依赖项，你就可以完美进行。 

**常用的starter**

- spring-boot-starter-web 嵌入tomcat和web开发需要servlet与jsp支持

- spring-boot-starter-data-jpa 数据库支持

- spring-boot-starter-data-redis redis数据库支持

- spring-boot-starter-data-solr solr支持

- mybatis-spring-boot-starter 第三方的mybatis集成starter


## 31、你能否举一个例子来解释更多 Staters 的内容？

让我们来思考一个 Stater 的例子 -Spring Boot Stater Web。

如果你想开发一个 web 应用程序或者是公开 REST 服务的应用程序。Spring Boot Start Web 是首选。让我们使用 Spring Initializr 创建一个 Spring Boot Start Web 的快速项目。

Spring Boot Start Web 的依赖项

依赖项可以被分为：

Spring - core，beans，context，aop

Web MVC - （Spring MVC）

Jackson - for JSON Binding

Validation - Hibernate,Validation API

Enbedded Servlet Container - Tomcat

Logging - logback,slf4j

任何经典的 Web 应用程序都会使用所有这些依赖项。Spring Boot Starter Web 预先打包了这些依赖项。

作为一个开发者，我不需要再担心这些依赖项和它们的兼容版本。



## 32、Spring Boot 还提供了其它的哪些 Starter Project Options？
Spring Boot 也提供了其它的启动器项目包括，包括用于开发特定类型应用程序的典型依赖项。

spring-boot-starter-web-services - SOAP Web Services；

spring-boot-starter-web - Web 和 RESTful 应用程序；

spring-boot-starter-test - 单元测试和集成测试；

spring-boot-starter-jdbc - 传统的 JDBC；

spring-boot-starter-hateoas - 为服务添加 HATEOAS 功能；

spring-boot-starter-security - 使用 SpringSecurity 进行身份验证和授权；

spring-boot-starter-data-jpa - 带有 Hibeernate 的 Spring Data JPA；

spring-boot-starter-data-rest - 使用 Spring Data REST 公布简单的 REST 服务；

## 33、 Spring 是如何快速创建产品就绪应用程序的？
Spring Boot 致力于快速产品就绪应用程序。为此，它提供了一些譬如高速缓存，日志记录，监控和嵌入式服务器等开箱即用的非功能性特征。

spring-boot-starter-actuator - 使用一些如监控和跟踪应用的高级功能

spring-boot-starter-undertow, spring-boot-starter-jetty, spring-boot-starter-tomcat - 选择您的特定嵌入式 Servlet 容器

spring-boot-starter-logging - 使用 logback 进行日志记录

spring-boot-starter-cache - 启用 Spring Framework 的缓存支持

## 34、Spring2 和 Spring5 所需要的最低 Java 版本是什么？

Spring Boot 2.0 需要 Java8 或者更新的版本。Java6 和 Java7 已经不再支持。
## 35、创建一个 Spring Boot Project 的最简单的方法是什么？

Spring Initializr是启动 Spring Boot Projects 的一个很好的工具。
![](image/Xnip2019-02-10_13-13-49.png)
## 36、Spring Initializr 是创建 Spring Boot Projects 的唯一方法吗？
不是的。

Spring Initiatlizr 让创建 Spring Boot 项目变的很容易，但是，你也可以通过设置一个 maven 项目并添加正确的依赖项来开始一个项目。

在我们的 Spring 课程中，我们使用两种方法来创建项目。

第一种方法是 start.spring.io 。

另外一种方法是在项目的标题为“Basic Web Application”处进行手动设置。

手动设置一个 maven 项目

这里有几个重要的步骤：

在 Eclipse 中，使用文件 - 新建 Maven 项目来创建一个新项目

添加依赖项。

添加 maven 插件。

添加 Spring Boot 应用程序类。

到这里，准备工作已经做好！


## 37、为什么我们需要 spring-boot-maven-plugin?

spring-boot-maven-plugin 提供了一些像 jar 一样打包或者运行应用程序的命令。

spring-boot:run 运行你的 SpringBooty 应用程序。

spring-boot：repackage 重新打包你的 jar 包或者是 war 包使其可执行

spring-boot：start 和 spring-boot：stop 管理 Spring Boot 应用程序的生命周期（也可以说是为了集成测试）。

spring-boot:build-info 生成执行器可以使用的构造信息。

## 38、如何使用 SpringBoot 自动重装我的应用程序？
![](image/Xnip2019-02-10_13-16-38.png)

## 39、什么是嵌入式服务器？我们为什么要使用嵌入式服务器呢?

思考一下在你的虚拟机上部署应用程序需要些什么。

第一步： 安装 Java

第二部： 安装 Web 或者是应用程序的服务器（Tomat/Wbesphere/Weblogic 等等）

第三部： 部署应用程序 war 包

如果我们想简化这些步骤，应该如何做呢？

让我们来思考如何使服务器成为应用程序的一部分？

你只需要一个安装了 Java 的虚拟机，就可以直接在上面部署应用程序了，

是不是很爽？

这个想法是嵌入式服务器的起源。

当我们创建一个可以部署的应用程序的时候，我们将会把服务器（例如，tomcat）嵌入到可部署的服务器中。

例如，对于一个 Spring Boot 应用程序来说，你可以生成一个包含 Embedded Tomcat 的应用程序 jar。你就可以想运行正常 Java 应用程序一样来运行 web 应用程序了。

嵌入式服务器就是我们的可执行单元包含服务器的二进制文件（例如，tomcat.jar）。

## 40、如何在 Spring Boot 中添加通用的 JS 代码？

![](image/Xnip2019-02-10_13-18-11.png)

## 后续问题
![](image/Xnip2019-02-10_13-20-01.png)


## spring 中的设计模式

![](image/Xnip2019-02-13_13-39-27.png)