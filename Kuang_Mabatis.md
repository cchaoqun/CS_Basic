# 总结

## MyBatis是什么？

MyBatis 是一款优秀的持久层框架，一个半 ORM（对象关系映射）框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和 Java 的 POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

## ORM是什么

ORM（Object Relational Mapping），对象关系映射，是一种为了解决关系型数据库数据与简单Java对象（POJO）的映射关系的技术。

简单的说，ORM是通过使用描述对象和数据库之间映射的元数据，将程序中的对象自动持久化到关系型数据库中。

## 为什么说Mybatis是半自动ORM映射工具？它与全自动的区别在哪里？

Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。

而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。

## 传统JDBC开发存在的问题

- 频繁创建数据库连接对象、释放，容易造成系统资源浪费，影响系统性能。可以使用连接池解决这个问题。但是使用jdbc需要自己实现连接池。
- sql语句定义、参数设置、结果集处理存在硬编码。实际项目中sql语句变化的可能性较大，一旦发生变化，需要修改java代码，系统需要重新编译，重新发布。不好维护。
- 使用preparedStatement向占有位符号传参数存在硬编码，因为sql语句的where条件不一定，可能多也可能少，修改sql还要修改代码，系统不易维护。
- 结果集处理存在重复代码，处理麻烦。如果可以映射成Java对象会比较方便。

## JDBC编程有哪些不足之处，MyBatis是如何解决这些问题的？

- 1、数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库连接池可解决此问题。
  - 解决：在mybatis-config.xml中配置数据链接池，使用连接池管理数据库连接。
- 2、Sql语句写在代码中造成代码不易维护，实际应用sql变化的可能较大，sql变动需要改变java代码。
  - 解决：将Sql语句配置在XXXXmapper.xml文件中与java代码分离。
- 3、向sql语句传参数麻烦，因为sql语句的where条件不一定，可能多也可能少，占位符需要和参数一一对应。
  - 解决： Mybatis自动将java对象映射至sql语句。
- 4、对结果集解析麻烦，sql变化导致解析代码变化，且解析前需要遍历，如果能将数据库记录封装成pojo对象解析比较方便。
  - 解决：Mybatis自动将sql执行结果映射至java对象。

## Mybatis优缺点

- 优点	
  - 基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，
  - SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；
  - 提供XML标签，支持编写动态SQL语句，并可重用
  - 与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接
  - 很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）
  - 提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护
  - 能够与Spring很好的集成
- 缺点
  - SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求
  - SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库
- MyBatis框架适用场景
  - MyBatis专注于SQL本身，是一个足够灵活的DAO层解决方案。
  - 对性能的要求很高，或者需求变化较多的项目，如互联网项目，MyBatis将是不错的选择。

## Hibernate 和 MyBatis 的区别

相同点

- 都是对jdbc的封装，都是持久层的框架，都用于dao层的开发。


不同点

- 映射关系
  - MyBatis 是一个半自动映射的框架，配置Java对象与sql语句执行结果的对应关系，多表关联关系配置简单
  - Hibernate 是一个全表映射的框架，配置Java对象与数据库表的对应关系，多表关联关系配置复杂
- SQL优化和移植性
  - Hibernate 对SQL语句封装，提供了日志、缓存、级联（级联比 MyBatis 强大）等特性，此外还提供 HQL（Hibernate Query Language）操作数据库，
  - 数据库无关性支持好，但会多消耗性能。如果项目需要支持多种数据库，代码开发量少，但SQL语句优化困难。
  - MyBatis 需要手动编写 SQL，支持动态 SQL、处理列表、动态生成表名、支持存储过程。开发工作量相对大些。直接使用SQL语句操作数据库，不支持数据库无关性，但sql语句优化容易。
- 开发难易程度和学习成本
  - Hibernate 是重量级框架，学习使用门槛高，适合于需求相对稳定，中小型的项目，比如：办公自动化系统
  - MyBatis 是轻量级框架，学习使用门槛低，适合于需求变化频繁，大型的项目，比如：互联网电子商务系统

### 总结

MyBatis 是一个小巧、方便、高效、简单、直接、半自动化的持久层框架，

Hibernate 是一个强大、方便、高效、复杂、间接、全自动化的持久层框架。
MyBatis的解析和运行原理
MyBatis编程步骤是什么样的？
1、 创建SqlSessionFactory

2、 通过SqlSessionFactory创建SqlSession

3、 通过sqlsession执行数据库操作

4、 调用session.commit()提交事务

5、 调用session.close()关闭会话

## 请说说MyBatis的工作原理

在学习 MyBatis 程序之前，需要了解一下 MyBatis 工作原理，以便于理解程序。MyBatis 的工作原理如下图

![MyBatis_Working_Procedures](Kuang_Mabatis.assets/MyBatis_Working_Procedures.png)

- 1）读取 MyBatis 配置文件：mybatis-config.xml 为 MyBatis 的全局配置文件，配置了 MyBatis 的运行环境等信息，例如数据库连接信息。
- 2）加载映射文件。映射文件即 SQL 映射文件，该文件中配置了操作数据库的 SQL 语句，需要在 MyBatis 配置文件 mybatis-config.xml 中加载。mybatis-config.xml 文件可以加载多个映射文件，每个文件对应数据库中的一张表。
- 3）构造会话工厂：通过 MyBatis 的环境等配置信息构建会话工厂 SqlSessionFactory。
- 4）创建会话对象：由会话工厂创建 SqlSession 对象，该对象中包含了执行 SQL 语句的所有方法。

- 5）Executor 执行器：MyBatis 底层定义了一个 Executor 接口来操作数据库，它将根据 SqlSession 传递的参数动态地生成需要执行的 SQL 语句，同时负责查询缓存的维护。

- 6）MappedStatement 对象：在 Executor 接口的执行方法中有一个 MappedStatement 类型的参数，该参数是对映射信息的封装，用于存储要映射的 SQL 语句的 id、参数等信息。

- 7）输入参数映射：输入参数类型可以是 Map、List 等集合类型，也可以是基本数据类型和 POJO 类型。输入参数映射过程类似于 JDBC 对 preparedStatement 对象设置参数的过程。

- 8）输出结果映射：输出结果类型可以是 Map、 List 等集合类型，也可以是基本数据类型和 POJO 类型。输出结果映射过程类似于 JDBC 对结果集的解析过程。

## MyBatis的功能架构是怎样的

我们把Mybatis的功能架构分为三层：

![aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL015QmF0aXMlRTYlQTElODYlRTYlOUUlQjYlRTYlODAlQkIlRTclQkIlOTMvTXlCYXRpcyVFNSU4QSU5RiVFOCU4MyVCRCVFNiU5RSVCNiVFNiU5RSU4NC5wbmc](Kuang_Mabatis.assets/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL015QmF0aXMlRTYlQTElODYlRTYlOUUlQjYlRTYlODAlQkIlRTclQkIlOTMvTXlCYXRpcyVFNSU4QSU5RiVFOCU4MyVCRCVFNiU5RSVCNiVFNiU5RSU4NC5wbmc.png)

- API接口层：提供给外部使用的接口API，开发人员通过这些本地API来操纵数据库。接口层一接收到调用请求就会调用数据处理层来完成具体的数据处理。
- 数据处理层：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等。它主要的目的是根据调用的请求完成一次数据库操作。
- 基础支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的东西，将他们抽取出来作为最基础的组件。为上层的数据处理层提供最基础的支撑。

## MyBatis的框架架构设计是怎么样的

![aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL015QmF0aXMlRTYlQTElODYlRTYlOUUlQjYlRTYlODAlQkIlRTclQkIlOTMvTXlCYXRpcyVFNiVBMSU4NiVFNiU5RSVCNiVFNiU5RSVCNiVFNiU](Kuang_Mabatis.assets/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL015QmF0aXMlRTYlQTElODYlRTYlOUUlQjYlRTYlODAlQkIlRTclQkIlOTMvTXlCYXRpcyVFNiVBMSU4NiVFNiU5RSVCNiVFNiU5RSVCNiVFNiU.webp)


这张图从上往下看。MyBatis的初始化，会从mybatis-config.xml配置文件，解析构造成Configuration这个类，就是图中的红框。

- (1)加载配置：配置来源于两个地方，一处是配置文件，一处是Java代码的注解，将SQL的配置信息加载成为一个个MappedStatement对象（包括了传入参数映射配置、执行的SQL语句、结果映射配置），存储在内存中。

- (2)SQL解析：当API接口层接收到调用请求时，会接收到传入SQL的ID和传入对象（可以是Map、JavaBean或者基本数据类型），Mybatis会根据SQL的ID找到对应的MappedStatement，然后根据传入参数对象对MappedStatement进行解析，解析后可以得到最终要执行的SQL语句和参数。

- (3)SQL执行：将最终得到的SQL和参数拿到数据库进行执行，得到操作数据库的结果。

- (4)结果映射：将操作数据库的结果按照映射的配置进行转换，可以转换成HashMap、JavaBean或者基本数据类型，并将最终结果返回。


## 为什么需要预编译

SQL 预编译指的是数据库驱动在发送 SQL 语句和参数给 DBMS 之前对 SQL 语句进行编译，这样 DBMS 执行 SQL 时，就不需要重新编译。

- JDBC 中使用对象 PreparedStatement 来抽象预编译语句，使用预编译。
- 预编译阶段可以优化 SQL 的执行。预编译之后的 SQL 多数情况下可以直接执行，DBMS 不需要再次编译，越复杂的SQL，编译的复杂度将越大，预编译阶段可以合并多次操作为一个操作。
- 同时预编译语句对象可以重复利用。把一个 SQL 预编译后产生的 PreparedStatement 对象缓存下来，下次对于同一个SQL，可以直接使用这个缓存的 PreparedState 对象。Mybatis默认情况下，将对所有的 SQL 进行预编译。

## Mybatis都有哪些Executor执行器？它们之间的区别是什么？

Mybatis有三种基本的Executor执行器，SimpleExecutor、ReuseExecutor、BatchExecutor。

- SimpleExecutor：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。

- ReuseExecutor：执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String, Statement>内，供下一次使用。简言之，就是重复使用Statement对象。

- BatchExecutor：执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。


作用范围：Executor的这些特点，都严格限制在SqlSession生命周期范围内。

## Mybatis中如何指定使用哪一种Executor执行器？

在Mybatis配置文件中，在设置（settings）可以指定默认的ExecutorType执行器类型，也可以手动给DefaultSqlSessionFactory的创建SqlSession的方法传递ExecutorType类型参数，如SqlSession openSession(ExecutorType execType)。

配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。

## Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

延迟加载原理:

- 使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，
- 拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，
- 然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的。




## #{}和${}的区别

- #{}是占位符，预编译处理；${}是拼接符，字符串替换，没有预编译处理。
- Mybatis在处理#{}时，#{}传入参数是以字符串传入，会将SQL中的#{}替换为?号，调用PreparedStatement的set方法来赋值。

- Mybatis在处理时, 是原值传入 ， 就 是 把 {}时，就是把{}替换成变量的值，相当于JDBC中的Statement编译

- 变量替换后，#{} 对应的变量自动加上单引号 ‘’；变量替换后，${} 对应的变量不会加上单引号 ‘’

- #{} 可以有效的防止SQL注入，提高系统安全性；${} 不能防止SQL 注入

- #{} 的变量替换是在DBMS 中；${} 的变量替换是在 DBMS 外



## 模糊查询like语句该怎么写

- （1）’%${question}%’ 可能引起SQL注入，不推荐

- （2）"%"#{question}"%" 注意：因为#{…}解析成sql语句时候，会在变量外侧自动加单引号’ '，所以这里 % 需要使用双引号" "，不能使用单引号 ’ '，不然会查不到任何结果。

- （3）CONCAT(’%’,#{question},’%’) 使用CONCAT()函数，推荐

- （4）使用bind标签

  ```xml
  <select id="listUserLikeUsername" resultType="com.jourwon.pojo.User">
  　　<bind name="pattern" value="'%' + username + '%'" />
  　　select id,sex,age,username,password from person where username LIKE #{pattern}
  </select>
  
  ```



## 在mapper中如何传递多个参数

### **方法1：顺序传参法**

```java
public User selectUser(String name, int deptId);

<select id="selectUser" resultMap="UserResultMap">
    select * from user
    where user_name = #{0} and dept_id = #{1}
</select>

```

\#{}里面的数字代表传入参数的顺序。

这种方法不建议使用，sql层表达不直观，且一旦顺序调整容易出错。

### **方法2：@Param注解传参法**

```java
public User selectUser(@Param("userName") String name, int @Param("deptId") deptId);

<select id="selectUser" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>

```

\#{}里面的名称对应的是注解@Param括号里面修饰的名称。

这种方法在参数不多的情况还是比较直观的，推荐使用。



### **方法3：Map传参法**

```java
public User selectUser(Map<String, Object> params);

<select id="selectUser" parameterType="java.util.Map" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>

```

\#{}里面的名称对应的是Map里面的key名称。

这种方法适合传递多个参数，且参数易变能灵活传递的情况。

### **方法4：Java Bean传参法**

```java
public User selectUser(User user);

<select id="selectUser" parameterType="com.jourwon.pojo.User" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>

```

\#{}里面的名称对应的是User类里面的成员属性。

这种方法直观，需要建一个实体类，扩展不容易，需要加属性，但代码可读性强，业务逻辑处理方便，推荐使用。



## Mybatis如何执行批量操作

### 使用foreach标签

foreach的主要用在构建in条件中，它可以在SQL语句中进行迭代一个集合。foreach标签的属性主要有item，index，collection，open，separator，close。

- item　　表示集合中每一个元素进行迭代时的别名，随便起的变量名；
- index　　指定一个名字，用于表示在迭代过程中，每次迭代到的位置，不常用；
- open　　表示该语句以什么开始，常用“(”；
- separator表示在每次进行迭代之间以什么符号作为分隔符，常用“,”；
- close　　表示以什么结束，常用“)”。

在使用foreach的时候最关键的也是最容易出错的就是collection属性，该属性是必须指定的，但是在不同情况下，该属性的值是不一样的，主要有一下3种情况：

- 如果传入的是单参数且参数类型是一个List的时候，collection属性值为list
- 如果传入的是单参数且参数类型是一个array数组的时候，collection的属性值为array
- 如果传入的参数是多个的时候，我们就需要把它们封装成一个Map了，当然单参数也可以封装成map，实际上如果你在传入参数的时候，在MyBatis里面也是会把它封装成一个Map的，
- map的key就是参数名，所以这个时候collection属性值就是传入的List或array对象在自己封装的map里面的key

具体用法如下：

```xml
<!-- 批量保存(foreach插入多条数据两种方法)
       int addEmpsBatch(@Param("emps") List<Employee> emps); -->
<!-- MySQL下批量保存，可以foreach遍历 mysql支持values(),(),()语法 --> //推荐使用
<insert id="addEmpsBatch">
    INSERT INTO emp(ename,gender,email,did)
    VALUES
    <foreach collection="emps" item="emp" separator=",">
        (#{emp.eName},#{emp.gender},#{emp.email},#{emp.dept.id})
    </foreach>
</insert>

<!-- 这种方式需要数据库连接属性allowMutiQueries=true的支持
 如jdbc.url=jdbc:mysql://localhost:3306/mybatis?allowMultiQueries=true -->  
<insert id="addEmpsBatch">
    <foreach collection="emps" item="emp" separator=";">                                 
        INSERT INTO emp(ename,gender,email,did)
        VALUES(#{emp.eName},#{emp.gender},#{emp.email},#{emp.dept.id})
    </foreach>
</insert>

```



## 如何获取生成的主键

**对于支持主键自增的数据库（MySQL）**

parameterType 可以不写，Mybatis可以推断出传入的数据类型。如果想要访问主键，那么应当parameterType 应当是java实体或者Map。这样数据在插入之后 可以通过ava实体或者Map 来获取主键值。通过 getUserId获取主键

```xml
<insert id="insertUser" useGeneratedKeys="true" keyProperty="userId" >
    insert into user( 
    user_name, user_password, create_time) 
    values(#{userName}, #{userPassword} , #{createTime, jdbcType= TIMESTAMP})
</insert>

```



## 当实体类中的属性名和表中的字段名不一样 ，怎么办

第1种： 通过在查询的SQL语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。

```xml
<select id="getOrder" parameterType="int" resultType="com.jourwon.pojo.Order">
       select order_id id, order_no orderno ,order_price price form orders where order_id=#{id};
</select>

```

第2种： 通过`<resultMap>`来映射字段名和实体类属性名的一一对应的关系。

```xml
<select id="getOrder" parameterType="int" resultMap="orderResultMap">
	select * from orders where order_id=#{id}
</select>

<resultMap type="com.jourwon.pojo.Order" id="orderResultMap">
    <!–用id属性来映射主键字段–>
    <id property="id" column="order_id">

    <!–用result属性来映射非主键字段，property为实体类属性名，column为数据库表中的属性–>
    <result property ="orderno" column ="order_no"/>
    <result property="price" column="order_price" />
</reslutMap>

```



## Mapper 编写有哪几种方式？

### 第一种：接口实现类继承 SqlSessionDaoSupport：

使用此种方法需要编写mapper 接口，mapper 接口实现类、mapper.xml 文件。

（1）在 sqlMapConfig.xml 中配置 mapper.xml 的位置

```xml
<mappers>
    <mapper resource="mapper.xml 文件的地址" />
    <mapper resource="mapper.xml 文件的地址" />
</mappers>

```

（2）定义 mapper 接口

（3）实现类集成 SqlSessionDaoSupport

mapper 方法中可以 this.getSqlSession()进行数据增删改查。

（4）spring 配置

```xml
<bean id=" " class="mapper 接口的实现">
    <property name="sqlSessionFactory"
    ref="sqlSessionFactory"></property>
</bean>

```

### 第二种：使用 org.mybatis.spring.mapper.MapperFactoryBean：

（1）在 sqlMapConfig.xml 中配置 mapper.xml 的位置，如果 mapper.xml 和mappre 接口的名称相同且在同一个目录，这里可以不用配置

```xml
<mappers>
    <mapper resource="mapper.xml 文件的地址" />
    <mapper resource="mapper.xml 文件的地址" />
</mappers>

```

（2）定义 mapper 接口：

（3）mapper.xml 中的 namespace 为 mapper 接口的地址

（4）mapper 接口中的方法名和 mapper.xml 中的定义的 statement 的 id 保持一致

（5）Spring 中定义

```xml
<bean id="" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="mapper 接口地址" />
    <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>

```

### 第三种：使用 mapper 扫描器：

（1）mapper.xml 文件编写：

mapper.xml 中的 namespace 为 mapper 接口的地址；

mapper 接口中的方法名和 mapper.xml 中的定义的 statement 的 id 保持一致；

如果将 mapper.xml 和 mapper 接口的名称保持一致则不用在 sqlMapConfig.xml中进行配置。

（2）定义 mapper 接口：

注意 mapper.xml 的文件名和 mapper 的接口名称保持一致，且放在同一个目录

（3）配置 mapper 扫描器：

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="mapper 接口包地址
    "></property>
    <property name="sqlSessionFactoryBeanName"
    value="sqlSessionFactory"/>
</bean>

```

（4）使用扫描器后从 spring 容器中获取 mapper 的实现对象。



## 什么是MyBatis的接口绑定？有哪些实现方式？

接口绑定，就是在MyBatis中任意定义接口，然后把接口里面的方法和SQL语句绑定，我们直接调用接口方法就可以，这样比起原来了SqlSession提供的方法我们可以有更加灵活的选择和设置。

### 接口绑定有两种实现方式

- 通过注解绑定，就是在接口的方法上面加上 @Select、@Update等注解，里面包含Sql语句来绑定；

- 通过xml里面写SQL来绑定， 在这种情况下，要指定xml映射文件里面的namespace必须为接口的全路径名。当Sql语句比较简单时候，用注解绑定， 当SQL语句比较复杂时候，用xml绑定，一般用xml绑定的比较多。


## 使用MyBatis的mapper接口调用时有哪些要求？

- 1、Mapper接口方法名和mapper.xml中定义的每个sql的id相同。
- 2、Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同。

- 3、Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同。

- 4、Mapper.xml文件中的namespace即是mapper接口的类路径。

## 最佳实践中，通常一个Xml映射文件，都会写一个Dao接口与之对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗

### 接口方法与SQL的对应

- Dao接口，就是人们常说的Mapper接口，接口的全限名，就是映射文件中的namespace的值，
- 接口的方法名，就是映射文件中MappedStatement的id值，
- 接口方法内的参数，就是传递给sql的参数。

### Dao接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略。

- Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement，
  - 举例：com.mybatis3.mappers.StudentDao.findStudentById，
  - 可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面id = findStudentById的MappedStatement。
  - 在Mybatis中，每一个\<select>、\<insert>、\<update>、\<delete>标签，都会被解析为一个MappedStatement对象。

### Dao接口的工作原理

是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回。

## Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

不同的Xml映射文件，如果配置了namespace，那么id可以重复；

如果没有配置namespace，那么id不能重复；毕竟namespace不是必须的，只是最佳实践而已。

原因就是namespace+id是作为Map<String, MappedStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。

有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。

## 简述Mybatis的Xml映射文件和Mybatis内部数据结构之间的映射关系？

- Mybatis将所有Xml配置信息都封装到All-In-One重量级对象Configuration内部。
- 在Xml映射文件中，\<parameterMap>标签会被解析为ParameterMap对象，其每个子元素会被解析为ParameterMapping对象。
- \<resultMap>标签会被解析为ResultMap对象，其每个子元素会被解析为ResultMapping对象。
- 每一个\<select>、\<insert>、\<update>、\<delete>标签均会被解析为MappedStatement对象，标签内的sql会被解析为BoundSql对象。

## Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

- 第一种是使用\<resultMap>标签，逐一定义列名和对象属性名之间的映射关系。
- 第二种是使用sql列的别名功能，将列别名书写为对象属性名，比如T_NAME AS NAME，对象属性名一般是name，小写，但是列名不区分大小写，Mybatis会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以写成T_NAME AS NaMe，Mybatis一样可以正常工作。


有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

## Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？

还有很多其他的标签，\<resultMap>、\<parameterMap>、\<sql>、\<include>、\<selectKey>，加上动态sql的9个标签，trim|where|set|foreach|if|choose|when|otherwise|bind等，

其中\<sql>为sql片段标签，通过\<include>标签引入sql片段，\<selectKey>为不支持自增的主键生成策略标签。

## Mybatis映射文件中，如果A标签通过include引用了B标签的内容，请问，B标签能否定义在A标签的后面，还是说必须定义在A标签的前面？

虽然Mybatis解析Xml映射文件是按照顺序解析的，但是，被引用的B标签依然可以定义在任何地方，Mybatis都可以正确识别。

- Mybatis解析A标签，发现A标签引用了B标签，但是B标签尚未解析到，尚不存在，
- 此时，Mybatis会将A标签标记为未解析状态，然后继续解析余下的标签，包含B标签，
- 待所有标签解析完毕，Mybatis会重新解析那些被标记为未解析的标签，此时再解析A标签时，B标签已经存在，A标签也就可以正常解析完成了。

## 高级查询MyBatis实现一对一，一对多有几种方式，怎么操作的？

- 有联合查询和嵌套查询。联合查询是几个表联合查询，只查询一次，通过在resultMap里面的association，collection节点配置一对一，一对多的类就可以完成
- 嵌套查询是先查一个表，根据这个表里面的结果的外键id，去再另外一个表里面查询数据，也是通过配置association，collection，但另外一个表的查询通过select节点配置。


## Mybatis是否可以映射Enum枚举类？

Mybatis可以映射枚举类，不单可以映射枚举类，Mybatis可以映射任何对象到表的一列上。映射方式为自定义一个TypeHandler，实现TypeHandler的setParameter()和getResult()接口方法。

TypeHandler有两个作用，一是完成从javaType至jdbcType的转换，二是完成jdbcType至javaType的转换，体现为setParameter()和getResult()两个方法，分别代表设置sql问号占位符参数和获取列查询结果。



### Mybatis动态sql是做什么的？都有哪些动态sql？能简述一下动态sql的执行原理不？

Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql，完成逻辑判断和动态拼接sql的功能，Mybatis提供了9种动态sql标签trim|where|set|foreach|if|choose|when|otherwise|bind。

其执行原理为，使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。



## Mybatis是如何进行分页的？分页插件的原理是什么？

Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页，可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

举例：select * from student，拦截sql后重写为：select t.* from (select * from student) t limit 0, 10

## 简述Mybatis的插件运行原理，以及如何编写一个插件。

Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，

Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，

每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。

实现Mybatis的Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。

## 缓存

Mybatis的一级、二级缓存
1）一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。

2）二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置<cache/> ；

3）对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。



# Mybatis

环境:

- JDK1.8
- Mysql5.7 8.0
- maven 3.6.1
- IDEA

回顾:

- JDBC
- Mysql
- Java基础
- Maven
- Junit

官网

```
https://mybatis.org/mybatis-3/zh/index.html
```

## 什么是Mybatis?

- MyBatis 是一款优秀的**持久层框架，**
- 它支持自定义 SQL、存储过程以及高级映射。
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
- MyBatis 本是apache的一个开源项目ibatis, 2010年这个项目由apache 迁移到了google code，并且改名为MyBatis 。
- 2013年11月迁移到**Github** .

## 如何获得Mybatis

- maven仓库

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
```

- Mybatis官方文档 : http://www.mybatis.org/mybatis-3/zh/index.html
- GitHub : https://github.com/mybatis/mybatis-3

## 持久化

### 数据持久化

- 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
- 内存: 断电即失
- 数据库(jdbc), io文件持久化
- 生活: 冷藏 罐头

### 为什么需要持久化?

- 有一些对象, 不能让他丢掉
- 内存太贵了



## 持久层

- Dao层, Service层, Controller层
  - 完成持久化工作的代码块
  - 层是界限十分明显的

## 为什么需要Mybatis?

- 帮助程序猿将数据存入到数据库中
- 方便
- 传统的JDBC代码复杂, 简化, 框架
- 不用Mybatis也可以, 更容易上手. **技术没有高低之分**
- 优点:
  - 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
  - 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
  - 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
  - 提供映射标签，支持对象与数据库的orm字段关系映射
  - 提供对象关系映射标签，支持对象关系组建维护
  - 提供xml标签，支持编写动态sql。
- 最重要的一点: 使用的人多
- Spring SpringMVC SpringBoot

# 第一个Mybatis程序

## 思路:搭建环境-->导入Mybatis-->编写代码-->测试

## 搭建环境

### 搭建数据库

```mysql
CREATE DATABASE `Mybatis`;

USE `Mybatis`;

CREATE TABLE `user`(
	`id` INT(20) PRIMARY KEY NOT NULL,
	`name` VARCHAR(30) DEFAULT NULL,
	`pwd` VARCHAR(30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `user` (`id`, `name`, `pwd`) VALUES
(1,'name1', 'pwd1'),
(2,'name2', 'pwd2'),
(3,'name3', 'pwd3');	
```

### 新建项目

1. 新建一个Maven项目

2. 删除src 目录, 让其成为父工程

3. 导入maven依赖

   ```xml
   <properties>
       <java.version>1.8</java.version>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
       <encoding>UTF-8</encoding>
   </properties>
   <!--导入依赖-->
       <dependencies>
           <!--Mysql驱动-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.23</version>
           </dependency>
           <!--Mybatis-->
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.5.2</version>
           </dependency>
           <!--Junit-->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.12</version>
           </dependency>
   
       </dependencies>
   ```
   
   

### 创建一个模块

- 编写Mybatis核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--Configuration核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC&amp;useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>
    <!--每一个Mapper.xml都需要在Mybatis核心配置文件中注册!-->
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```



- 编写Mybatis工具类

```java
//sqlSessionFactory -->sqlSession
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static{
        InputStream inputStream = null;
        try {
            //使用Mybatis第一步: 获取sqlSession对象
            String resource = "mybatis-config.xml";
            inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。例如：

    public static SqlSession getSession(){
        return sqlSessionFactory.openSession();
    }
}
```



### 编写代码

- 实体类

```java
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }
}

```



- Dao接口

```java
public interface UserDao {
    List<User> getUserList();
}
```



- 接口实现类由原来的UserDaoImpl转变为一个Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.kuang.dao.UserDao">
    <!--select查询语句-->
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from mybatis.user
    </select>


</mapper>
```



## 测试

### 注意点

MapperRegistry 在核心配置文件中注册mappers

```xml
<!--每一个Mapper.xml都需要在Mybatis核心配置文件中注册!-->
<mappers>
    <mapper resource="UserMapper.xml"/>
</mappers>
```



- junit测试

```java
public class UserDaoTest {

    @Test
    public void getUserList() {
        //获取SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //方式一: getMapper
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> userList = mapper.getUserList();

        //方式二
//        List<User> userList = sqlSession.selectList("com.kuang.dao.UserDao.getUserList");
        for(User user:userList){
            System.out.println(user);
        }

        //关闭sqlSession
        sqlSession.close();

    }
}
```

- 可能会遇到的问题

  1. 配置文件没有注册

  2. 绑定接口错误

  3. 方法名不对

  4. 返回类型错误

  5. Maven导出资源问题

     1. ```xml
        <!--在build中配置resource, 来防止我们资源导出失败的问题-->
            <build>
                <resources>
                    <resource>
                        <directory>src/main/java</directory>
                        <includes>
                            <include>**/*.properties</include>
                            <include>**/*.xml</include>
                        </includes>
                        <filtering>false</filtering>
                    </resource>
                    <resource>
                        <directory>src/main/resources</directory>
                        <includes>
                            <include>**/*.properties</include>
                            <include>**/*.xml</include>
                        </includes>
                        <filtering>false</filtering>
                    </resource>
                </resources>
            </build>
        ```



# CRUD

## namespace

namespace中的包名和Dao/Mapper接口的包名一致

## 操作流程

参数解释

- id: 就是对应的namespace中的方法名
- resultType: sql 语句执行的返回值
- parameterType: 参数类型

### 编写接口

```java
public interface UserMapper {
    //查询全部用户
    List<User> getUserList();

    //根据id查询用户
    User getUserById(int id);

    //insert一个用户
    int addUser(User user);

    //update
    int updateUser(User user);

    //delete
    int deleteUserById(int id);
    
}
```



### 编写Mapper中的SQL语句(Select, Update, Add, Delete)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.kuang.dao.UserMapper">
    <!--select查询语句-->
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from mybatis.user
    </select>

    <select id="getUserById" parameterType="int" resultType="com.kuang.pojo.User">
        select * from user where id = #{id}
    </select>

    <!--插入-->
    <!--对象中的属性可以直接取出-->
    <insert id="addUser" parameterType="com.kuang.pojo.User">
        insert into mybatis.user(id,name,pwd) values (#{id}, #{name}, #{pwd});
    </insert>

    <!--更新-->
    <update id="updateUser" parameterType="com.kuang.pojo.User">
        update mybatis.user set `name`=#{name}, `pwd`=#{pwd} where `id`=#{id};
    </update>

    <!--删除-->
    <delete id="deleteUserById" parameterType="int">
        delete from mybatis.user where `id`=#{id};
    </delete>

</mapper>
```



### 测试

注意点:

- 增删改需要提交事务

```java
public class UserMapperTest {
    @Test
    public void getUserList() {
        //获取SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //方式一: getMapper
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.getUserList();
        //方式二
//        List<User> userList = sqlSession.selectList("com.kuang.dao.UserDao.getUserList");
        for(User user:userList){
            System.out.println(user);
        }
        //关闭sqlSession
        sqlSession.close();
    }
    @Test
    public void getUserById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.getUserById(2);
        System.out.println(user);

        sqlSession.close();
    }

    @Test
    public void addUser(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int res = mapper.addUser(new User(6,"name4", "pwd4"));
        if(res>0){
            System.out.println("插入成功");
        }
        //提交事务
        sqlSession.commit();

        sqlSession.close();
    }

    @Test
    public void updateUser(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int res = mapper.updateUser(new User(5, "name555", "pwd555"));
        if(res>0){
            System.out.println("更新成功");
        }
        sqlSession.commit();


        sqlSession.close();
    }

    @Test
    public void deleteUserById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int res = mapper.deleteUserById(5);
        if(res>0){
            System.out.println("删除成功");
        }
        sqlSession.commit();
        sqlSession.close();
    }
}
```



## 分析错误

- 标签不要匹配错
- resource绑定mapper需要使用路径(com/kuang/...)
- 程序配置文件必须符合规范
- NullPointerException没有注册到资源
- maven资源没有导出



## 万能Map

假设我们的实体类, 或者数据库中的表, 字段或者参数过多, 我们应当考虑使用Map

```java
//万能Map
    int addUser2(Map<String, Object> map);
```

```xml
<!--map
    传递map的key
    -->
    <insert id="addUser2" parameterType="map">
        insert into mybatis.user(id,name,pwd) values (#{userId}, #{userName}, #{userPwd});
    </insert>
```

```java
@Test
public void addUser2(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    HashMap<String, Object> map = new HashMap<>();
    map.put("userId",5);
    map.put("userName","name555");
    map.put("userPwd","pwd555");
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    mapper.addUser2(map);

    sqlSession.commit();
    sqlSession.close();

}
```

- Map传递参数, 直接在SQL中取出key即可          [parameterType="map"]
- 对象传递参数, 直接在SQL中取出对象属性即可  [parameterType="map"]
- 只有一个基本类型参数的情况下, 可以直接在SQL中取到
- 多个参数用Map, 或者注解

## 思考题

### 模糊查询?

1. java代码执行的时候, 传递通配符%%

   1. ```java
      List<User> userList = mapper.getUserLike("%李%");
      ```

      

2. 在sql拼接中使用通配符

   1. ```xml
      select * from mybatis.user where name like "%"#{value}"%";
      ```



# 配置解析

## 核心配置文件

- mybatis-config.xml

- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。

  ```
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
  <!-- 注意元素节点的顺序！顺序不对会报错 -->
  ```



## 环境配置(environments)

### environments元素

```xml
<environments default="development">
 <environment id="development">
   <transactionManager type="JDBC">
     <property name="..." value="..."/>
   </transactionManager>
   <dataSource type="POOLED">
     <property name="driver" value="${driver}"/>
     <property name="url" value="${url}"/>
     <property name="username" value="${username}"/>
     <property name="password" value="${password}"/>
   </dataSource>
 </environment>
</environments>
```

- 配置MyBatis的多套运行环境，将SQL映射到多个不同的数据库上，必须指定其中一个为默认运行环境（通过default指定）

- 子元素节点：**environment**

- - dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。

  - 数据源是必须配置的。

  - 有三种内建的数据源类型

    ```
    type="[UNPOOLED|POOLED|JNDI]"）
    ```

  - unpooled：这个数据源的实现只是每次被请求时打开和关闭连接。

  - **pooled**：这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来 , 这是一种使得并发 Web 应用快速响应请求的流行处理方式。

  - jndi：这个数据源的实现是为了能在如 Spring 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的引用。

  - 数据源也有很多第三方的实现，比如dbcp，c3p0，druid等等....

## 属性(properties)

### 配置文件的属性标签必须按照顺序来写

![image-20210524161611634](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524161611634.png)

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。

### 编写一个配置文件

db.properties

```xml
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC&useSSL=true&useUnicode=true&characterEncoding=UTF-8
username=root
password=123
```

### 在核心配置文件中引入

```xml
<!--引入外部配置文件-->
<properties resource="db.properties">
    <property name="username" value="root"/>
    <property name="password" value="1231"/>
</properties>
```

- 可以直接引入外部文件
- 可以在其中增加一些属性
- 如果properties标签中的property标签与外部文件中的字段相同, 优先使用外部配置文件的值



## 类型别名(TypeAlias)

### 方式一

- 类型别名是为 Java 类型设置一个短的名字。
- 它只和 XML 配置有关，存在的意义仅在于用来减少类完全限定名的冗余。

```xml
<!--可以给实体类起别名-->
    <typeAliases>
        <typeAlias type="com.kuang.pojo.User" alias="User"/>
    </typeAliases>
```

### 方式二

- 也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如：
- 扫描实体类的包, 他的默认别名就是这个类的类名, 首字母小写

```xml
<typeAliases>
    <package name="com.kuang.pojo"/>
</typeAliases>
```

### 总结

- 实体类较少, 使用第一种

- 实体类较多, 使用第二种

- 第一种可以自定义别名, 第二种不行, 如果必须改, 必须在实体类上增加注解

  - ```java
    @Alias("user")
    public class User {
    ```



## 设置

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。 下表描述了设置中各项设置的含义、默认值等。

![image-20210524164451961](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524164451961.png)

![image-20210524164435802](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524164435802.png)

## 其他配置

- [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
- [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
- [plugins（插件）]



## 映射器(mapper)

MapRegistry: 注册绑定我们的Mapper文件

### 方式一

```xml
<!--每一个Mapper.xml都需要在Mybatis核心配置文件中注册!-->
<mappers>
    <mapper resource="com/kuang/dao/UserMapper.xml"/>
</mappers>
```

### 方式二: 使用class文件绑定注册

```xml
<mappers>
    <mapper class="com.kuang.dao.UserMapper"/>
</mappers>
```

#### 注意点

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下

### 方式三:使用扫描包进行注入绑定

```xml
<mappers>
    <package name="com.kuang.dao"/>
</mappers>
```

#### 注意点

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下

### 练习

- 将数据库配置文件外部引入
- 实体类别名
- 保证UserMapper接口和UserMapper.xml放在同名包下



## 声明周期和作用域

作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的并发问题。

<img src="C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524170056728.png" alt="image-20210524170056728" style="zoom:50%;" />

### **SqlSessionFacoryBuilder**

- 这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了
- 局部变量

### SqlSessionFacory

- 可以类比于数据库连接池
- SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。
-  SqlSessionFactory 的最佳作用域是应用作用域
- 最简单的就是使用单例模式或者静态单例模式

### SqlSession

- 连接到连接池的一个请求

- 每个线程都应该有它自己的 SqlSession 实例。
- SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域
- 用完之后需要赶紧关闭, 否则资源被占用

<img src="C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524170416342.png" alt="image-20210524170416342" style="zoom:50%;" />

- 这里面的每一个Mapper都代表一个具体的业务



# 解决属性名和字段名不一致的问题

## 数据库字段

![image-20210524170531664](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524170531664.png)

## 拷贝之前项目新建,测试实体类字段不一致的情况

```java
public class User {
    private int id;
    private String name;
    private String password;
```

## 测试出现问题

![image-20210524171324008](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524171324008.png)

```xml
select * from mybatis.user where id = #{id}
select id,name,pwd from mybatis.user where id=#{id}
```

## 解决方法

### 起别名

```xml
<select id="getUserById" parameterType="int" resultType="com.kuang.pojo.User">
    select id,name,pwd as password from mybatis.user where id=#{id}
</select>
```

### ResultMap

结果集映射

```
数据库:	id	name	pwd
实体类:	id	name	password
```

```xml
<!--结果集映射-->
<resultMap id="UserMap" type="User">
<!--column数据库中的字段名, property实体类的属性名-->
<result column="id" property="id"/>
<result column="name" property="name"/>
<result column="pwd" property="password"/>
</resultMap>

<select id="getUserById" parameterType="int" resultMap="UserMap">
select * from mybatis.user where id = #{id}
</select>
```

- `resultMap` 元素是 MyBatis 中最重要最强大的元素。
- ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。



# 日志

## 日志工厂

- 如果一个数据库操作, 出现了异常, 我们需要排错, 日志是最好的助手
- 曾经: syso Debug
- 现在:日志工厂

![image-20210524180016536](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524180016536.png)

- SLF4J
- LOG4J [掌握]
- LOG4J2
- JDK_LOGGING
- COMMONS_LOGGING
- STDOUT_LOGGING [掌握]
- NO_LOGGING

### **标准日志实现**

指定 MyBatis 应该使用哪个日志记录实现。如果此设置不存在，则会自动发现日志记录实现。

```xml
<settings>
       <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

测试，可以看到控制台有大量的输出！我们可以通过这些输出来判断程序到底哪里出了Bug

![image-20210524180523165](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524180523165.png)



## LOG4J

### 什么是LOG4J

- Log4j是Apache的一个开源项目
- 通过使用Log4j，我们可以控制日志信息输送的目的地：控制台，文本，GUI组件....
- 我们也可以控制每一条日志的输出格式；
- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。最令人感兴趣的就是，这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

1. 先导入LOG4J的包

   ```xml
   <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.17</version>
   </dependency>
   ```

   

2. log4j.properties

   ```xml
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
   log4j.appender.file.File=./log/kuang.log
   log4j.appender.file.MaxFileSize=10mb
   log4j.appender.file.Threshold=DEBUG
   log4j.appender.file.layout=org.apache.log4j.PatternLayout
   log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
   
   #日志输出级别
   log4j.logger.org.mybatis=DEBUG
   log4j.logger.java.sql=DEBUG
   log4j.logger.java.sql.Statement=DEBUG
   log4j.logger.java.sql.ResultSet=DEBUG
   log4j.logger.java.sql.PreparedStatement=DEBUG
   ```

   

3. 配置log4j为日志的实现

   ```xml
   <settings>
   	<setting name="logImpl" value="LOG4J"/>
   </settings>
   ```

4. Log4j的使用, 运行之前的查询

   1. ![image-20210524181824828](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524181824828.png)

### 简单使用

1. 在要使用Log4j的类中, 导入包 

   1. ```
      import org.apache.log4j.Logger;
      ```

2. 生成日志对象, 参数为当前类的class

   1. ```java
      static Logger logger = Logger.getLogger(UserMapperTest.class);
      ```

3. 日志级别

   1. ```java
      @Test
      public void testLog4J(){
          logger.info("info:进入了testLog4J");
          logger.debug("debug:进入了testLog4J");
          logger.error("error:进入了testLog4J");
      }
      ```



# 分页

## 思考,为什么要分页?

- 减少数据的处理量

## 使用Limit分页

```sql
Select * from user limit startIndex, pageSize;
Select * from user limit n; #[0,n]
```



## 使用Mybatis实现分页

1. 接口

   ```java
   //分页
   List<User> getUserByLimit(Map<String, Integer> map);
   ```

2. Mapper.xml

   ```xml
   <select id="getUserByLimit" parameterType="map" resultType="User" resultMap="UserMap">
       select * from mybatis.user limit #{startIndex}, #{pageSize};
   </select>
   ```

3. 测试

   ```java
   @Test
   public void getUserByLimit(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
   
       HashMap<String, Integer> map = new HashMap<String, Integer>();
       map.put("startIndex",1);
       map.put("pageSize",2);
   
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       List<User> userList = mapper.getUserByLimit(map);
   
       System.out.println(userList);
   
   
       sqlSession.close();
   }
   ```



## RowBounds分页

1. 不再使用SQL实现分页

   1. 接口

      ```java
      //分页2
      List<User> getUserByRowBounds();
      ```

   2. mapper.xml

      ```xml
      <!--分页2-->
      <select id="getUserByRowBounds" resultMap="UserMap">
          select * from mybatis.user;
      </select>
      ```

   3. 测试

      ```java
      @Test
      public void getUserByRowBounds(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
      
          //RowBounds实现
          RowBounds rowBounds = new RowBounds(1, 2);
      
          //通过java代码层面实现分页
          List<User> userList = sqlSession.selectList("com.kuang.dao.UserMapper.getUserByRowBounds", null, rowBounds);
          for(User user:userList){
          System.out.println(user);
          }
          sqlSession.close();
      }
      ```

## 分页插件

![image-20210524202305859](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524202305859.png)

# 使用注解开发

## 面向接口编程

- 大家之前都学过面向对象编程，也学习过接口，但在真正的开发中，很多时候我们会选择面向接口编程
- **根本原因 :  解耦 , 可拓展 , 提高复用 , 分层开发中 , 上层不用管具体的实现 , 大家都遵守共同的标准 , 使得开发变得容易 , 规范性更好**
- 在一个面向对象的系统中，系统的各种功能是由许许多多的不同对象协作完成的。在这种情况下，各个对象内部是如何实现自己的,对系统设计人员来讲就不那么重要了；
- 而各个对象之间的协作关系则成为系统设计的关键。小到不同类之间的通信，大到各模块之间的交互，在系统设计之初都是要着重考虑的，这也是系统设计的主要工作内容。面向接口编程就是指按照这种思想来编程。



### **关于接口的理解**

- 接口从更深层次的理解，应是定义（规范，约束）与实现（名实分离的原则）的分离。

- 接口的本身反映了系统设计人员对系统的抽象理解。

- 接口应有两类：

- - 第一类是对一个个体的抽象，它可对应为一个抽象体(abstract class)；
  - 第二类是对一个个体某一方面的抽象，即形成一个抽象面（interface）；

- 一个体有可能有多个抽象面。抽象体与抽象面是有区别的。



### **三个面向区别**

- 面向对象是指，我们考虑问题时，以对象为单位，考虑它的属性及方法 .
- 面向过程是指，我们考虑问题时，以一个具体的流程（事务过程）为单位，考虑它的实现 .
- 接口设计与非接口设计是针对复用技术而言的，与面向对象（过程）不是一个问题.更多的体现就是对系统整体的架构



## 使用注解开发

1. 注解在接口上实现

   ```java
   @Select("select * from user;")
   List<User> getUsers();
   ```

2. 需要在核心配置文件中绑定接口

   ```xml
   <!--绑定接口-->
   <mappers>
       <mapper class="com.kuang.dao.UserMapper"/>
   </mappers>
   ```

3. 测试

   ```java
   @Test
   public void getUsers(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
   
       //底层主要应用反射
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       List<User> users = mapper.getUsers();
   
       System.out.println(users);
   
       sqlSession.close();
   
   
   }
   ```

   

- 本质: 反射机制实现
- 底层: 动态代理

## Mybatis详细执行流程

![image-20210524204813922](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524204813922.png)







## CRUD

1. 我们可以在工具类创建的时候实现自动提交事务

   ```java
   public static SqlSession getSqlSession(){
       //true 代表自动提交事务 aucommit=true
       return sqlSessionFactory.openSession(true);
   }
   ```

2. 编写接口, 增加注解

   ```java
   public interface UserMapper {
       //查询所有
       @Select("select * from user;")
       List<User> getUsers();
   
       //根据id查询
       //方法存在多个参数,所有的参数都需加上 @Param("")
       @Select("select * from user where id=#{id};")
       User getUserById(@Param("id") int id, @Param("name") String name);
   
   
       //添加
       @Insert("insert into user(id,name,pwd) values(#{id}, #{name}, #{password})")
       int addUser(User user);
   
       //更新
       @Update("update user set name=#{name},pwd=#{password} where id=#{id};")
       int updateUser(User user);
   
       //删除
       @Delete("delete from user where id = #{uid};")
       int deleteUser(@Param("uid")int id);
   }
   ```

   

3. 测试类

4. 注意: 必须要将接口注册到核心配置文件

   ```xml
   <!--绑定接口-->
   <mappers>
       <mapper class="com.kuang.dao.UserMapper"/>
   </mappers>
   ```

   

## 关于@Param()注解

- 基本类型的参数或者String类型, 需要加上
- 引用类型不需要加
- 如果只有一个基本类型的话, 可以忽略, 但是建议加上
- 我们在SQL中引用的就是我们这里的@Param()照片那个设定的属性名



# Lombok

```java
Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.
```

- java library
- plugs
- build tools
- with one annotation

## 使用步骤

1. 在IDEA安装Lombok插件

2. 在项目中带入lombok的jar包

   ```
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.10</version>
   </dependency>
   ```

3. 注释解析

   ```
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
   @var
   experimental @var
   @UtilityClass
   Lombok config system
   ```

   说明:

   ```
   @Data: 无参构造, get set  toString hashcode equals
   @AllArgsConstructor
   @NoArgsConstructor
   @EqualsAndHashCode
   @ToString
   Getter
   ```

   

# 多对一的处理

1. 示例

   - 多个学生, 对应一个老师
   - 对于学生这边, **关联**, 多个学生, 关联一个老师 [多对一]
   - 对于老师而言, **集合**, 一个老师有很多学生[一对多]

2. sql

   ```sql
   CREATE TABLE `teacher` (
     `id` INT(10) NOT NULL,
     `name` VARCHAR(30) DEFAULT NULL,
     PRIMARY KEY (`id`)
   ) ENGINE=INNODB DEFAULT CHARSET=utf8
   
   INSERT INTO teacher(`id`, `name`) VALUES (1, '秦老师'); 
   
   CREATE TABLE `student` (
     `id` INT(10) NOT NULL,
     `name` VARCHAR(30) DEFAULT NULL,
     `tid` INT(10) DEFAULT NULL,
     PRIMARY KEY (`id`),
     KEY `fktid` (`tid`),
     CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
   ) ENGINE=INNODB DEFAULT CHARSET=utf8
   
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES (1, '小明', 1); 
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES (2, '小红', 1); 
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES (3, '小张', 1); 
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES (4, '小李', 1); 
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES (5, '小王', 1);
   ```

   ![image-20210524235822371](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210524235822371.png)



## 测试环境搭建

1. 导入lombok

   ```
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.10</version>
   </dependency>
   ```

2. 新建实体类Teacher Student

   ```java
   @Data
   public class Teacher {
       private int id;
       private String name;
   }
   
   @Data
   public class Student {
       private int id;
       private String name;
       //学生需要关联一个老师
       private Teacher taecher;
   }
   ```

   

3. 新建Mapper接口

   ![image-20210525002759149](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525002759149.png)

4. 建立Mapper.xml文件

   ![image-20210525002736050](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525002736050.png)

5. 在核心配置文件中绑定注册我们的Mapper接口或者文件[方式很多]

   ```xml
   <mappers>
       <mapper class="com.kuang.dao.TeacherMapper"/>
       <mapper class="com.kuang.dao.StudentMapper"/>
   </mappers>
   ```

   

6. 测试查询是否能够成功

   ```java
   @Test
   public void getTeacher(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       TeacherMapper mapper = sqlSession.getMapper(TeacherMapper.class);
       Teacher teacher = mapper.getTeacher(1);
       System.out.println(teacher);
       sqlSession.close();
   }
   ```



## 按查询嵌套处理

```xml
<!--
    需求：获取所有学生及对应老师的信息
    思路：
     1. 获取所有学生的信息
     2. 根据获取的学生信息的老师ID->获取该老师的信息
     3. 思考问题，这样学生的结果集中应该包含老师，该如何处理呢，数据库中我们一般使用关联查询？
         1. 做一个结果集映射：StudentTeacher
         2. StudentTeacher结果集的类型为 Student
         3. 学生中老师的属性为teacher，对应数据库中为tid。
            多个 [1,...）学生关联一个老师=> 一对一，一对多
         4. 查看官网找到：association – 一个复杂类型的关联；使用它来处理关联查询
     -->
<select id="getStudent" resultMap="StudentTeacher">
    select * from student;
</select>
<resultMap id="StudentTeacher" type="Student">

    <!--association关联属性 property属性名 javaType属性类型 column在多的一方的表中的列名-->
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>
<!--
    这里传递过来的id，只有一个属性的时候，下面可以写任何值
    association中column多参数配置：
       column="{key=value,key=value}"
       其实就是键值对的形式，key是传给下个sql的取值名称，value是片段一中sql查询的字段名。
    -->
<select id="getTeacher" resultType="Teacher">
    select * from teacher where id =#{id};
</select>
```

## 按照结果嵌套处理

```xml
<!--
按查询结果嵌套处理
思路：
   1. 直接查询出结果，进行结果集的映射
-->
<select id="getStudent2" resultMap="StudentTeacher2">
	select s.id sid, s.name sname, t.name tname
	from student s, teacher t
	where s.tid = t.id;
</select>
<resultMap id="StudentTeacher2" type="Student">
	<result property="id" column="sid"/>
	<result property="name" column="sname"/>
    <!--关联对象property 关联对象在Student实体类中的属性-->
	<association property="teacher" javaType="Teacher">
		<result property="name" column="tname"/>
	</association>
</resultMap>
```



## 回顾MySql 多对一查询方式

- 子查询
- 联表查询



# 一对多处理

一对多的理解：

- 一个老师拥有多个学生
- 如果对于老师这边，就是一个一对多的现象，即从一个老师下面拥有一群学生（集合）！

## 环境搭建

1. 环境搭建 和刚才一样

2. 实体类

   ```java
   @Data
   public class Student {
       private int id;
       private String name;
       //学生需要关联一个学生
       private int tid;
   }
   @Data
   public class Teacher {
       private int id;
       private String name;
       private List<Student> students;
   }
   ```

## 按照结果嵌套处理

```xml
<!--按结果嵌套查询
   思路:
       1. 从学生表和老师表中查出学生id，学生姓名，老师姓名
       2. 对查询出来的操作做结果集映射
           1. 集合的话，使用collection！
               JavaType和ofType都是用来指定对象类型的
               JavaType是用来指定pojo中属性的类型
               ofType指定的是映射到list集合属性中pojo的类型。
   -->
<select id="getTeacher" resultMap="TeacherStudent">
    select s.id sid, s.name sname, t.name tname, t.id tid
    from student s, teacher t
    where s.tid = t.id and t.id = #{tid}
</select>
<resultMap id="TeacherStudent" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <!--复杂的属性, 我们需要单独处理, 对象association 集合collection
        javaType = "" 指向属性的类型
        集合中的泛型信息, 我们使用ofType获取
        -->
    <collection property="students" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```







## 按照查询嵌套处理

```xml
<!--按照查询嵌套处理-->
<select id="getTeacher2" resultMap="TeacherStudent2">
    select * from teacher where id = #{tid}
</select>
<resultMap id="TeacherStudent2" type="Teacher">
    <collection property="students" javaType="ArrayList" ofType="Student" column="id" select="getStudentByTeacherId"/>
</resultMap>

<select id="getStudentByTeacherId" resultType="Student">
    select * from student where tid = #{tid};
</select>
```



## 小结

1. 关联-association
2. 集合-collection
3. 所以association是用于一对一和多对一，而collection是用于一对多的关系
4. JavaType和ofType都是用来指定对象类型的
   1. JavaType是用来指定pojo中属性的类型
   2. ofType指定的是映射到list集合属性中pojo的类型。

## **注意说明：**

1、保证SQL的可读性，尽量通俗易懂

2、根据实际要求，尽量编写性能更高的SQL语句

3、注意属性名和字段不一致的问题

4、注意一对多和多对一 中：字段和属性对应的问题

5、尽量使用Log4j，通过日志来查看自己的错误





# 动态SQL

**动态SQL指的是根据不同的查询条件 , 生成不同的Sql语句.**

```
官网描述：
MyBatis 的强大特性之一便是它的动态 SQL。如果你有使用 JDBC 或其它类似框架的经验，你就能体会到根据不同条件拼接 SQL 语句的痛苦。例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL 这一特性可以彻底摆脱这种痛苦。
虽然在以前使用动态 SQL 并非一件易事，但正是 MyBatis 提供了可以被用在任意 SQL 映射语句中的强大的动态 SQL 语言得以改进这种情形。
动态 SQL 元素和 JSTL 或基于类似 XML 的文本处理器相似。在 MyBatis 之前的版本中，有很多元素需要花时间了解。MyBatis 3 大大精简了元素种类，现在只需学习原来一半的元素便可。MyBatis 采用功能强大的基于 OGNL 的表达式来淘汰其它大部分元素。

  - if
  - choose (when, otherwise)
  - trim (where, set)
  - foreach
```

## 搭建环境

### sql

```sql
CREATE TABLE `blog` (
`id` varchar(50) NOT NULL COMMENT '博客id',
`title` varchar(100) NOT NULL COMMENT '博客标题',
`author` varchar(30) NOT NULL COMMENT '博客作者',
`create_time` datetime NOT NULL COMMENT '创建时间',
`views` int(30) NOT NULL COMMENT '浏览量'
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

### 创建新的基础工程

![image-20210525164059165](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525164059165.png)



### IDUtils工具类

```javA
public class IDUtils {
    //生成随机的id
    public static String getId(){
        return UUID.randomUUID().toString().replaceAll("-", "");
    }
    @Test
    public void testGetId(){
        System.out.println(getId());
        System.out.println(getId());
        System.out.println(getId());
    }
}
```

### 实体类

```java
public class Blog {
   private String id;
   private String title;
   private String author;
   private Date createTime;
   private int views;
   //set，get....
}
```

### 编写Mapper接口及xml文件

```java
public interface BlogMapper {}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.dao.BlogMapper">

    <insert id="addBlog" parameterType="blog">
        insert into mybatis.blog(id,title,author, create_time,views)
        values(#{id},#{title},#{author},#{createTime},#{views});
    </insert>


</mapper>
```

### mybatis核心配置文件，下划线驼峰自动转换

```xml
<settings>
    <!--标准的日志工厂的实现-->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
    <!--是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。-->
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>

<mappers>
    <mapper resource="com/kuang/dao/BlogMapper.xml"/>
</mappers>
```



### 插入初始数据

1. 编写接口

   ```java
   //新增一个博客
   int addBlog(Blog blog);
   ```

2. sql配置文件

   ```xml
   <insert id="addBlog" parameterType="blog">
     insert into blog (id, title, author, create_time, views)
     values (#{id},#{title},#{author},#{createTime},#{views});
   </insert>
   ```

3. 初始化博客方法

   ```java
    @Test
   public void addBlogTest() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
       Blog blog = new Blog();
       blog.setId(IDUtils.getId());
       blog.setTitle("Mybatis");
       blog.setAuthor("狂神说");
       blog.setCreateTime(new Date());
       blog.setViews(9999);
   
       mapper.addBlog(blog);
   
       blog.setId(IDUtils.getId());
       blog.setTitle("Java");
       mapper.addBlog(blog);
   
       blog.setId(IDUtils.getId());
       blog.setTitle("Spring");
       mapper.addBlog(blog);
   
       blog.setId(IDUtils.getId());
       blog.setTitle("微服务");
       mapper.addBlog(blog);
   
       sqlSession.close();
   
   }
   ```

   

![image-20210525161935164](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525161935164.png)



## if

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from blog where 1=1
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</select>
```

## choose (when, otherwise)

有时候，我们不想使用所有的条件，而只是想从多个条件中选择一个使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

```xml
<select id="queryBlogChoose" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <choose>
            <when test="title!=null">
                title = #{title}
            </when>
            <when test="author!=null">
                author = #{author}
            </when>
            <otherwise>
                views = #{views}
            </otherwise>
        </choose>
    </where>
</select>
```



## where

```xml
<!--IF 查询-->
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <if test="title != null">
            title = #{title}
        </if>
        <if test="author != null">
            and author = #{author}
        </if>
    </where>
</select>
```



## trim Set

```xml
<!--Set更新-->
<update id="updateBlog" parameterType="map" >
    update blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    where id = #{id};
</update>
```



## SQL片段

有时候可能某个 sql 语句我们用的特别多，为了增加代码的重用性，简化代码，我们需要将这些代码抽取出来，然后使用时直接调用。

1. 使用sql标签抽取公共的部分

   ```xml
   <!--sql片段-->
   <sql id="if-title-author">
       <if test="title != null">
           title = #{title}
       </if>
       <if test="author != null">
           and author = #{author}
       </if>
   </sql>
   ```

2. 在需要使用的地方使用include标签引用即可

   ```xml
   <!--IF 查询-->
   <select id="queryBlogIF" parameterType="map" resultType="blog">
       select * from blog
       <where>
           <include refid="if-title-author"/>
       </where>
   </select>
   ```

3. 注意:

   - 最好基于单表来定义SQL片段
   - 不要存在where标签



## foreach

*foreach* 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符，看它多智能！

**提示** 你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象作为集合参数传递给 *foreach*。当使用可迭代对象或者数组时，index 是当前迭代的序号，item 的值是本次迭代获取到的元素。当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。

将数据库中前三个数据的id修改为1,2,3；

需求：我们需要查询 blog 表中 id 分别为1,2,3的博客信息

![image-20210525180318385](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525180318385.png)

1. 编写接口

   ```java
   List<Blog> queryBlogForeach(Map map);
   ```

2. 编写SQL语句

   ```xml
    <!--foreach 查询-->
   <select id="queryBlogForeach" parameterType="map" resultType="blog">
       select * from blog
       <where>
           <!--
          collection:指定输入对象中的集合属性
          item:每次遍历生成的对象
          open:开始遍历时的拼接字符串
          close:结束时拼接的字符串
          separator:遍历对象之间需要拼接的字符串
          select * from blog where 1=1 and (id=1 or id=2 or id=3)
        	-->
           <foreach collection="ids" item="id" open="and (" separator="or" close=")">
               id = #{id}
           </foreach>
       </where>
   </select>
   ```

3. 测试

   ```java
   @Test
   public void testQueryBlogForeach(){
      SqlSession session = MybatisUtils.getSession();
      BlogMapper mapper = session.getMapper(BlogMapper.class);
   
      HashMap map = new HashMap();
      List<Integer> ids = new ArrayList<Integer>();
      ids.add(1);
      ids.add(2);
      ids.add(3);
      map.put("ids",ids);
   
      List<Blog> blogs = mapper.queryBlogForeach(map);
   
      System.out.println(blogs);
   
      session.close();
   }
   ```



## 动态SQL 总结

==其实动态 sql 语句的编写往往就是一个拼接的问题，为了保证拼接准确，我们最好首先要写原生的 sql 语句出来，然后在通过 mybatis 动态sql 对照着改，防止出错。多在实践中使用才是熟练掌握它的技巧。==



# 缓存

## 简介

1. 什么是缓存 [ Cache ]？
   - 存在内存中的临时数据。
   - 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。\
2. 为什么使用缓存？
   - 减少和数据库的交互次数，减少系统开销，提高系统效率。
3. 什么样的数据能使用缓存？
   - 经常查询并且不经常改变的数据。[可以使用缓存]



## Mybatis缓存

- MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。

- MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**

- - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。
  - 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存



## 一级缓存

一级缓存也叫本地缓存：

- 与数据库同一次会话期间查询到的数据会放在本地缓存中。
- 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；

1. 测试
   1. 开启日志
   2. 测试在一个Session中查询两次相同的记录
   3. 查看日志输出
   4. ![image-20210525184630416](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525184630416.png)

### 缓存失效

1. 查询不同的东西
2. 增删改操作, 可能会改变原来的数据 所以必定会刷新缓存
   1. ![image-20210525185449734](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525185449734.png)
3. 查询不同的Mapper.xml
4. 手动清理缓存 

### 小结

一级缓存默认开启, 只有在一次sqlSession中有效, 也就是拿到连接到关闭连接这个时间段

一级缓存就是于一个Map

## 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存

- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；

- 工作机制

- - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
  - 新的会话查询信息，就可以从二级缓存中获取内容；
  - 不同的mapper查出的数据会放在自己对应的缓存（map）中



### 步骤

1. 开启全局缓存

   ```xml
   <!--显示开启全局缓存-->
   <setting name="cacheEnabled" value="true"/>
   ```

2. 在要使用二级缓存中的Mapper中开启

   ```
   <!--在当前Mapper.xml中使用二级缓存-->
   可以无参
   <cache/>
   也可以自定义参数
   <cache 	eviction="FIFO"
           flushInterval="60000"
           size="512"
           readOnly="true"/>
   ```

3. 测试

   1. 问题: 我们需要将实体类序列化!否则会报错

   2. ```
      Caused by: java.io.NotSerializableException: com.kuang.pojo.User
      ```

### 小结

- 只要开启了二级缓存，我们在同一个Mapper中的查询，可以在二级缓存中拿到数据
- 查出的数据都会被默认先放在一级缓存中
- 只有会话提交或者关闭以后，一级缓存中的数据才会转到二级缓存中

## 缓存原理

1. 先查看二级缓存 找到直接返回
2. 二级缓存没找到 继续找一级缓存
3. 一级缓存没有 数据库查询

![image-20210525193803448](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210525193803448.png)



## 自定义缓存-ehcache

Ehcache是一种广泛使用的java分布式缓存，用于通用缓存；

要在应用程序中使用Ehcache，需要引入依赖的jar包

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
   <groupId>org.mybatis.caches</groupId>
   <artifactId>mybatis-ehcache</artifactId>
   <version>1.1.0</version>
</dependency>
```

在mapper.xml中使用对应的缓存即可

```xml
<mapper namespace = “org.acme.FooMapper” >
   <cache type = “org.mybatis.caches.ehcache.EhcacheCache” />
</mapper>
```

编写ehcache.xml文件，如果在加载时未找到/ehcache.xml资源或出现问题，则将使用默认配置。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
        updateCheck="false">
   <!--
      diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
      user.home – 用户主目录
      user.dir – 用户当前工作目录
      java.io.tmpdir – 默认临时文件路径
    -->
   <diskStore path="./tmpdir/Tmp_EhCache"/>
   
   <defaultCache
           eternal="false"
           maxElementsInMemory="10000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="259200"
           memoryStoreEvictionPolicy="LRU"/>

   <cache
           name="cloud_user"
           eternal="false"
           maxElementsInMemory="5000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           memoryStoreEvictionPolicy="LRU"/>
   <!--
      defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
    -->
   <!--
     name:缓存名称。
     maxElementsInMemory:缓存最大数目
     maxElementsOnDisk：硬盘最大缓存个数。
     eternal:对象是否永久有效，一但设置了，timeout将不起作用。
     overflowToDisk:是否保存到磁盘，当系统当机时
     timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
     timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
     diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
     diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
     diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
     memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
     clearOnFlush：内存数量最大时是否清除。
     memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
     FIFO，first in first out，这个是大家最熟的，先进先出。
     LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
     LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
  -->

</ehcache>
```





































