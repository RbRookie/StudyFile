# Spring5

## 1. 简介

spring理念：是现有的技术更加容易实用，本身是一个大杂烩。

- SSH：Struct2 + Spring + Hibernate
- SSM: SpringMVC + Spring + Mybatis

引用别人写的源码：https://github.com/Donkequan/Spring-Study

官网： https://spring.io/projects/spring-framework#overview

官方下载： https://repo.spring.io/release/org/springframework/spring/

Spring框架源码GitHub： https://github.com/spring-projects/spring-framework

[Spring Web MVC](https://mvnrepository.com/artifact/org.springframework/spring-webmvc) **»** [5.2.5.RELEASE](https://mvnrepository.com/artifact/org.springframework/spring-webmvc/5.2.5.RELEASE)

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.3.RELEASE</version>
</dependency>
```

- spring是开源的免费的容器。
- spring是一个轻量级的，非入侵式的。
- 控制反转（IOC），面向切面编程 (AOP)。
- 支持事务处理，对框架整合的支持。

总结：spring是一个轻量级的控制反转(IOC)和面向切面编程(AOP)的框架。

## 2.IOC层介绍

**`控制反转IOC`(Inversion of Control)，是一种设计思想，DI(依赖注入)是实现IOC的一种方法**，也有人认为DI只是IOC的另一种说法。没有IOC的程序中 , 我们使用面向对象编程 , 对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是：获得依赖对象的方式反转了。
![在这里插入图片描述](https://www.freesion.com/images/48/5a946946a86ee0833b0947953425a050.png)
  IOC是Spring框架的核心内容，使用多种方式完美的实现了IOC，可以使用XML配置，也可以使用注解，新版本的Spring也可以零配置实现IoC。

  Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从IOC容器中取出需要的对象。
![在这里插入图片描述](https://www.freesion.com/images/785/b395b628162c9650842280af3790dc01.png)
  采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。

  **控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection,DI）。**

------

## 3.IOC理论推导

> 搭建项目

  新建Maven项目，pom.xml到导入spring-webmvc依赖(也可称为包，导该依赖的好处就是：idea会默认帮我们导入spring的几个主要包)，删除src目录，项目根目录下新建Module，并且选择maven项目（这样就可以在一个项目中，新建多个项目了(Module)）

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.6.RELEASE</version>
</dependency>
123456
```

![在这里插入图片描述](https://www.freesion.com/images/82/ea04f4c07aeb2eff0d2099327b2206c2.png)
`原来：`

1. UserDao 接口

```java
package com.ling.dao;

/**
 * @author 编程之外
 * dao接口
 */
public interface UserDao {
    void getUser();
}

```

1. UserDaoImpl 实现类

```java
package com.ling.dao;

/**
 * @author 编程之外
 * dao实现类
 */
public class UserDaoImpl implements UserDao {
    public void getUser(){
        System.out.println("默认获取用户的数据");
    }
}


```

1. UserService 业务接口

```java
package com.ling.service;

/**
 * @author 编程之外
 * service接口
 */
public interface UserService {
    void getUser();
}


```

1. UserServiceImpl 业务实现类

```java
package com.ling.service;

import com.ling.dao.UserDao;
import com.ling.dao.UserDaoImpl;

/**
 * @author 编程之外
 * service 实现类
 * service层调到层实现类
 */
public class UserServiceImpl implements UserService {

    private UserDao userDao = new UserDaoImpl();

    public void getUser() {
        userDao.getUser();
    }
}

```

![在这里插入图片描述](https://www.freesion.com/images/324/7d6b1f2881aee4b742e70716c928f12c.png)
在之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求，去修改原代码！如果程序代码量非常大，修改一次的成本代价就十分昂贵！

**现在我们使用一个Set接口实现：**

```java
package com.ling.service;

import com.ling.dao.UserDao;
import com.ling.dao.UserDaoImpl;

/**
 * @author 编程之外
 * service 实现类
 * service层调到层实现类
 */
public class UserServiceImpl implements UserService {

    private UserDao userDao;
    
    //利用set进行动态实现值的注入
    public void setUserDao(UserDao userDao){
        this.userDao=userDao;
    }
    

    public void getUser() {
        userDao.getUser();
    }
}

```

- 之前，程序是主动创建对象！控制权在程序员手上
- 使用了set注入后，程序不再具有主动性，而是变成了被动的接收对象！ 控制反转

**这种思想从本质上解决了问题，我们不用再去管理对象的创建 。系统的耦合性大大降低~，可以更专注的在业务的实现上！** 这是IOC的原型！

## 4.Hello Spring

pojo层中

```java
package com.hui.pojo;

/**
 * @ClassName: Hello
 * @Author: Rokkie丶zimiao
 * @Date: 2021/10/26 15:07
 * @Description:
 */
public class Hello {
    private String str;

    public void setStr(String str) {
        this.str = str;
    }

    public String getStr() {
        return str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}

```

### resource文件中的Spring配置文件

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="hello" class="com.hui.pojo.Hello">
        <property name="str" value="Spring"/>
    </bean>

</beans>
```

Mytest文件

```java
import com.hui.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @ClassName: Mytest
 * @Author: Rokkie丶zimiao
 * @Date: 2021/10/26 15:19
 * @Description:
 */
public class Mytest {
    public static void main(String[] args) {
     //获取spring的对象   
     ApplicationContext content = new ClassPathXmlApplicationContext("bean.xml");
     //得到bean 容器，从容器中取出对象   
     Hello hello = (Hello) content.getBean("hello");
        System.out.println(hello.toString());
    }
}


```

## 5. IOC创建对象的方式

1. 使用无参构造创建对象，默认。
2. 使用有参构造

下标赋值

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="com.hou.pojo.User">
        <constructor-arg index="0" value="hou"/>
    </bean>
</beans>
```

类型赋值（不建议使用）

```xml
<bean id="user" class="com.hou.pojo.User">
    <constructor-arg type="java.lang.String" value="dong"/>
</bean>
```

直接通过参数名

```xml
<bean id="user" class="com.hou.pojo.User">
    <constructor-arg name="name" value="hou"></constructor-arg>
</bean>
```

Spring类似于婚介网站！

你想不想要，对象都在里面。注册bean之后用不用被实例化。

## 6. Spring配置

**别名**

```xml
<bean id="user" class="com.hou.pojo.User">
    <constructor-arg name="name" value="hou"></constructor-arg>
</bean>

<alias name="user" alias="user2aaa"/>
```

**Bean的配置**

- id：bean的id标识符
- class：bean对象所对应的类型
- name：别名，更高级，可以同时取多个别名。

**import**

一般用于团队开发，它可以将多个配置文件，导入合并为一个

```xml
<import resource="beans.xml"/>
```

## 6. DI依赖注入

**构造器注入**

**set方式注入**（重点）

- 依赖：bean对象的创建依赖于容器
- 注入：bean对象中的所有属性，由容器来注入

【环境搭建】

1. 复杂类型
2. 真实测试对象

stdudent类

```java
package com.hui.pojo;

import java.util.*;

/**
 * @ClassName: Student
 * @Author: Rokkie丶zimiao
 * @Date: 2021/10/26 20:39
 * @Description:
 */
public class Student {

    private String name;
    private Address address;
    private String[] books;
    private List<String> hobbys;
    private Map<String,String> crad;
    private Set<String> game;
    private String wife; //设置空指针项目
    private Properties info;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public String[] getBooks() {
        return books;
    }

    public void setBooks(String[] books) {
        this.books = books;
    }

    public List<String> getHobbys() {
        return hobbys;
    }

    public void setHobbys(List<String> hobbys) {
        this.hobbys = hobbys;
    }

    public Map<String, String> getCrad() {
        return crad;
    }

    public void setCrad(Map<String, String> crad) {
        this.crad = crad;
    }

    public Set<String> getGame() {
        return game;
    }

    public void setGame(Set<String> game) {
        this.game = game;
    }

    public String getWife() {
        return wife;
    }

    public void setWife(String wife) {
        this.wife = wife;
    }

    public Properties getInfo() {
        return info;
    }

    public void setInfo(Properties info) {
        this.info = info;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", address=" + address.toString() +
                ", books=" + Arrays.toString(books) +
                ", hobbys=" + hobbys +
                ", crad=" + crad +
                ", game=" + game +
                ", wife='" + wife + '\'' +
                ", info=" + info +
                '}';
    }
}

```

Address

```java
package com.hui.pojo;

/**
 * @ClassName: Address
 * @Author: Rokkie丶zimiao
 * @Date: 2021/10/26 20:38
 * @Description:
 */
public class Address {
    private String address;

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return  address;
    }
}

```

bean.xml  //各种类型依赖注入方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
<!--bean = 对象-->
<!--id = 变量名-->
<!--class = new 的对象，类文件的路径-->
    <bean id="address" class="com.hui.pojo.Address">
        <property name="address" value="南京"></property>
    </bean>
    <bean id="student" class="com.hui.pojo.Student">
<!-- 普通值注入 value注入-->
        <property name="name" value="王晖"/>
<!--bean注入-->
        <property name="address" ref="address"/>
        <property name="books">
            <array>
                <value>红楼梦</value>
                <value>三国演义</value>
                <value>水浒传</value>
                <value>西游记</value>
            </array>
        </property>
        <property name="hobbys">
            <list>
                <value>听歌</value>
                <value>健身</value>
                <value>java</value>
            </list>
        </property>
        <property name="crad">
            <map>
                <entry key="身份证" value="3426221997100512312"/>
                <entry key="银行卡" value="123456781234"/>
            </map>
        </property>
        <property name="wife">
            <null/>
        </property>
        <property name="game">
            <set>
                <value>lol</value>
                <value>P2P</value>
                <value>RAR</value>
            </set>
        </property>
        <property name="info">
            <props>
                <prop key="drive">@123456</prop>
                <prop key="url">jdbc</prop>
            </props>
        </property>
    </bean>
</beans>
```

**第三方**

p标签和c标签

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"     使用P命名空间和C命名空间需要在<beans>中引入规范 
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--p命名空间注入/set注入-->
    <bean id="use" class="com.pojo.User" p:name="dong" p:age="10">
    </bean>

    <!--c命名空间/构造器-->
    <bean id="use2" class="com.pojo.User" c:name="kun" c:age="19"></bean>
</beans>
```

**bean的作用域**

1. 单例模式（默认）

```xml
<bean id="use2" class="com.pojo.User" c:name="kun" c:age="19" scope="singleton"></bean>
```

1. 原型模式: 每次从容器中get的时候，都产生一个新对象！

```xml
<bean id="use2" class="com.pojo.User" c:name="kun" c:age="19" scope="prototype"></bean>
```

1. 其余的request、session、application这些只能在web开放中使用！

## 7. Bean的自动装配

- 自动装配是Spring是满足bean依赖的一种方式
- Spring会在上下文自动寻找，并自动给bean装配属性

在Spring中有三种装配的方式

1. 在xml中显示配置

2. 在java中显示配置

3. 隐式的自动装配bean 【重要】

   ```xml
       <bean id="people" class="com.pojo.People" autowire="byType">
           <property name="name" value="hou"></property>
       </bean>
   
   
   
   autowire="byType" 
   ```

   

![image](https://cdn.jsdelivr.net/gh/RbRookie/image-management@master/20211101/image.3gie5aduvtc0.png)

**使用注解自动装配**

导入context约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

@Autowire

在属性上个使用，也可以在set上使用

我们可以不用编写set方法了

```java
public class People {
    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;
}
```

如果@Autowired自动装配环境比较复杂。自动装配无法通过一个注解完成的时候

我们可以使用@Qualifier(value = "dog")去配合使用，指定一个唯一的id对象

```JAVA
public class People {
    @Autowired
    private Cat cat;
    @Autowired
    @Qualifier(value = "dog")
    private Dog dog;
    private String name;
}
```

@Resource(name="dog")也可以

区别：

- @autowire通过byType实现，而且必须要求这个对象存在
- @resource默认通过byName实现，如果找不到，通过byType实现

## 8. 使用注解开发

在spring4之后，必须要保证aop的包导入

使用注解需要导入contex的约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

1. 属性如何注入

```java
@Component
public class User {
    
    @Value("dong")
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

1. 衍生的注解

@Component有几个衍生注解，会按照web开发中，mvc架构中分层。

- dao （@Repository）
- service（@Service）
- controller（@Controller）

小结：

xml与注解

- xml更加万能，维护简单
- 注解，不是自己的类，使用不了，维护复杂

最佳实践：

- xml用来管理bean
- 注解只用来完成属性的注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
    <!--指定要扫描的包-->
    <context:component-scan base-package="com.pojo"/>

</beans>
```

## 9. 使用java方式配置spring

JavaConfig

Spring的一个子项目，在spring4之后，，他成为了核心功能

```java
@Configuration //这个也会被spring容器托管，注册到容器中，因为他本来就是一个@Component
@ComponentScan("com.pojo")
@Import(Config2.class)
public class MyConfig {

    @Bean
    public User getUser(){
        return new User();
    }

}
@Component
public class User {

    @Value("dong")
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

这种纯java配置方式

在springboot中，随处可见

## 10. 动态代理

动态代理和静态代理

角色一样

动态代理类是动态生成的，不是我们直接写好的！

动态代理：基于接口，基于类

- 基于接口：JDK的动态代理【使用】
- 基于类：cglib
- java字节码

**InvocationHandler**   invocation 调用

Proxy

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

//会这个类，自动生成代理类
public class ProxyInvocation implements InvocationHandler {

    //被代理的接口
    private Rent rent;

    public void setRent(Rent rent) {
        this.rent = rent;
    }

    //生成代理类
    public Object getProxy(){
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),rent.getClass().getInterfaces(),this);
    }

    //处理代理实例，并返回结果
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        seeHouse();
        Object result = method.invoke(rent, args);
        fare();
        return result;
    }

    public void seeHouse(){
        System.out.println("see house");
    }

    public void fare(){
        System.out.println("fare");
    }
}
public interface Rent {
    void rent();
}
public class Host implements Rent {
    public void rent() {
        System.out.println("host rent");
    }
}
public class Client {

    public static void main(String[] args) {
        //真实角色
        Host host = new Host();

        //代理角色
        ProxyInvocation proxyInvocation = new ProxyInvocation();

        //通过调用程序处理角色来处理我们要调用的接口对象
        proxyInvocation.setRent(host);

        Rent proxy = (Rent) proxyInvocation.getProxy();  //这里的proxy是动态生成的

        proxy.rent();
    }
}
```

## 11.AOP

```xml
<dependencies>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.4</version>
    </dependency>
</dependencies>
```

方法一：使用spring接口【springAPI接口实现】

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beanss
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userservice" class="com.service.UserServiceImp"></bean>
    <bean id="log" class="com.log.Log"/>
    <bean id="afterlog" class="com.log.AfterLog"/>

    <!--配置aop-->
    <aop:config>
        <!--切入点：expression:表达式，execution（要执行的位置）-->
        <aop:pointcut id="point" expression="execution(* com.service.UserServiceImp.*(..))"/>
        <!--执行环绕-->
        <aop:advisor advice-ref="log" pointcut-ref="point"/>
        <aop:advisor advice-ref="afterlog" pointcut-ref="point"/>
    </aop:config>

</beans>
```



```java
import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class Log implements MethodBeforeAdvice {
    //method：要执行的目标对象的方法
    //args：参数
    //target：目标对象
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+method.getName());
    }
```

方法二：自定义来实现AOP【主要是切面定义】

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userservice" class="com.service.UserServiceImp"></bean>
    <bean id="log" class="com.log.Log"/>
    <bean id="afterlog" class="com.log.AfterLog"/>

    <bean id="diy" class="com.diy.DiyPointcut">
    </bean>
    <aop:config>
        <!--自定义切面-->
        <aop:aspect ref="diy">
            <!--切入点-->
            <aop:pointcut id="point" expression="execution(* com.service.UserServiceImp.*(..))"/>
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>

</beans>
```

```java
public class DiyPointcut {

    public void before(){
        System.out.println("before");
    }

    public void after(){
        System.out.println("after");
    }
}
```

方法三：注解方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
    
    <bean id="ann" class="com.diy.Annotation"></bean>
    <aop:aspectj-autoproxy/>
    <!--注册bean-->
    <bean id="userservice" class="com.service.UserServiceImp"></bean>
    
</beans>
```

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect  //标注这个类是一个切面
public class Annotation {

    @Before("execution(* com.service.UserServiceImp.*(..))")
    public void before(){
        System.out.println("before");
    }

    @After("execution(* com.service.UserServiceImp.*(..))")
    public void after(){
        System.out.println("after");
    }

    //在环绕增强中，我们可以给地暖管一个参数，代表我们要获取切入的点
    @Around("execution(* com.service.UserServiceImp.*(..))")
    public void around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("around");

        Object proceed = joinPoint.proceed();

        System.out.println("after around");
    }
}
```

## 12. 整合mybatis

文档： https://mybatis.org/spring/zh/

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>spring-study</artifactId>
        <groupId>com.hou</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>spring-10-mybatis</artifactId>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.4</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.3.RELEASE</version>
        </dependency>


        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>

        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
        </dependency>
    </dependencies>

    <build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>

</project>
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <typeAliases>
        <package name="com.pojo"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://111.230.212.103:3306/mybatis?userSSL=true&amp;
                userUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="hdk123"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper class="com.mapper.UserMapper"/>
    </mappers>
</configuration>
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.mapper.UserMapper">

    <select id="selectUser" resultType="user">
        select * from mybatis.user;
    </select>

</mapper>
public interface UserMapper {
    List<User> selectUser();
}
```

整合

方法一：

<img src="https://cdn.jsdelivr.net/gh/RbRookie/image-management@master/20211108/image.3xoufmbx8sw0.png" alt="image" style="zoom:80%;" />

UserMapperImpl

```java
package com.mapper;

import com.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;

import java.util.List;

public class UserMapperImpl implements UserMapper {

    private SqlSessionTemplate sqlSessionTemplate;

    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
        this.sqlSessionTemplate = sqlSessionTemplate;
    }

    public List<User> selectUser() {
        UserMapper mapper = sqlSessionTemplate.getMapper(UserMapper.class);
        return mapper.selectUser();
    }
}
```

mybatis.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <typeAliases>
        <package name="com.pojo"/>
    </typeAliases>

</configuration>
```

spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--data source-->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://111.230.212.103:3306/mybatis?userSSL=true&amp;
                userUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="hdk123"/>
    </bean>

    <!--sqlsession-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource" />
        <!--bound mybatis-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/mapper/UserMapper.xml"/>
    </bean>

    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>

    <bean id="userMapper" class="com.mapper.UserMapperImpl">
        <property name="sqlSessionTemplate" ref="sqlSession"></property>
    </bean>

</beans>
```

test

```java
import com.mapper.UserMapper;
import com.pojo.User;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.io.IOException;


public class Mytest {


    public static void main(String[] args) throws IOException {

        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("spring-dao.xml");
        UserMapper userMapper = context.getBean("userMapper", UserMapper.class);

        for (User user : userMapper.selectUser()) {
            System.out.println(user);
        }
    }
}
```

方法二：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--data source-->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://111.230.212.103:3306/mybatis?userSSL=true&amp;
                userUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="hdk123"/>
    </bean>

    <!--sqlsession-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource" />
        <!--bound mybatis-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/mapper/UserMapper.xml"/>
    </bean>

    <!--<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">-->
        <!--<constructor-arg index="0" ref="sqlSessionFactory"/>-->
    <!--</bean>-->

    <!--<bean id="userMapper" class="com.mapper.UserMapperImpl">-->
        <!--<property name="sqlSessionTemplate" ref="sqlSession"></property>-->
    <!--</bean>-->

    <bean id="userMapper2" class="com.mapper.UserMapperIml2">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
    </bean>

</beans>
package com.mapper;

import com.pojo.User;
import org.apache.ibatis.session.SqlSession;
import org.mybatis.spring.support.SqlSessionDaoSupport;

import java.util.List;

public class UserMapperIml2 extends SqlSessionDaoSupport implements UserMapper {
    public List<User> selectUser() {
        SqlSession sqlSession = getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.selectUser();
    }
}
```

## 13. 声明式事务

- 要么都成功，要么都失败
- 十分重要，涉及到数据一致性
- 确保完整性和一致性

事务的acid原则：

- 原子性
- 一致性
- 隔离性
  - 多个业务可能操作一个资源，防止数据损坏
- 持久性
  - 事务一旦提交，无论系统发生什么问题，结果都不会被影响。

Spring中的事务管理

- 声明式事务
- 编程式事务

声明式事务

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-tx.aop">

    <!--data source-->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://111.230.212.103:3306/mybatis?userSSL=true&amp;
                userUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="hdk123"/>
    </bean>

    <!--sqlsession-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource" />
        <!--bound mybatis-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/mapper/*.xml"/>
    </bean>

    <!--声明式事务-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg ref="datasource" />
    </bean>

    <!--结合aop实现事务置入-->
    <!--配置事务的类-->
    <tx:advice id="tx1" transaction-manager="transactionManager">
        <!--给哪些方法配置事务-->
        <!--配置事务的传播特性-->
        <tx:attributes>
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete" propagation="REQUIRED"/>
            <tx:method name="update" propagation="REQUIRED"/>
            <tx:method name="*" propagation="REQUIRED"/>
            <tx:method name="query" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!--配置事务切入-->
    <aop:config>
        <aop:pointcut id="txpointxut" expression="execution(* com.mapper.*.*(..))"/>
        <aop:advisor advice-ref="tx1" pointcut-ref="txpointxut"/>
    </aop:config>

</beans>
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="spring-dao.xml"/>

    <bean id="userMapper2" class="com.mapper.UserMapperIml2">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
    </bean>

</beans>
```

Mapper

```java
package com.mapper;

import com.pojo.User;

import java.util.List;

public interface UserMapper {
    List<User> selectUser();
    int addUser(User user);
    int delete(int id);
}
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.mapper.UserMapper">
    <select id="selectUser" resultType="user">
        select * from mybatis.user;
    </select>

    <insert id="addUser" parameterType="user">
        insert into mybatis.user (id, name, pwd) values
        (#{id}, #{name}, #{pwd})
    </insert>

    <delete id="delete" parameterType="int">
        delete from mybatis.user where id=#{id}
    </delete>
</mapper>
package com.mapper;

import com.pojo.User;
import org.apache.ibatis.session.SqlSession;
import org.mybatis.spring.support.SqlSessionDaoSupport;

import java.util.List;

public class UserMapperIml2 extends SqlSessionDaoSupport implements UserMapper {

    public List<User> selectUser() {
        User user = new User(6, "long", "zhi");
        SqlSession sqlSession = getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        mapper.addUser(user);
        mapper.delete(6);
        return mapper.selectUser();
    }

    public int addUser(User user) {
        return getSqlSession().getMapper(UserMapper.class).addUser(user);
    }

    public int delete(int id) {
        return getSqlSession().getMapper(UserMapper.class).delete(id);
    }
}
```
