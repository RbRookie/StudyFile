# Mybatis

### 第一个Mybatis程序

#### 步骤

Mybatis一般不用注解

环境搭建--->导入mybatis-->编写代码-->测试

导入依赖

**maven依赖**

```xml	
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.19</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```

创建mybatis核心配置文件

根据mybatis文档要求填写对应的xml文件。

XML 配置文件中包含了对 MyBatis 系统的核心设置，包括获取数据库连接实例的**数据源（DataSource）**以及**决定事务作用域和控制方式的事务管理器**（TransactionManager）。后面会再探讨 XML 配置文件的详细内容，这里先给出一个简单的示例：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

- 编写mybatis工具类

```java 
//sqlSessionFactory --> sqlSession
public class MybatisUtils {

    static SqlSessionFactory sqlSessionFactory = null;

    static {
        try {
            //使用Mybatis第一步 ：获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例.
    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}

```

#### 编写代码

- 实体类
- Dao接口

```java
public interface UserDao {
    public List<User> getUserList();
}
123
```

- 接口实现类 （由原来的UserDaoImpl转变为一个Mapper配置文件）

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=绑定一个指定的Dao/Mapper接口-->
<mapper namespace="com.kuang.dao.UserDao">
    <select id="getUserList" resultType="com.kuang.pojo.User">
    select * from USER
  </select>
</mapper>
1234567891011
```

- 测试

**注意点：**

org.apache.ibatis.binding.BindingException: Type interface com.kuang.dao.UserDao is not known to the MapperRegistry.

**MapperRegistry是什么?**

核心配置文件中注册mappers

- junit测试

```java
    @Test
    public void test(){

        //1.获取SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //2.执行SQL
        // 方式一：getMapper
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> userList = userDao.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }

        //关闭sqlSession
        sqlSession.close();
    }
12345678910111213141516
```

**可能会遇到的问题：**
1.配置文件没有注册
2.绑定接口错误
3.方法名不对
4.返回类型不对
5.Maven导出资源问题

#### CRUD

写好MybatisUtils工具类，只需要动UserMapper接口和其中的xml文件就可以实现增删改查操作

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace=绑定一个对应的Dao、Mapper接口-->
<mapper namespace="com.hui.dao.UserMapper">
    <select id= "getUserList" resultType="com.hui.pojo.User">
        select *from mybatis.user
    </select>

    <select id="getUserById" resultType="com.hui.pojo.User" parameterType="int">
        select *from mybatis.user where id = #{id}
    </select>

    <insert id="addUser" parameterType="com.hui.pojo.User">
        insert into mybatis.user(id, name, pwd) VALUES (#{id},#{name},#{pwd});
    </insert>

    <update id="updataUser" parameterType="com.hui.pojo.User">
        update mybatis.user set name = #{name},pwd=#{pwd}   where id = #{id};
    </update>

    <delete id="deleteUser" parameterType="com.hui.pojo.User">
        delete from mybatis.user where id =#{id};
    </delete>
</mapper>
```

#### 万能Map

假设，我们的实体类，或者数据库中的表，字段或者参数过多，我们应该考虑使用Map!

1.UserMapper接口

```java
//用万能Map插入用户
public void addUser2(Map<String,Object> map);

```

2.UserMapper.xml

```java
<!--对象中的属性可以直接取出来 传递map的key-->
<insert id="addUser2" parameterType="map">
    insert into user (id,name,password) values (#{userid},#{username},#{userpassword})
</insert>

```

3.测试

```java
    @Test
    public void test3(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        HashMap<String, Object> map = new HashMap<String, Object>();
        map.put("userid",4);
        map.put("username","王虎");
        map.put("userpassword",789);
        mapper.addUser2(map);
        //提交事务
        sqlSession.commit();
        //关闭资源
        sqlSession.close();
    }

Map传递参数，直接在sql中取出key即可！ 【parameter=“map”】

对象传递参数，直接在sql中取出对象的属性即可！ 【parameter=“Object”】

只有一个基本类型参数的情况下，可以直接在sql中取到

多个参数用Map , 或者注解！
```

## 配置解析

### 1. 核心配置文件

- mybatis-config.xml
- Mybatis的配置文件包含了会深深影响MyBatis行为的设置和属性信息。

```xml
configuration（配置）
    properties（属性）
    settings（设置）
    typeAliases（类型别名）
    typeHandlers（类型处理器）
    objectFactory（对象工厂）
    plugins（插件）
    environments（环境配置）
    	environment（环境变量）
    		transactionManager（事务管理器）
    		dataSource（数据源）
    databaseIdProvider（数据库厂商标识）
    mappers（映射器）
```

### 2. 环境配置 environments

MyBatis 可以配置成适应多种环境

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境**

**学会使用配置多套运行环境！**

Mybatis的**默认事务管理器**是**jdbc**，默认数据源（dataSource）是**POOLED**

### 3. 属性 properties

我们可以通过properties属性来实现引用配置文件

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。【db.poperties】

1.编写一个配置文件

db.properties

```java
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?userSSL=false&useUnicode=ture&characterEncoding=UTF-8&serverTimezone=GMT
username=root
password=
```

1.可以直接引入外部文件
2.可以在其中增加一些属性配置
3.如果两个文件有同一个字段，优先使用外部配置文件的

### 4. 类型别名 typeAliases

- 类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置.
- 意在降低冗余的全限定类名书写。

```java
<!--可以给实体类起别名-->
<typeAliases>
    <typeAlias type="com.kuang.pojo.User" alias="User"/>
</typeAliases>

```

也可以指定一个包，每一个在包 domain.blog 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 domain.blog.Author 的别名为 author,；若有注解，则别名为其注解值。见下面的例子：

```java
<typeAliases>
    <package name="com.kuang.pojo"/>
</typeAliases>

在实体类比较少的时候，使用第一种方式。
如果实体类十分多，建议用第二种扫描包的方式。
第一种可以DIY别名，第二种不行，如果非要改，需要在实体上增加注解。

@Alias("author")
public class Author {
    ...
}

```

### 5. 设置 Settings

**这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。**
![image](https://raw.githubusercontent.com/RbRookie/image-management/master/20211016/image.6gqt6g3mhw40.png)

![image](https://raw.githubusercontent.com/RbRookie/image-management/master/20211016/image.6gqt6g3mhw40.png)

### 6. 其他配置

- typeHandlers（类型处理器）
- objectFactory（对象工厂）
- plugins 插件
  - mybatis-generator-core
  - mybatis-plus
  - 通用mapper

 

### 7. 映射器 mappers

MapperRegistry：注册绑定我们的Mapper文件；

方式一：【推荐使用】

```java
<!--每一个Mapper.xml都需要在MyBatis核心配置文件中注册-->
<mappers>
    <mapper resource="com/kuang/dao/UserMapper.xml"/>
</mappers>
```

方式二：使用class文件绑定注册

```java
<!--每一个Mapper.xml都需要在MyBatis核心配置文件中注册-->
<mappers>
    <mapper class="com.kuang.dao.UserMapper"/>
</mappers>
```

**注意点：**

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下

方式三：使用包扫描进行注入**(和方式二注意点一样)**

```java
<mappers>
    <package name="com.kuang.dao"/>
</mappers>
```

**注意点：**

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下 

### 8. 作用域和生命周期


声明周期和作用域是至关重要的，因为错误的使用会导致非常严重的并发问题。

<img src="https://raw.githubusercontent.com/RbRookie/image-management/master/20211016/image.146tgycim9e.png" alt="image" style="zoom:80%;" />

**SqlSessionFactoryBuilder:**

- 一旦创建了SqlSessionFactory，就不再需要它了
- 局部变量
  **SqlSessionFactory:**
- 说白了就可以想象为：数据库连接池
- SqlSessionFactory一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建一个实例。**
- 因此SqlSessionFactory的最佳作用域是应用作用域（ApplocationContext）。
- 最简单的就是使用单例模式或静态单例模式。

**SqlSession：**

- 连接到连接池的一个请求

- SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。

- 用完之后需要赶紧关闭，否则资源被占用！

  <img src="https://raw.githubusercontent.com/RbRookie/image-management/master/20211016/image.48wcmpiuctk0.png" alt="image" style="zoom:80%;" />
### 9 resultMap

 	可以解决类名和mysql表中**字段名不一致导致查不出对应数据**的问题，也可以直接**MySQL语句直接起别名**。

结果集映射

```xml
id name pwd
id name password
<!--结果集映射-->
<resultMap id="UserMap" type="User">
    <!--column数据库中的字段，property实体类中的属性-->
    <result column="id" property="id"></result>
    <result column="name" property="name"></result>
    <result column="pwd" property="password"></result>
</resultMap>

<select id="getUserList" resultMap="UserMap">
    select * from USER
</select>
```

- resultMap 元素是 MyBatis 中最重要最强大的元素。
- ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。
- ResultMap 的优秀之处——你完全可以不用显式地配置它们。
- 如果这个世界总是这么简单就好了。

### 10、日志

#### 10.1 日志工厂

如果一个数据库操作，出现了异常，我们需要排错，日志就是最好的助手！

曾经：sout、debug

现在：日志工厂
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201017024935492.png#pic_center)

- SLF4J
- LOG4J 【掌握】
- LOG4J2
- JDK_LOGGING
- COMMONS_LOGGING
- STDOUT_LOGGING 【掌握】
- NO_LOGGING
  在MyBatis中具体使用哪一个日志实现，在设置中设定

**STDOUT_LOGGING**

```java
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

<img src="https://raw.githubusercontent.com/RbRookie/image-management/master/20211017/image.png" alt="image" style="zoom:67%;" />

#### 10.2Log4j

什么是Log4j？

- Log4j是Apache的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件；
- 我们也可以控制每一条日志的输出格式；
- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程；
- 最令人感兴趣的就是，这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

1.先导入log4j的包

```java
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2.log4j.properties

```java
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file
#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/hui.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sq1.PreparedStatement=DEBUG
```

3.配置settings为log4j实现

4.测试运行

**Log4j简单使用**

1.在要使用Log4j的类中，导入包 **import org.apache.log4j.Logger**;

2.日志对象，参数为当前类的class对象

```java
Logger logger = Logger.getLogger(UserDaoTest.class);
```

 3.日志测试

```java
    @Test
    public void testLog4j(){
        logger.info("输出信息");
        logger.debug("debug");
    }
```

### 11、Lombok

Lombok项目是一个Java库，它会自动插入编辑器和构建工具中，Lombok提供了一组有用的注释，用来消除Java类中的大量样板代码。仅五个字符(@Data)就可以替换数百行代码从而产生干净，简洁且易于维护的Java类

使用步骤：

1.在IDEA中安装Lombok插件

2.在项目中导入lombok的jar包

```java
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.10</version>
    <scope>provided</scope>
</dependency>
123456
```

3在程序上加注解

```java
@Getter and @Setter
@FieldNameConstants
@ToString
@EqualsAndHashCode
@AllArgsConstructor, @RequiredArgsConstructor and @NoArgsConstructor
@Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger, @CustomLog
@Data
@Builder
@SuperBuilder
@Singular
@Delegate
@Value
@Accessors
@Wither
@With
@SneakyThrows
@val
```

说明：

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
    private String password;
}
```

### 12、多对一处理

```
多个学生一个老师；
alter table student ADD CONSTRAINT fk_tid foreign key (tid) references teacher(id)
```

#### 1. 测试环境搭建

1.导入lombok
2.新建实体类Teacher,Student
3.建立Mapper接口
4.建立Mapper.xml文件
5.在核心配置文件中绑定注册我们的Mapper接口或者文件 【方式很多，随心选】
6.测试查询是否能够成功

### 3.按照结果嵌套处理

```xml
 <select id="getStdudent" resultMap="StudentTeacher">
        select s.id sid,s.name sname,t.name tname
        from student as s, teacher as t
        where s.tid = t.id;
    </select>
    <resultMap id="StudentTeacher" type="com.hui.pojo.Stdudent">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <association property="teacher" javaType="com.hui.pojo.Teacher">
            <result property="name" column="tname"></result>
        </association>
    </resultMap>
```

### 13、一对多处理

```
一个老师多个学生；
对于老师而言，就是一对多的关系；
12
```

#### 1. 环境搭建

**实体类**

```java
@Data
public class Student {
    private int id;
    private String name;
    private int tid;
}

@Data
public class Teacher {
    private int id;
    private String name;

    //一个老师拥有多个学生
    private List<Student> students;
}

```

#### 2. 按照结果嵌套嵌套处理

```java
<!--按结果嵌套查询-->
<select id="getTeacher" resultMap="StudentTeacher">
    SELECT s.id sid, s.name sname,t.name tname,t.id tid FROM student s, teacher t
    WHERE s.tid = t.id AND tid = #{tid}
</select>
<resultMap id="StudentTeacher" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <!--复杂的属性，我们需要单独处理 对象：association 集合：collection
    javaType=""指定属性的类型！
    集合中的泛型信息，我们使用ofType获取
    -->
    <collection property="students" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>

```

#### 小结

1.关联 - association 【多对一】
2.集合 - collection 【一对多】
3.javaType & ofType

1. JavaType用来指定实体类中的类型
2. ofType用来指定映射到List或者集合中的pojo类型，泛型中的约束类型

** 注意点：**

- 保证SQL的可读性，尽量保证通俗易懂
- 注意一对多和多对一，属性名和字段的问题
- 如果问题不好排查错误，可以使用日志，建议使用Log4j

面试高频

- Mysql引擎
- InnoDB底层原理
- 索引
- 索引优化

### 14、动态SQL

**什么是动态SQL：动态SQL就是根据不同的条件生成不同的SQL语句**

**所谓的动态SQL，本质上还是SQL语句，只是我们可以在SQL层面，去执行一个逻辑代码**

```
动态 SQL 是 MyBatis 的强大特性之一。如果你使用过 JDBC 或其它类似的框架，你应该能理解
根据不同条件拼接 SQL 语句有多痛苦，例如拼接时要确保不能忘记添加必要的空格，还要注意去
掉列表最后一个列名的逗号。利用动态 SQL，可以彻底摆脱这种痛苦。
123
```

#### 搭建环境

```java
vCREATE TABLE `mybatis`.`blog`  (
  `id` int(10) NOT NULL AUTO_INCREMENT COMMENT '博客id',
  `title` varchar(30) NOT NULL COMMENT '博客标题',
  `author` varchar(30) NOT NULL COMMENT '博客作者',
  `create_time` datetime(0) NOT NULL COMMENT '创建时间',
  `views` int(30) NOT NULL COMMENT '浏览量',
  PRIMARY KEY (`id`)
)
12345678
```

创建一个基础工程

1.导包

2.编写配置文件

3.编写实体类

```java
@Data
public class Blog {
    private int id;
    private String title;
    private String author;

    private Date createTime;// 属性名和字段名不一致
    private int views;
}
123456789
```

4.编写实体类对应Mapper接口和Mapper.xml文件

#### IF

```java
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <if test="title!=null">
            and title = #{title}
        </if>
        <if test="author!=null">
            and author = #{author}
        </if>
    </where>
</select>
```

### choose (when, otherwise)

### trim、where、set

### SQL片段

有的时候，我们可能会将一些功能的部分抽取出来，方便服用！

1.使用SQL标签抽取公共部分可

```java
<sql id="if-title-author">
    <if test="title!=null">
        title = #{title}
    </if>
    <if test="author!=null">
        and author = #{author}
    </if>
</sql>
```

2.在需要使用的地方使用Include标签引用即可

```java
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <include refid="if-title-author"></include>
    </where>
</select>
```

注意事项：

- 最好基于单标来定义SQL片段
- 不要存在where标签

**动态SQL就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了**

建议：

- 先在Mysql中写出完整的SQL，再对应的去修改成我们的动态SQL实现通用即可

### 14、缓存

#### 14.1 简介

```
查询 ： 连接数据库，耗资源

 一次查询的结果，给他暂存一个可以直接取到的地方 --> 内存：缓存

我们再次查询的相同数据的时候，直接走缓存，不走数据库了
12345
```

1.什么是缓存[Cache]？
1.存在内存中的临时数据
2.将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上（关系型数据库文件）查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题
2.为什么使用缓存？
1.减少和数据库的交互次数，减少系统开销，提高系统效率
3.什么样的数据可以使用缓存？
1.经常查询并且不经常改变的数据 【可以使用缓存】

#### 14.2 MyBatis缓存

- MyBatis包含一个非常强大的查询缓存特性，它可以非常方便的定制和配置缓存，缓存可以极大的提高查询效率。
- MyBatis系统中默认定义了两级缓存：一级缓存和二级缓存
  - 默认情况下，只有一级缓存开启（SqlSession级别的缓存，也称为本地缓存）
  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。
  - 为了提高可扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来定义二级缓存。

#### 14.3 一级缓存

- 一级缓存也叫本地缓存：SqlSession
  - 与数据库同一次会话期间查询到的数据会放在本地缓存中
  - 以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库

测试步骤：

1.开启日志

2.测试在一个Session中查询两次记录

```java
    @Test
    public void test1() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.getUserById(1);
        System.out.println(user);

        System.out.println("=====================================");

        User user2 =  mapper.getUserById(1);
        System.out.println(user2 == user);
    }
123456789101112
```

3.查看日志输出
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201017073516811.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDgyMjQ1NQ==,size_16,color_FFFFFF,t_70#pic_center)
**缓存失效的情况：**

1.查询不同的东西

2.增删改操作，可能会改变原来的数据，所以必定会刷新缓存

3.查询不同的Mapper.xml

4.手动清理缓存

```java
sqlSession.clearCache();
1
```

#### 14.4 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存
- 工作机制
  - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中
  - 如果会话关闭了，这个会员对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中
  - 新的会话查询信息，就可以从二级缓存中获取内容
  - 不同的mapper查询出的数据会放在自己对应的缓存（map）中

一级缓存开启（SqlSession级别的缓存，也称为本地缓存）

- 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。
- 为了提高可扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来定义二级缓存。

步骤：

1.开启全局缓存

```java
<!--显示的开启全局缓存-->
<setting name="cacheEnabled" value="true"/>
12
```

2.在Mapper.xml中使用缓存

```java
<!--在当前Mapper.xml中使用二级缓存-->
<cache
       eviction="FIFO"
       flushInterval="60000"
       size="512"
       readOnly="true"/>
123456
```

3.测试

1.问题：我们需要将实体类序列化，否则就会报错

**小结：**

- 只要开启了二级缓存，在同一个Mapper下就有效
- 所有的数据都会放在一级缓存中
- 只有当前会话提交，或者关闭的时候，才会提交到二级缓存中

#### 14.5 缓存原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201017074031230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDgyMjQ1NQ==,size_16,color_FFFFFF,t_70#pic_center)
**注意：**

- 只有查询才有缓存，根据数据是否需要缓存（修改是否频繁选择是否开启）useCache=“true”

```java
    <select id="getUserById" resultType="user" useCache="true">
        select * from user where id = #{id}
    </select>
```

