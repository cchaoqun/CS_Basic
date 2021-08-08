[TOC]

# 快捷键

1. ctrl+H 查看类的结构

# IOC 操作 Bean 管理

https://mrbird.cc/Spring-Bean-Regist.html

https://github.com/wuyouzhuguli/SpringAll

https://potoyang.gitbook.io/spring-in-action-v5/di-1-zhang-spring-ru-men/1.1-shen-me-shi-spring

## 什么是Bean管理

1. Bean管理指的是两个操作
2. Spring 创建对象 实例化类的对象
3. Spring 注入属性, 类的属性赋值



## Bean管理操作有两种方式

1. 基于xml配置文件方式实现
2. 基于注解方式实现

### 基于xml方式

#### xml方式创建对象

 1. 在spring配置文件中,使用bean标签, 标签里添加对应属性,可以实现对象的创建

     ```xml
     <!--1 配置User对象的创建 -->
     <bean id="对象别名" class="对象对应类所在的路径"> </bean>
     <bean id="user" class="com.AtGuiGu.Spring.User"></bean>
     ```

     

 2. bean标签里很多属性
     1. id: 获取对象的唯一标识,通过这个标签可以获取对应的bean进而创建对象 (不能加特殊符号)
     2. class:  创建对象类的全路径(包类路径)
     3. name:   早期的属性,可以添加特殊符号

 3. 创建对象的时候,默认也是执行无参的构造方法,完成对象的创建,所以正常情况下bean对应的类中必须有无参构造

#### xml方式注入属性

1. DI: 依赖注入, 就是注入属性, IOC中的一种属性,需要在创建对象的基础上实现
   1. 使用set方法注入
      1. 类中创建属性
      2. 创建属性对应的set方法
       3. spring配置文件配置对象的创建, 配置属性注入(bean标签内的property标签对属性进行赋值)
      
      ```xml
      <!--2.1 set方法注入属性 -->
          <bean id="book" class="com.AtGuiGu.Spring.testDemo.Book">
              <!-- 使用properties完成属性注入
                  name:类属性名称
                  value:向对应属性注入的值
              -->
              <property name="bname" value="三国"></property>
              <property name="bauthor" value="三国的作者"></property>
              <!-- null值 -->
      <!--        <property name="address"><null/></property>-->
              <!-- 属性值包含特殊符号
                  1 把<>进行转义 &lt;代表左括号 &gt;代表右括号
                  2 把待特殊符号内容写到CDATA 语法为 <value> <![CDATA[内容]]> </value>
              -->
      <!--        <property name="address" value="&lt;&lt;南京&gt;&gt;"></property>-->
              <property name="address"><value><![CDATA[<<南京>>]]></value>
              </property>
          </bean>
      ```
      
      
      
   2. 使用有参构造注入

      - 使用constructor-arg标签 
        1. 利用 name value 赋值实现属性的注入
        2. 利用index value赋值 index从0开始对应构造器的第一个形参

      ```xml
      <bean id="orders" class="com.AtGuiGu.Spring.testDemo.Orders">
              <!--  使用constructor-arg标签 以及利用 name value 赋值实现属性的注入      -->
              <constructor-arg name="oname" value="order_01"></constructor-arg>
              <constructor-arg name="address" value="1654 McIntyre St,MI,USA"></constructor-arg>
      
              <!-- 也可以通过index对属性赋值, 0代表构造器第一个形参1代表第二个       -->
      <!--        <constructor-arg index="0" value="abc"></constructor-arg>-->
      <!--        <constructor-arg index="1" value="abcd"></constructor-arg>-->
          </bean>
      ```

      

   3. 简化的set方法 -->p名称空间注入

      1. 在配置文件的beans标签内添加p名称空间注入

         - 赋值xmlns 加 ::p 类似于新变量p, 赋值原本连接, 最后的bean改成p

         ```xml
         xmlns:p="http://www.springframework.org/schema/p"
         ```

          - 只需在bean标签内部最后添加 p:类属性名="赋值" 即可完成属性注入

            ```xml
            - <bean id="book" class="com.AtGuiGu.Spring.testDemo.Book" p:bname="bookname2" p:bauthor="bookname2's_author">
            - </bean>
            ```

#### xml注入其他类型属性

1. 字面量

   1. null值

      ```xml
       <property name="address"><null/></property>-->
      ```

      

   2. 属性值包含特殊符号

      ```xml
      <!-- 属性值包含特殊符号
                  1 把<>进行转义 &lt;代表左尖括号 &gt;代表右尖括号
                  2 把待特殊符号内容写到CDATA 语法为 <value> <![CDATA[内容]]> </value>
              -->
      		<property name="address" value="&lt;&lt;南京&gt;&gt;"></property>
              <property name="address"><value><![CDATA[<<南京>>]]></value>
              </property>
      ```

2. 注入属性

   1. 外部bean

      1. 创建两个类service类和dao类

      2. 在service调用dao里面的方法

         1. service类中将dao类作为属性, 并且创建set方法
         2. 先创建两个bean, 在一个service bean中 设置property name="外部bean属性名" ref="外部bean别名"

      3. 在spring配置文件中进行配置

         ```xml
         <!-- service和dao对象的创建-->
         <bean id="userService" class="com.AtGuiGu.Spring.service.UserService">
             <!-- 注入userDao对象
                 name:类的属性名称
                 ref: 创建userDao对象bean标签id值
             -->
             <property name="userDao" ref="userDaoImpl"></property>
         </bean>
         
         <!-- 接口不能创建对象,需要实例化实现子类-->
         <bean id="userDaoImpl" class="com.AtGuiGu.Spring.dao.UserDaoImpl"></bean>
         ```

         

   2. 注入内部bean和级联赋值

      1. 一对多关系: 部门和员工 部门是一 员工是多

      2. 实体类中表示一对多

         1. 创建两个类 Dept部门  Emp员工

         2. Emp中有Dept作为属性,表示每一个员工都属于一个部门 即部门1---->员工多

         3. 通过bean 内部property标签 内嵌套 内部bean

            ```xml
            <!-- 内部bean -->
                <bean id="emp" class="com.AtGuiGu.Spring.bean.Emp">
                    <!-- 设置两个普通的属性-->
                    <property name="ename" value="emp1"></property>
                    <property name="gender" value="male"></property>
                    <!-- 设置对象类属性-->
                    <property name="dept">
                        <!-- 属性内部设置内部bean-->
                        <bean id="dempt" class="com.AtGuiGu.Spring.bean.Dept">
                            <!-- 内部bean内设置bean对应的属性-->
                            <property name="dname" value="security"></property>
                        </bean>
                    </property>
                </bean>
            ```

         4. 注入属性-级联赋值

            1. 方式一, 

               1. 将内部类写在外部bean中并且赋值, 通过引用bean的property标签ref=被引用bean的别名的方式进行级联赋值

               ```xml
               <!-- 级联赋值 -->
                   <bean id="emp" class="com.AtGuiGu.Spring.bean.Emp">
                       <!-- 设置两个普通的属性-->
                       <property name="ename" value="emp2"></property>
                       <property name="gender" value="female"></property>
                       <!-- 级联赋值-->
                       <property name="dept" ref="dempt"></property>
                   </bean>
                   <!--内部bean-->
                   <bean id="dempt" class="com.AtGuiGu.Spring.bean.Dept">
                       <!-- 内部bean设置bean对应的属性-->
                       <property name="dname" value="sports"></property>
                   </bean>
               ```

            2. 方式二

               1. 通过在引用bean引入内部bean声明在外部的bean以后 
               2. 设置property标签, 内部类(dept)属性.(dname)属性名 来修改类的对象的属性
               3. 需要在Emp类的里面写上Dept的get方法

               ```xml
               <!-- 级联赋值 -->
                   <bean id="emp" class="com.AtGuiGu.Spring.bean.Emp">
                       <!-- 设置两个普通的属性-->
                       <property name="ename" value="emp2"></property>
                       <property name="gender" value="female"></property>
                       <!-- 级联赋值-->
                       <property name="dept" ref="dempt"></property>
                       <!-- 设置/修改dept的属性的类属性dname的值为IT-->
                       <property name="dept.dname" value="IT"></property>
                   </bean>
                   <!--内部bean-->
                   <bean id="dempt" class="com.AtGuiGu.Spring.bean.Dept">
                       <!-- 内部bean设置bean对应的属性-->
                       <property name="dname" value="sports"></property>
                   </bean>
               ```



#### xml注入集合属性

1. 注入数组类型属性

2. 注入List集合类型属性

3. 注入Map集合类型属性

   1. 创建类,包含数组,List,Map,Set类型的属性

   2. Spring配置 对于不同的集合属性,property标签内都有对于的标签与其赋值

      ```xml
      <bean id="stu" class="com.AtGuiGu.Spring_CollectionType.Step1_Arrray.Stu">
              <!-- 数组类型属性注入-->
              <property name="courses">
                  <array>
                      <value>java</value>
                      <value>database</value>
                  </array>
              </property>
              <!-- list类型属性注入-->
              <property name="list">
                  <list>
                      <value>xiaowang</value>
                      <value>xiaohong</value>
                  </list>
              </property>
              <!-- map类型属性注入-->
              <property name="map">
                  <map>
                      <entry key="JAVA" value="java"></entry>
                      <entry key="PHP" value="php"></entry>
                      <entry key="PYHTHON" value="python"></entry>
                  </map>
              </property>
              <!-- set类型属性注入-->
              <property name="set">
                  <set>
                      <value>MySQL</value>
                      <value>Redias</value>
                  </set>
              </property>
          </bean>
      ```

4. 集合里面设置对象类型值

   1. 创建同一个类的多个对象,每个对象使用不同的别名并且同时给对象的属性赋值

      ```xml
      <!-- 创建多个course对象-->
          <bean id="course1" class="com.AtGuiGu.Spring_CollectionType.Step1_Arrray.Course">
              <property name="cname" value="Math"></property>
          </bean>
      
          <bean id="course2" class="com.AtGuiGu.Spring_CollectionType.Step1_Arrray.Course">
              <property name="cname" value="English"></property>
          </bean>
      ```

      

   2. 泛型为对象的list集合属性

   3. 标签 property --> list -->ref -->bean="创建同一个类不同对象的别名"

      ```xml
      <!-- 注入List集合类型, 值是对象-->
              <property name="courseList">
                  <list>
                      <ref bean="course1"></ref>
                      <ref bean="course2"></ref>
                  </list>
              </property>
      ```

      

5. 把集合注入部分提取出来

   1. 在spring配置文件中引入名称空间 util

      1. beans标签内添加一下一行 由原始的xmlns修改而来
      2. 赋值一行, xmlns后面添加:util, 网址最后的beans变成util即可
      3. 同时最后一个xsi, 赋值一份放在后面, 将beans替换成util即可

      ```xml
      
      <beans ....
      xmlns:util="http://www.springframework.org/schema/util"
      xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                          http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-beans.xsd"></beans>
      ```

   2. 通过util 提取list map set集合类型属性注入

      ```xml
      	<!--1 提取list集合类型属性注入-->
          <util:list id="extractList">
              <value>三国</value>
              <value>水浒</value>
              <value>红楼</value>
          </util:list>
      	<!--2 提取Map集合类型属性注入-->
          <util:map id="extractMap">
              <entry key="key1" value="val1"></entry>
              <entry key="key2" value="val2"></entry>
              <entry key="key3" value="val3"></entry>
          </util:map>
          <!--3 提取set集合类型属性注入-->
          <util:set id="extractSet">
              <value>num1</value>
              <value>num2</value>
              <value>num3</value>
          </util:set>
      ```

   3. 提取list集合类型属性注入使用, 

      1. Extract类对应的属性list集合已经通过util提取出来, id为 extractList
      2. 这里的Extract类对应的对象,在给list集合赋值的时候直接引用了extractList实现了属性的注入

      ```xml
      
      <!--4 提取list map set 集合类型属性注入使用-->
          <bean id="extract" class="com.AtGuiGu.Spring_CollectionType.Step1_Collection.Extract">
              <property name="list" ref="extractList"></property>
              <property name="map" ref="extractMap"></property>
              <property name="set" ref="extractSet"></property>
          </bean>
      ```




#### FactoryBean

1. Spring有两种类型bean, 一种普通bean, 另外一种工厂bean(FactoryBean)
2. 普通bean: 在配置文件中定义bean类型就是返回类型
   1. bean标签内定义了bean的别名以及class="bean对象对应类的包路径, 是一个确定的类"
   2. java中通过getBean方法以及别名获取的就是对应的类对象
3. 工厂bean: 在配置文件中定义bean类型可以和返回类型不一样
   1. 创建类, 让这个类作为工厂bean, 实现接口FactoryBean
   2. 实现接口里面的方法, 在实现的方法里定义返回的bean类型
      1. 返回类型取决于getObject方法的返回值 默认是Object
      2. 返回值类型取决于工厂接口的泛型类型
      3. 这样bean的返回类型就可以变成接口的泛型类型, 也就是getObject方法的返回值类型
   3. 创建工厂bean类实现了FactoryBean接口
   4. 重写接口对应的方法
   5. 根据FactoryBean接口的泛型修改getObject方法的返回值类型
   6. xml文件中创建工厂bean类对应的bean文件
   7. 这样配置文件class="工厂bean类对应的包路径"
      1. 正常应该返回工厂bean类对应的对象,
      2. 但是实现了FactoryBean接口重写了getObject方法以后, 返回类型就可以不一样

#### Bean的作用域

1. 在spring中,设置创建bean实例是单实例还是多实例

   1. 单实例代表类对应只会创建一个对象,获取多次都是指向的同一个对象地址相同
   2. 多实例表示可以创建多个不同的对象

2. 默认情况下, 创建bean是一个单实例对象

   1. 获取两次对应类的对象, 分别打印对象的地址,比较对象是否相同

   2. 相同表名两次获取类的对象都是同一个则表明为单例

      ```java
          /**
           * 测试bean实例默认是单例, 可以修改为多例
           */
          @Test
          public void testCollection2(){
              ApplicationContext context = new ClassPathXmlApplicationContext("bean6Extraction.xml");
              Extract extract1 =  context.getBean("extract", Extract.class);
              Extract extract2 =  context.getBean("extract", Extract.class);
              System.out.println(extract1);
              System.out.println(extract2);
          }
      //结果表名两个对象的地址相同
      com.AtGuiGu.Spring2_CollectionType.Step1_Collection.Extract@16f7c8c1
      com.AtGuiGu.Spring2_CollectionType.Step1_Collection.Extract@16f7c8c1
      ```

3. 如何设置单例/多例

   1. spring配置文件bean标签属性scope用于设置单例/多例

   2. scope属性值

      1. 默认 singleton 表示单例对象

      2. prototype 表示多实例对象

         ```xml
         <bean id="extract" class="com.AtGuiGu.Spring2_CollectionType.Step1_Collection.Extract" scope="prototype">
                 <property name="list" ref="extractList"></property>
                 <property name="map" ref="extractMap"></property>
                 <property name="set" ref="extractSet"></property>
             </bean>
         ```

      3. 修改为scope=prototype 后两个对象的地址不同

         ```
         com.AtGuiGu.Spring2_CollectionType.Step1_Collection.Extract@16f7c8c1
         com.AtGuiGu.Spring2_CollectionType.Step1_Collection.Extract@2f0a87b3
         ```

   3. singleton 和 prototype 区别

      1. singleton: 单实例 ptototype: 多实例

      2. scope=singleton 

         1. 加载配置文件时就会创建单实例对象

            ```java
            ApplicationContext context = new ClassPathXmlApplicationContext("bean6Extraction.xml");
            ```

      3. scope=prototype

         1. 加载配置文件不会创建对象

         2. 调用getBean方法的时候, 创建多实例对象

            ```java
            Extract extract1 =  context.getBean("extract", Extract.class);
            ```

   4. 其他scope的值

      1. request
      2. session



#### Bean生命周期

1. 生命周期

   1. 从对象的创建到对象的销毁过程

2. bean生命周期

   1. 通过类的构造器创建bean实例(无参构造)
   2. 为bean的属性设置值,和对其他bean的引用(调用类中对应属性的set方法为属性赋值)
   3. 调用bean的初始化的方法(需要进行配置初始化的方法)
   4. bean可以使用了(对象获取到了)
   5. 容器关闭时, 调用bean的销毁方法(需要进行配置销毁的方法)

3. 演示bean的声明周期

   1. bean对应的类中的无参构造调用创建bean实例

      ```java
      public Order() {
              System.out.println("Step1: 执行无参构造创建bean实例");
          }
      ```

      

   2. 调用对应属性的set方法为属性赋值

      ```java
      public void setOname(String oname) {
              this.oname = oname;
              System.out.println("Step2: 执行属性对应的set设置属性的值");
          }
      ```

   3. 调用类中创建的初始化方法

      1. 现在类中创建希望成为的初始化方法

         ```java
         //创建执行的初始化方法
             public void initMethod(){
                 System.out.println("Step3: 执行的初始化方法");
             }
         ```

      2. 在配置文件中设置bean标签的init-method属性=对应类中的初始化方法

         ```xml
         <bean id="order" class="com.AtGuiGu.Spring4_LifeSpan.bean.Order" init-method="initMethod" destroy-method="destroyMethod">
                 <property name="oname" value="phone" ></property>
             </bean>
         ```

   4. 获取创建bean的实例

      1. 通过getBean方法

         ```java
         Order order = context.getBean("order", Order.class);
         System.out.println("Step4: 获取创建bean实例对象");
         ```

   5. 调用销毁bean的方法

      1. 类中创建bean的销毁方法

         ```java
            //创建执行的销毁方法
             public void destroyMethod(){
                 System.out.println("Step5: 执行销毁的方法");
             }
         ```

      2. 在创建bean的bean标签内设置属性 destroy-method="类中对应的销毁方法"

      3. 在结束使用bean对象以后调用close()方法

         ```java
         //手动让bean实例销毁
         //因为接口中没有close方法
         //需要使用Application 实现类中的close方法
         //需要将context强转为ApplicationContext的实现类
         ((ClassPathXmlApplicationContext)context).close();
         ```

   6. 最后的结果

      ```
      Step1: 执行无参构造创建bean实例
      Step2: 执行属性对应的set设置属性的值
      Step3: 执行的初始化方法
      Step4: 获取创建bean实例对象
      com.AtGuiGu.Spring4_LifeSpan.bean.Order@6121c9d6
      Step5: 执行销毁的方法
      ```

4. 除了上述5步以外还有其他两个步骤 bean的后置处理器, bean的生命周期有七步

   1. 通过类的构造器创建bean实例(无参构造)

   2. 为bean的属性设置值,和对其他bean的引用(调用类中对应属性的set方法为属性赋值)

   3. **把bean实例传递给bean后置处理器的方法**

      1. ```java
         postProcessBeforeInitialization()
         ```

   4. 调用bean的初始化的方法(需要进行配置初始化的方法)

   5. **把bean实例传递给bean后置处理器的方法**

      1. ```java
         postProcessAfterInitialization()
         ```

   6. bean可以使用了(对象获取到了)

   7. 容器关闭时, 调用bean的销毁方法(需要进行配置销毁的方法)

5. 演示添加后置处理器效果

   1. 创建类, 实现接口 BeanPostProcessor 创建后置处理器

   2. 重写接口中的两个方法

      ```java
      @Override
          public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
              System.out.println("在初始化之前执行的方法");
              return bean;
          }
      
          @Override
          public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
              System.out.println("在初始化之后执行的方法");
              return bean;
          }
      ```

   3. 在配置文件中配置后置处理器

      1. 创建后置处理器类对应的bean实例

         ```xml
         <!-- 配置后置处理器 -->
             <bean id="myBeanPost" class="com.AtGuiGu.Spring4_LifeSpan.bean.MyBeanPost"></bean>
         ```

   4. 这样系统能够识别该bean就是对应的后置处理器

      1. 因为类中实现了BeanPostProcessor接口并且重写了的接口中的两个方法
      2. 同时该后置处理器会对配置文件中所有的bean添加后置处理器处理
         1. 即后置处理器类中的两个方法都会执行

   5. 最后的效果

      ```
      Step1: 执行无参构造创建bean实例
      Step2: 执行属性对应的set设置属性的值
      在初始化之前执行的方法
      Step3: 执行的初始化方法
      在初始化之后执行的方法
      Step4: 获取创建bean实例对象
      com.AtGuiGu.Spring4_LifeSpan.bean.Order@eafc191
      Step5: 执行销毁的方法
      ```



#### xml自动装配

1. 手动装配

   1. 在bean实例中写property标签 设置name value 属性

2. 什么是自动装配

   1. 根据指定装配规则, (属性名称或者属性类型), Spring自动将匹配的属性值进行注入

3. 演示自动装配过程

   1. 根据属性名称自动注入 autowire="byName"

      ```xml
      注入值bean的id值和类型属性名称一样 即Dept类对应的id值=Emp类中Dept属性的属性名称
      <bean id="emp" class="com.AtGuiGu.Spring5_AutoWire.Emp" autowire="byName">
      ```

      

   2. 根据属性类型自动注入 autowire="byType"

      ```xml
      根据属性对应的类型找到对应的bean实例,进行属性的注入
      <bean id="emp" class="com.AtGuiGu.Spring5_AutoWire.Emp" autowire="byType">
      ```

      1. 相同类型的bean如果定义了多个则需要根据byName进行自动装配



#### 外部属性文件

1. 直接配置数据库信息

   1. 配置德鲁伊连接池

   2. 引入德鲁伊连接池依赖jar包

      ```xml
      <!-- 直接配置连接池 -->
          <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
              <property name="url" value="jdbc:mysql://localhost:3306/test?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC&amp;useSSL=false&amp;rewriteBatchedStatements=true" />
              <property name="username" value="root" />
              <property name="password" value="123" />
              <property name="driverClassName" value="com.mysql.jdbc.Driver" />
          </bean>
      ```

      

2. 引入外部属性文件配置数据库连接池

   1. 创建外部属性文件, properties格式文件,写入jdbc所需的属性

      ```properties
      prop.url=jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&rewriteBatchedStatements=true
      prop.username=root
      prop.password=123
      prop.driverClassName=com.mysql.cj.jdbc.Driver
      ```

   2. 把外部properties属性文件引入到spring配置文件中

      1. 引入context名称空间

         ```xml
         xmlns:context="http://www.springframework.org/schema/context"
                xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                    http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd
                                    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
         ```

      2. 在spring配置文件使用标签引入外部属性文件

         ```xml
         <!-- 引入外部属性文件 -->
         <context:property-placeholder location="classpath:jdbc.properties"/>
         ```

      3. 配置数据库连接池

         1. ${}表达式里面放的是properties文件里等号左边的名称, 这样就获取了=右边的值

         ```xml
         <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
                 <property name="url" value="${prop.url}" />
                 <property name="username" value="${prop.username}" />
                 <property name="password" value="${prop.password}" />
                 <property name="driverClassName" value="${prop.driverClassName}" />
         </bean>
         ```



### 基于注解方式

#### 什么是注解

1. 注解是代码特殊标记, 格式: @注解名称(属性名称1=属性值, 属性名称2=属性值...)
2. 使用注解, 注解可以作用在类, 方法, 属性上面
3. 使用注解目的: 简化xml配置

#### Spring 针对Bean管理中创建对象提供的注解

1. @Component
2. @Service
3. @Controller
4. @Repository
5. 上面的四个注解功能相同, 都可以用来创建bean实例

#### 基于注解方式实现对象创建

1. 引入依赖

   1. AOP依赖
   2. spring-aop-5.2.6.RELEASE.jar

2. 开启组件扫描

   1. 在指定包名下扫描文件, 只要类文件上面有注解就会创建对象

   2. 需要在配置文件里引入context名称空间

      ```xml
      <!-- 开启组件扫描
              1.如果扫描多个包, 在base-package="多个包的路径之间用逗号隔开"
              2.扫描多个包的上层目录
          -->
      <context:component-scan base-package="com.AtGuiGu.Spring6_Annotation.dao, com.AtGuiGu.Spring6_Annotation.service"></context:component-scan>
      <context:component-scan base-package="com.AtGuiGu.Spring6_Annotation"></context:component-scan>
      ```

3. 创建类, 在类上添加创建对象的注解

   ```java
   //在注解里面value="属性值" 可以省略不写
   //默认值是类名称, 首字母小写
   //UserService --> userService
   @Component(value="userService") //<bean id="userService" class="...类的包路径">
   //@Service()
   //@Service
   //@Controller
   //@Repository
   ```

   1. 针对创建对象的任意一个注解都可以创建对象
   2. value的值等价于xml文件创建对象bean标签里的id
      1. value="属性值"也可以不写, 默认就是类名首字母小写
   3. 但是我们不需要写class因为扫描到该类的有注解就已经知道包名了

4. 测试, 加载配置文件

   1. 因为配置文件中有context:component标签,spring了解需要通过注解方式创建对象
   2. spring会去到bask-package的值的包名下去扫描所有的类, 
   3. 遇到带有四个注解(@Componeng @Service @Controller @Repository )之一的类就会创建对应的bean实例
   4. 这样我们就可以通过getBean方法获取bean实例对应的类对象



#### 开启组件扫描配置细节

1. 设置扫描哪些内容

   1. 添加use-default-filter="false"表示不使用默认的过滤器即全部扫描
   2. context:include-filter 表示扫描哪些内容
      1. type="annotation" 注解类型
      2. expression="" 哪种注解

   ```xml
   <!-- 示例1 设置扫描哪些内容
           use-default-filter="false" 表示现在不使用默认filter, 自己配置filter
           context:include-filter,  到com.AtGuiGu中扫描注解类型为Controller的类
       -->
       <context:component-scan base-package="com.AtGuiGu" use-default-filters="false">
           <context:include-filter type="annotation"
                                   expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>
   
   ```

2. 设置不扫描哪些内容

   1. context:exclude-filter 表示不扫描哪些内容

   ```xml
   	<!-- 示例2 设置不扫描哪些内容
               下面的配置扫描包内的所有内容
               context:exclude-filter,  设置Controller类型的注解的类不扫描
           -->
       <context:component-scan base-package="com.AtGuiGu">
           <context:exclude-filter type="annotation"
                                   expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>
   ```

   

#### 基于注解方式实现属性的注入

1. @AutoWired

   1. 根据属性类型进行自动装配

   2. 把service 和 dao 对象创建, 在service和dao类添加创建对象注解

   3. 在service类中注入dao对象

      1. 在service中定义注入的dao类属性

      2. 不需要添加set方法

      3. 在属性上面使用注解

         ```java
         @Service
         public class UserService {
         
             //1. 在UserService中定义注入的dao类属性
             //2. 不需要添加set方法
             //3. 在属性上面使用注解
             @Autowired
             private UserDao userDao;
         
             public void add(){
                 System.out.println("Service add...");
                 userDao.add();
             }
         }
         //执行结果
         com.AtGuiGu.Spring6_Annotation.service.UserService@5e4c8041
         Service add...
         dao add...
         ```

         

2. @Qualifier

   1. 根据属性名称进行注入

   2. 这个@Qualifier注解的使用, 和上面@Autowired一起使用

      1. 根据类型进行注入, 使用@Autowired注解, 

      2. 如果被注入的属性类为接口, 并且接口有多个实现类,那么这些实现类的都为接口的类型

      3. 那么@Autowired就没有办法找到哪个实现类来注入因为他们的类型都一样

      4. 所以需要使用Qualifier注解根据名称进行注入, 

      5. 需要在不同的实现类上面的注解里加上别名Repository(value="别名")

      6. 这样Qualifier(value="别名")就可以根据不同实现类注解里的别名来区分进而注入该类的属性

         ```java
         @Service
         public class UserService {
         
             //1. 在UserService中定义注入的dao类属性
             //2. 不需要添加set方法
             //3. 在属性上面使用注解
             @Autowired //根据类型进行注入
             @Qualifier(value="userDaoImpl2")
             private UserDao userDao;
         
             public void add(){
                 System.out.println("Service add...");
                 userDao.add();
             }
         }
         
         @Repository(value="userDaoImpl2")
         public class UserDaoImpl2 implements UserDao{
             @Override
             public void add() {
                 System.out.println("userDaoImpl2 dao add2...");
             }
         }
         
         @Repository(value="userDaoImpl1")
         public class UserDaoImpl implements UserDao{
             @Override
             public void add() {
                 System.out.println("userDaoImpl1 dao add1...");
             }
         }
         ```

         

3. @Resource

   1. 可以根据类型注入 可以根据名称注入

   2. 属性上只写@Resource表示只根据类型注入, 但是注入类型为接口并且有多个实现类, 不能解决

   3. 通过注解里name属性并赋值为不同实现注解里的别名可以即根据类型又根据名称注入

   4. @Resource(name="userDaoImpl1")

      ```java
      //    @Resource//根据类型进行注入
          @Resource(name="userDaoImpl1") //根据名称进行注入
          private UserDao userDao;
      ```

      

4. @Value

   1. 注入普通类型属性

   2. 通过在普通属性上添加@Value(value="abc")注解就可以将注解里value对应的值注入到userName里

      ```java
      @Value(value="abc")
      private String userName;
      ```



#### 完全注解开发

1. 不需要利用xml文件, 之前通过注解方式创建对象和注入属性,但是仍然需要在xml文件配置开启组件扫描

2. 通过完全注解开发将唯一的开启组件扫描的配置文件放到其他地方

   ```xml
   <context:component-scan base-package="com.AtGuiGu.Spring6_Annotation"></context:component-scan>
   ```

3. 步骤

   1. 创建配置类, 替代配置文件

      ```java
      @Configuration//作为配置类, 替代xml配置文件
      //替代<context:component-scan base-package="com.AtGuiGu.Spring6_Annotation"></context:component-scan>
      @ComponentScan(basePackages={"com.AtGuiGu.Spring6_Annotation"})
      public class SpringConfig {
      }
      ```

   2. 可以删除xml配置文件

   3. 测试类需要重新写 

      1. 通过AnnotationConfigApplicationContext(配置类.class)加载配置而不是加载配置文件

      ```java
      //完全注解开发 无需xml文件
          @Test
          public void testCompleteAnnotation(){
              ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
              UserService userService = context.getBean("userService", UserService.class);
              System.out.println(userService);
              userService.add();
          }
      ```

      

# AOP

## AOP 基本概念

1. 什么是AOP
   1. 面向切面编程
      1. 分离业务逻辑,解耦, 提高可重用性, 开发效率
   2. 登录功能示例
      1. ![image-20210503165037501](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210503165037501.png)
      2. 不通过修改源代码的方式,在主干功能里面添加新的功能

## AOP 底层原理

### AOP底层使用动态代理

1. 两种情况的动态代理
   1. 有接口的情况
      1. 使用JDK动态代理
         1. 创建接口实现类的代理对象, 通过代理对象增强需要增加的功能
         2. ![image-20210503170032330](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210503170032330.png)
   2. 没有接口的情况
      1. 使用CGLIB动态代理
         1. 创建子类的代理对象,实现功能的增强
         2. ![image-20210503170259311](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210503170259311.png)



### AOP JDK 动态代理

1. 使用JDK动态代理, 使用Proxy类里面的方法创建代理对象
   1. 调用newProxyInstance方法
      1. ![image-20210503171222182](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210503171222182.png)
      2. 第一个参数:类加载器
      3. 第二个参数: 增强方法所在类, 这个类实现的接口, 支持多个接口
      4. 第三个参数: 实现这个接口 InvocationHandler.  创建代理对象, 写增强的方法
   
2. 编写JDK动态代理方法
   1. 创建接口, 定义方法
      1. UserDao
         1. add()
         2. update()
   2. 创建接口实现类, 实现方法
      1. UserDaoImpl
         1. 重写了add()
         2. 重写了update()
   3. 使用Proxy类创建接口代理对象
      1. 定义一个JDK动态代理类 JDK_proxy

         ```java
         public class JDK_proxy {
             public static void main(String[] args) {
                 //创建接口实现类的代理对象
                 Class[] interfaces = {UserDao.class};
                 //方法一: InvocationHandler 创建匿名内部代理对象
         //        Proxy.newProxyInstance(JDK_proxy.class.getClassLoader(), interfaces, new InvocationHandler() {
         //            @Override
         //            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
         //                return null;
         //            }
         //        });
                 UserDaoImpl userDao = new UserDaoImpl();
                 //方法二: 写一个类, 再在其中写增强的方法
                 //JDK_proxy.class.getClassLoader() 获取当前动态代理类的类加载器
                 //interfaces 包含了需要被代理的类的实现的接口的数组,
                 //new InvocationHandler() 需要增强的功能所实现的接口的实现类的对象,
                 //该方法返回的是UserDao接口实现类的代理对象, 本质上也是实现UserDao接口, 所以需要强转成UserDao
                 UserDao dao = (UserDao)Proxy.newProxyInstance(JDK_proxy.class.getClassLoader(), interfaces, new UserDaoProxy(userDao));
                 int res = dao.add(1,2);
                 System.out.println("res="+res);
         
             }
         }
         ```

         

      2. 其中利用Proxy.newProxyInstance()方法创建接口实现类的动态代理对象

      3. newProxyInstance()需要传入三个参数
         1. 当前类的类加载器
         2. 该代理对象所代理的实现类所实现的接口
         3. InvocationHandler接口的实现类的对象

      4. 创建UserDaoProxy类实现InvocationHandler接口

         ```Java
         //创建代理对象代码
         class UserDaoProxy implements InvocationHandler{
         
             //1 创建的是谁的代理对象, 把谁传递进来
             //有参构造传递
             //在构造器的参数里, Object代表的就是所代理对象的类, 这样更加通用
             private Object obj;
             public UserDaoProxy(Object obj){
                 this.obj = obj;
             }
         
             //增强的逻辑
             @Override
             public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
         
                 //方法之前
                 System.out.println("被增强方法之前执行..."+method.getName()+":传递的参数..."+ Arrays.toString(args));
         
                 //被增强的方法执行
                 //(执行method方法的对象, 方法执行的参数)
                 Object res = method.invoke(this.obj, args);
         
                 //方法之后
                 System.out.println("被增强方法之后执行..."+obj);
                 return res;
             }
         }
         ```

         

         1. 将被代理类的对象传递进来
            1. 通过创建被代理类的对象属性
            2. 通过有参构造中被代理类对象的属性,将被代理类对象传递进来并赋值给当前类的被代理对象属性,实现将被代理类的对象传递进来
         2. 重写接口中invoke(Object proxy, Method method, Object[] args)方法
            1. proxy 被代理对象
            2. method需要被增强的方法
            3. args method方法的参数
         3. 通过参数中method.invoke(被代理对象(通过有参构造传递进来),args ) 执行被增强的原始方法
            1. 该方法的返回值也是当前invoke方法的返回值
         4. 在method之前或之后, 实现方法的增强

      5. 在动态代理类中把InvocationHandler接口的实现类UserDaoProxy的对象传递进来

         1. 需要先new 一个被代理类的对象 userDao
         2. 传递到UserDaoProxy对象参数中, 将被代理类传递进去
         3. 该方法实例化的是被代理类的代理对象, 需要强制转化成被代理类实现的接口, 因为实际上是增强了接口里的方法
         4. 通过该代理类调用对应的方法, 获得增强方法后的结果

### AOP(术语)

1. 连接点
   1. 类中可以被增强的方法, 称为连接点
2. 切入点
   1. 实际被增强的方法, 称为切入点
3. 通知(增强)
   1. 实际增强的逻辑部分, 称为通知
   2. 通知有多种类型(以增强add方法为例)
      1. 前置通知
         1. add()方法之前执行
      2. 后置通知
         1. add()方法之后执行
      3. 环绕通知
         1. add()方法之前之后都执行
      4. 异常通知
         1. add()方法发生异常才执行
      5. 最终通知
         1. 相当于try catch finally 中的finally, 无论add()方法是否异常都最后执行
4. 切面 (是一个动作)
   1. 把通知应用到切入点的过程称为切面

### AOP操作(准备)

1. Spring框架一般都是基于AspectJ实现AOP操作
   1. AspectJ
      1. AspectJ不是spring的组成部分, 是独立的AOP框架, 一般把AspectJ 和 Spring框架一起使用,进行AOP操作
2. 基于AspectJ 实现AOP操作
   1. 基于xml配置文件实现
   2. 基于注解方式实现(使用)
3. 在项目工程中引入AOP相关依赖
   1. spring-aspects
   2. spring-boot-starter-aop
4. 切入点表达式
   1. 作用
      1. 知道对哪个类里面的哪个方法进行增强
   2. 语法结构:
      1. execution(\[权限修饰符]\[返回类型]\[类全路径]\[方法名称]\[参数列表])
         1. 类的权限修饰符, 
         2. 切入点的返回类型
         3. 切入点类的路径包括方法
         4. 切入点方法名称
         5. 切入点方法参数
      2. 举例1 指定类, 方法
         1. 对com.atguigu.dao.BookDao类里面的add进行增强
         2. execution(* com.atguigu.dao.BookDao.add(..))
            1. *表示任意的权限修饰符
            2. 返回类型可以省略
            3. 方法中 .. 表示方法的参数列表
      3. 举例2 指定类 所有方法
         1. 对com.atguigu.dao.BookDao类里面所有方法进行增强
         2. execution(* com.atguigu.dao.BookDao.*(..))
            1. 其他相同, 方法名称用*代替
      4. 举例3 所有类所有方法
         1. 对com.atguigu.dao包里的所有类, 类里面所有方法进行增强
         2. execution(* com.atguigu.dao.\*.\*(..))

### AOP (AspectJ 注解)

1. 创建类, 在类里面定义方法

   1. User

2. 创建增强类, (写增强逻辑)

   1. 在增强类里面. 创建方法, 让不同方法代表不同的通知类型

3. 进行通知的配置

   1. 在spring配置文件中, 开启注解扫描

      1. 配置文件

         ```xml
         <!--    开启注解扫描-->
             <context:component-scan base-package="com.AtGuiGu.Spring8_AOP_AspectJ.Annotation"></context:component-scan>
         ```

      2. 注解类

         ```java
         @Configuration
         @ComponentScan(basePackages = {"com.AtGuiGu.Spring8_AOP_AspectJ.Annotation"}) //开启组件扫描
         @EnableAspectJAutoProxy(proxyTargetClass = true) // 开启Aspect生成代理对象
         public class ConfigAop {
         }
         ```

         

   2. 使用注解创建User 和UserProxy对象

      1. 在两个类上添加@Component注解

   3. 在增强类上面添加注解 @Aspect

   4. 在spring配置文件中开启生成代理对象

      ```xml
      <!--    开启Aspect生产代理对象-->
          <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
      ```

      1. 开启后, 在对应包类查找所有的类文件, 
      2. 如果类文件用@Aespect注解修饰
      3. 就生成对应的代理对象

4. 配置不同类型的通知

   1. 在增强类中, 在作为通知的方法上面添加通知类型注解, 使用切入点表达式配置
   2. 测试
      1. 获得被增强的类的对象
      2. 调用被增强的方法, 增强的逻辑也会执行
      3. @Before (value="切入点表达式");
         1. before...
            add...
   3. 没有异常的情况
      1. 环绕之前...
         before...
         add...
         环绕之后...
         after...
         after returning...
   4. 有异常的情况
      1. 环绕之前...
         before...
         after...
         after throwing...

   ```java
   //增强的类
   @Component
   @Aspect //表示生产代理对象
   public class UserProxy {
   
       //前置通知
       //@Before 前置通知对应的注解
       //"execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Annotation.User.add(..))" 中为切入点表达式
       @Before(value="execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Annotation.User.add(..))")
       public void before(){
           System.out.println("before...");
       }
   
       //后置通知
       //被增强方法之后执行
       //有异常也执行
       @After(value="execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Annotation.User.add(..))")
       public void after(){
           System.out.println("after...");
       }
   
       //最终通知
       //被增强方法返回值以后执行
       //有异常不执行
       @AfterReturning(value="execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Annotation.User.add(..))")
       public void afterReturning(){
           System.out.println("after returning...");
       }
   
       //异常通知
       //被增强方法存在异常会执行
       @AfterThrowing(value="execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Annotation.User.add(..))")
       public void afterThrowing(){
           System.out.println("after throwing...");
       }
   
       //环绕通知
       //在被增强方法之前之后都执行
       @Around(value="execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Annotation.User.add(..))")
       public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
           System.out.println("环绕之前...");
   
           //被增强的方法执行
           proceedingJoinPoint.proceed();
   
           System.out.println("环绕之后...");
       }
   
   }
   ```

5. 相同切入点的抽取 

   1. 不同的通知注解后面的切入点表达式相同, 可以抽取出来

   2. 在增强类中创建一个切入点抽取的方法

      1. 用@Pointcut注解修饰
      2. 注解内的值为相同切入点的表达式
      3. @Pointcut(value="相同切入点表达式")

   3. ```java
          //相同切入点抽取
          @Pointcut(value="execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Annotation.User.add(..))")
          public void pointDemo(){
      
          }
      
          //前置通知
          //@Before 前置通知对应的注解
          //"execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Annotation.User.add(..))" 中为切入点表达式
          @Before(value="pointDemo()")
          public void before(){
              System.out.println("before...");
          }
      ```

      

6. 有多个增强类对同一个方法进行增强, 设置增强的优先级

   1. 在增强类上面添加注解@Order(数字类型), 数字类型值越小优先级越高, 从0开始

   2. ```java
      @Order(1) //对同一个类的方法增强时的优先级, 越高先执行
      public class PersonProxy{
      }
      ```



### AOP (AspectJ 配置文件)

1. 创建两个类, 增强类和被增强类, 创建方法

2. 在spring配置文件中创建两个类对象

   ```xml
   <!--    创建对象-->
       <bean id="book" class="com.AtGuiGu.Spring8_AOP_AspectJ_Xml.Book"></bean>
       <bean id="bookproxy" class="com.AtGuiGu.Spring8_AOP_AspectJ_Xml.BookProxy"></bean>
   ```

   

3. 在spring配置文件中配置切入点

   ```xml
   <!--    配置aop增强-->
       <aop:config>
       <!--
           pointcut: 切入点
           id: 切入点别名 任意
           expression: 切入点表达式
       -->
           <aop:pointcut id="point" expression="execution(* com.AtGuiGu.Spring8_AOP_AspectJ.Xml.Book.buy(..))"/>
   
           <!--配置切面-->
           <aop:aspect ref="bookproxy">
               <!--增强作用在具体的方法上
                   aop:before 表示增强为前置通知
                   method 增强的方法(5个类型的通知中的一个) 在增强类中声明的方法名字
                   pointcut-ref 切入点, 即增强的方法所应有的切入点的别名, 之前已经配置过了
               表示把before方法作用在切入点point(Book类的buy方法), 并且通知类型为前置通知
               -->
               <aop:before method="before" pointcut-ref="point"/>
               <!--最终通知-->
               <aop:after method="after" pointcut-ref="point"/>
               <!--后置通知-->
               <aop:after-returning method="afterReturning" pointcut-ref="point"/>
               <!--异常通知-->
               <aop:after-throwing method="afterThrowing" pointcut-ref="point"/>
               <!--环绕通知-->
               <aop:around method="around" pointcut-ref="point"/>
           </aop:aspect>
   
       </aop:config>
   
   测试结果:
   before...
   环绕之前...
   buy...
   环绕之后...
   afterReturning...
   after...
   ```





# JDBC Template

## 概念和准备

### 什么是JdbcTemplate

1. Spring 框架对JDBC进行封装, 使用JdbcTemplate 方便实现对数据库的操作

### 准备工作

1. 引入依赖

   1. ```
      mysql-connector-java
      druid
      spring-jdbc //数据库连接
      spring-tx //事务相关
      spring-orm
      ```

   2. xml文件中配置数据库连接池

      1. ```xml
         <!-- 直接配置连接池 -->
             <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
                 <property name="url" value="jdbc:mysql://localhost:3306/user_db?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC&amp;useSSL=false&amp;rewriteBatchedStatements=true" />
                 <property name="username" value="root" />
                 <property name="password" value="123" />
                 <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
             </bean>
         ```

   3. 配置JdbcTemplate对象, 注入DataSource

      1. ```xml
         <!--JdbcTemplate对象-->
             <bean id="jdbdTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
                 <!--注入DataSource
                     JdbcTemplate有参构造传入DataSource对象, 有参构造中调用了父类中的setDataSource()方法
                     通过JdbcTemplate父类JdbcAccessor中的setDataSource()方法注入dataSource
                 -->
                 <property name="dataSource" ref="dataSource"></property>
             </bean>
         ```

   4. 创建service类, dao类, 在dao注入jdbcTemplate对象

      1. 配置文件中开启组件扫描

         1. 配置xml

            1. ```xml
               <!--开启组件扫描-->
                   <context:component-scan base-package="com.AtGuiGu.Spring9_JdbcTemplate"></context:component-scan>
               ```

         2. 配置类

            1. ```java
               @Configuration
               @ComponentScan(basePackages = {"com.AtGuiGu.Spring9_JdbcTemplate"})
               public class JdbcConfig {
               }
               ```

      2. Service

         1. ```java
            @Service
            public class BookService {
                //注入dao
                @Autowired
                private BookDao bookdao;
            }
            ```

      3. Dao

         1. ```java
            @Repository
            public class BookDaoImpl implements BookDao{
                //注入jdbcTemplate
                @Autowired
                private JdbcTemplate jdbcTemplate;
            }
            ```

            



## JdbcTemplate操作数据库

### 添加

1. 对应数据库创建实体类 

   1. 创建对应于数据库列的属性

   2. ```java
      private String userId;
      private String username;
      private String ustatus;
      ```

2. 编写service和dao

   1. 在dao进行数据库添加操作

   2. 调用JdbcTemplate对象里面update方法实现添加操作

      1. update(String sql, Object...args)

         1. sql: sql语句
         2. args: 可变参数, 设置sql语句值

      2. ```java
         @Repository
         public class BookDaoImpl implements BookDao{
             //注入jdbcTemplate
             @Autowired
             private JdbcTemplate jdbcTemplate;
             //添加的方法
             @Override
             public void add(Book book) {
                 //1 创建SQL语句
                 String sql = "insert into t_book values(?,?,?);";
                 //2 调用方法实现
                 Object[] args = {book.getUserId(), book.getUsername(), book.getUstatus()};
                 int update = jdbcTemplate.update(sql, args);
                 System.out.println(update);
             }
         }
         ```

      3. 

3. 测试

   1. ```java
      	@Test
          public void testJdbcTemplateAdd(){
              ApplicationContext context = new ClassPathXmlApplicationContext("bean14_Jdbc_Template.xml");
              BookService bookService = context.getBean("bookService", BookService.class);
      
              Book book = new Book();
              book.setUserId("1");
              book.setUsername("java");
              book.setUstatus("a");
              bookService.addBook(book);
          }
      ```

      ![image-20210505150421739](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210505150421739.png)

### 修改和删除

1. 修改

   1. ```java
      //修改的方法
          @Override
          public void updateBook(Book book) {
              //1 sql
              String sql = "update t_book set username=?,ustatus=?where user_id=?;";
              Object[] args = {book.getUsername(), book.getUstatus(), book.getUserId()};
              int update = jdbcTemplate.update(sql, args);
              System.out.println(update);
          }
      ```

2. 删除

   1. ```java
      //删除的方法
          @Override
          public void delete(String id) {
              //1 sql
              String sql = "delete from t_book where user_id=?;";
              int update = jdbcTemplate.update(sql, id);
              System.out.println(update);
      
          }
      ```



### 查询

#### 查询返回某个值

1. 查询表里有多少条记录, 返回的是某个值

2. 使用jdbcTemplate 实现查询返回某个值的方法

   1. ![image-20210505221638344](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210505221638344.png)

      1. 第一个参数 sql语句
      2. 第二个参数 返回类型的class

   2. ```java
      //查询记录数
      @Override
      public int selectCount() {
          String sql = "select COUNT(*) from t_book;";
          Integer count = jdbcTemplate.queryForObject(sql, Integer.class);
          return count;
      }
      ```

      

#### 查询返回对象

1. 场景: 查询图书详情

2. JdbcTemplate 实现查询返回对象

   1. ![image-20210505222412977](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210505222412977.png)

      1. 第一个参数: sql语句
      2. 第二个参数: RowMapper, 是接口, 返回不同类型的数据, 使用这个接口里面实现类完成数据封装
      3. 第三个参数: sql语句中的占位符对应的参数

   2. ```java
      //查询返回对象
      @Override
      public Book findBookInfo(String id) {
          String sql = "select * from t_book where user_id=?;";
          //调用方法
          Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), id);
          return book;
      }
      ```





#### 查询返回集合

1. 场景: 查询图书列表分页

2. 调用jdbcTemplate 实现查询返回集合分页

   1. ![image-20210505223449402](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210505223449402.png)

      1. 第一个参数: sql语句
      2. 第二个参数: RowMapper, 是接口, 返回不同类型的数据, 使用这个接口里面实现类完成数据封装
      3. 第三个参数: sql语句中的占位符对应的参数

   2. ```java
      //查询返回对象集合
      @Override
      public List<Book> findAll() {
          String sql = "select * from t_book;";
          List<Book> query = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class));
          return query;
      }
      ```



### 批量操作

1. 批量操作: 操作表里面的多条记录

#### 批量添加

1. ![image-20210505223942560](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210505223942560.png)

   1. 第一个参数: sql语句
   2. 第二个参数: 
      1. List集合 添加多条记录的数据, 
      2. Object[]数组每个元素对应数据每条记录的对应列数据

2. ```java
   //批量添加
   @Override
   public void batchAddBook(List<Object[]> batchArgs) {
       String sql = "insert into t_book values(?,?,?)";
       //遍历集合batchArgs里面的每个Object[]数组
       //将每个数组执行一遍sql里面对应的添加操作
       int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
       System.out.println(Arrays.toString(ints));
   }
   ```

3. ```java
   //批量添加
           List<Object[]> batchArgs = new ArrayList<>();
           Object[] o1 = {"3", "java3", "c"};
           Object[] o2 = {"4", "C++", "d"};
           Object[] o3 = {"5", "javaScript", "e"};
           batchArgs.add(o1);
           batchArgs.add(o2);
           batchArgs.add(o3);
           bookService.batchAdd(batchArgs);
   ```

   

4. ![image-20210505225202675](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210505225202675.png)



#### 批量修改

1. 和批量添加方法一致, 区别在于sql和参数不同

2. ```java
   //批量修改
   @Override
   public void batchUpdateBook(List<Object[]> batchArgs) {
       String sql = "update t_book set username=?, ustatus=?, where user_id=?;";
       int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
       System.out.println(Arrays.toString(ints));
   }
   ```

3. ```java
   //批量修改
   List<Object[]> batchArgs = new ArrayList<>();
   //需要修改参数顺序 按照sql的顺序来
   Object[] o1 = {"java33333", "c", "3"};
   Object[] o2 = {"C++4444", "d", "4"};
   Object[] o3 = {"javaScript5555", "e", "5"};
   batchArgs.add(o1);
   batchArgs.add(o2);
   batchArgs.add(o3);
   bookService.batchAdd(batchArgs);
   ```

4. ![image-20210505230211394](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210505230211394.png)



#### 批量删除

1. ```java
   //批量删除
   @Override
   public void batchDeletBook(List<Object[]> batchArgs) {
       String sql = "delete from t_book where user_id=?";
       int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
       System.out.println(Arrays.toString(ints));
   }
   ```

2. ```java
   //批量删除
   List<Object[]> batchArgs = new ArrayList<>();
   //需要修改参数顺序 按照sql的顺序来
   Object[] o1 = {"3"};
   Object[] o2 = {"4"};
   Object[] o3 = {"5"};
   batchArgs.add(o1);
   batchArgs.add(o2);
   batchArgs.add(o3);
   bookService.batchDelete(batchArgs);
   ```

3. ![image-20210505230645411](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210505230645411.png)





# 事务操作

## 事务概念

### 什么是事务

1. 事务是数据库操作的最基本单元, 逻辑上一组操作, 要么都成功, 如果有一个失败, 所有操作都失败
2. 典型场景, 银行转账

### 事务的四个特性 (ACID)

1. 原子性 
2. 一致性
3. 隔离性
4. 持久性



## 搭建事务操作环境



![image-20210506091249201](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506091249201.png)

1. 创建数据库表, 添加记录

   1. **![image-20210506091548758](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506091548758.png)**

2. 创建service, 搭建dao, 完成对象创建和注入关系

   1. service注入dao, 在dao注入jdbcTemplate,  在JdbcTemplate 注入 DataSource

      1. ```java
         @Service
         public class UserService {
             //注入Dao
             @Autowired
             private UserDao userDao;
         }
         
         ```

      2. ```java
         
         @Repository
         public class UserDaoImpl implements UserDao{
             //注入jdbcTemplate
             private JdbcTemplate jdbcTemplate;
         }
         ```

3. 在Dao创建两个方法, 多钱和少钱的方法, 在service 创建一个转账方法调用Dao中的两个方法

   1. ```java
      //lucy --> 100 mary
      //多钱
      @Override
      public void addMoney() {
          String sql = "update t_account set money=money-? where username=?;";
          jdbcTemplate.update(sql, 100,"lucy");
      }
      
      //少钱
      @Override
      public void reduceMoney() {
          String sql = "update t_account set money=money+? where username=?;";
          jdbcTemplate.update(sql, 100, "mary");
      
      }
      ```

   2. ```java
      //转账的方法
      public void transfer(){
          //lucy 少 100
          userDao.reduceMoney();
          
          //mary 多 100
          userDao.addMoney();
      }
      ```

4. 如果上述代码之间存在异常产生, 会造成数据的不一致

   1. ```java
      //转账的方法
          public void transfer(){
              //lucy 少 100
              userDao.reduceMoney();
      
              //模拟异常
              int i = 10 / 0;
      
              //mary 多 100
              userDao.addMoney();
          }
      ```

      

   2. 如何解决?

      1. 使用事务进行解决

   3. 事务操作过程

      1. ```java
         //转账的方法
         public void transfer(){
             try{
                 //第一步  开启事务
         
                 //第二步 进行业务操作
                 //lucy 少 100
                 userDao.reduceMoney();
         
                 //模拟异常
                 int i = 10 / 0;
         
                 //mary 多 100
                 userDao.addMoney();
                 
                 //第三步 没有发生异常, 提交事务
             }catch(Exception e){
                 //第四步  出现异常, 事务回滚
             }
             
         }
         ```





## Spring 事务管理介绍

1. 事务添加到JavaEE三层结构里面Service 层, 业务逻辑层
2. 在Spring进行事务管理操作
   1. 有两种方式
      1. 编程式事务管理
         1. 对应事务开启, 操作,提交等 都用代码的方式写出来
      2. 声明式事务管理(使用)
         1. 通过配置方式
3. 声明式事务管理
   1. 基于注解方式(使用)
   2. 基于xml配置文件方式
4. 在spring进行声明式事务管理, 底层使用AOP原理
5. spring事务管理API
   1. 提供接口, 代表事务管理器, 这个接口针对不同框架提供不同的实现类
   2. ![image-20210506102207335](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506102207335.png)



## 注解声明式事务管理

1. 在spring: 配置文件配置事务管理器

   1. ```xml
      <!--创建事务管理器-->
      <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <!--注入数据源-->
          <property name="dataSource" ref="dataSource"></property>
      </bean>
      ```

2. 在spring配置文件开启事务注解

   1. 在spring配置文件引入名称空间tx

      1. ```xml
         <beans xmlns="http://www.springframework.org/schema/beans"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xmlns:context="http://www.springframework.org/schema/context"
                xmlns:aop="http://www.springframework.org/schema/aop"
                xmlns:tx="http://www.springframework.org/schema/tx"
                xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                     http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                                     http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                                     http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
         ```

         

   2. 开启事务注解

      1. ```xml
         <!--开启事务注解-->
             <tx:annotation-driven transaction-manager="dataSourceTransactionManager"></tx:annotation-driven>
         ```

         

   3. 在service类上面(或者service类里面方法上面)添加事务注解

      1. @Transactional既可以添加到类和方法上面

      2. 如果添加到类上面, 这个类里面所有的方法都添加事务

      3. 如果添加到方法上面,为这个方法添加事务

      4. ```java
         @Service
         @Transactional
         public class UserService {
         ```

         



## 声明式事务管理参数配置

1. 在service类上面添加@Transactional 在这个注解里面可以配置事务相关参数

   1. ![image-20210506103849114](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506103849114.png)

2. propagation: 事务传播行为

   1. 多事务方法之间进行调用, 这个过程中事务是如何进行管理的

   2. ![image-20210506104756059](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506104756059.png)

   3. ![image-20210506104818066](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506104818066.png)

   4. ```java
      @Service
      @Transactional(propagation= Propagation.REQUIRED)
      public class UserService {
      ```

3. isolation: 事务隔离级别

   1. 事务有特性成为隔离性, 多事务操作之间不产生影响, 不考虑隔离性产生很多问题

   2. 三个问题: 脏读, 不可重复读, 幻读

      1. 脏读: 一个未提交的事务, 读取到另一个未提交事务的数据
         1. ![image-20210506110412740](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506110412740.png)
      2. 不可重复读: 一个未提交的事务读取到了另一个提交事务修改的数据
         1. ![image-20210506110707391](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506110707391.png)
      3. 幻读: 一个未提交事务读取到另一个提交事务添加的数据

   3. 解决: 通过设置事务隔离级别, 解决读问题

      1. ![image-20210506110912083](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506110912083.png)

      2. ```java
         @Service
         //              传播行为                           隔离级别
         @Transactional(propagation= Propagation.REQUIRED, isolation= Isolation.REPEATABLE_READ)
         public class UserService {
         ```

         

4. timeout: 超时时间

   1. 事务需要在一定时间内提交, 如果不提交进行回滚
   2. 默认值是-1(不超时), 可以设置时间以秒单位进行计算

5. readOnly: 是否只读

   1. 读: 查询操作, 写:添加修改删除操作
   2. readOnly默认值false, 表示可以查询以及增删改
   3. 设置readOnly值时true, 设置成true之后, 只能查询

6. rollbackFor: 回滚

   1. 设置出现哪些异常进行事务回滚

7. noRollbackFor; 不回滚

   1. 设置出现哪些异常不尽兴事务回滚



## xml方式声明式事务管理

1. 在spring配置文件进行配置

   1. 配置事务管理器

   2. 配置通知

   3. 配置切入点和切面

   4. ```xml
      	<!--1. 创建事务管理器-->
          <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
              <!--注入数据源-->
              <property name="dataSource" ref="dataSource"></property>
          </bean>
      
          <!--2. 配置通知-->
          <tx:advice id="txadvice">
              <!--配置事务参数-->
              <tx:attributes>
                  <!--指定哪种规则的方法上面添加事务-->
                  <tx:method name="accountMoney" propagation="REQUIRED" isolation="REPEATABLE_READ"/>
                  <!--方法名以account开头的添加事务-->
      <!--            <tx:method name="account*"/>-->
              </tx:attributes>
          </tx:advice>
      
          <!--3. 配置切入点和切面 -->
          <aop:config>
              <!--配置切入点-->
              <aop:pointcut id="pt" expression="execution(* com.AtGuiGu.Spring10_Transaction.service.UserService.*(..))"/>
              <!--配置切面-->
              <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
          </aop:config>
      ```



## 完全注解声明式事务管理

1. 创建配置类, 使用配置类替代xml配置文件

2. ```java
   @Configuration //配置类
   @ComponentScan(basePackages = "com.AtGuiGu.Spring10_Transaction") //组件扫描
   @EnableTransactionManagement //开启事务
   public class TransactionConfig {
   
       //创建数组库连接池
       @Bean
       public DruidDataSource getDruidDataSource(){
           DruidDataSource dataSource = new DruidDataSource();
           dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
           dataSource.setUrl("jdbc:mysql://localhost:3306/user_db?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&rewriteBatchedStatements=true");
           dataSource.setUsername("root");
           dataSource.setPassword("123");
           return dataSource;
       }
   
       //创建JdbcTemplate对象
       @Bean
       public JdbcTemplate getJdbcTemplate(DataSource dataSource){
           //dao IOC容器中根据类型找到DataSource
           JdbcTemplate jdbcTemplate = new JdbcTemplate();
           //注入dataSource
           jdbcTemplate.setDataSource(dataSource);
           return jdbcTemplate;
       }
   
       //创建事务管理器
       @Bean
       public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource){
           DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
           transactionManager.setDataSource(dataSource);
           return transactionManager;
       }
   }
   ```

   



# Spring5 框架新功能

## 整个Spring5框架的代码基于java8, 运行时兼容JDK9. 许多不建议使用的类和方法在代码库中删除

## Spring5.0框架自带了通用的日志封装

1. Spring5已经移除了Log4jConfigListener, 官方建议使用Log4j2

2. Spring5 框架整合Log4j2

   1. 引入jar包依赖

      1. ```
         log4j-core
         log4j-api
         log4j-slf4j-impl
         2.12.1
         ```

   2. 创建 log4j2.xml配置文件

      1. ```xml
         <?xml version="1.0" encoding="UTF-8"?>
         <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
         <!--Configuration后面的status用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，可以看到log4j2内部各种详细输出-->
         <configuration status="INFO">
             <!--先定义所有的appender-->
             <appenders>
                 <!--输出日志信息到控制台-->
                 <console name="Console" target="SYSTEM_OUT">
                     <!--控制日志输出的格式-->
                     <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
                 </console>
             </appenders>
             <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
             <!--root：用于指定项目的根日志，如果没有单独指定Logger，则会使用root作为默认的日志输出-->
             <loggers>
                 <root level="info">
                     <appender-ref ref="Console"/>
                 </root>
             </loggers>
         </configuration>
         ```

         

## Spring5框架核心容器支持@Nullable注解

1. @Nullable注解可以使用在方法, 属性, 参数上面, 表示方法返回,属性值,参数值可以为空
2. 方法返回值可以为空
   1. ![image-20210506142047629](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506142047629.png)
3. 方法参数可以为空
   1. ![image-20210506142147934](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506142147934.png)
4. 属性值可以为空
   1. ![image-20210506142230210](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506142230210.png)

## Spring5核心容器支持函数式风格GenericApplicationContext

1. ```java
       //函数式风格创建对象, 交给Spring管理
       @Test
       public void testGenericApplicationContext(){
           //1. 创建GenericApplicationContext对象
           GenericApplicationContext context = new GenericApplicationContext();
           //2. 调用context的方法对象注册
           context.refresh();
           context.registerBean("user1",User.class, ()->new User());
           //3. 获取在spring注册的对象
   //        User user = (User)context.getBean("com.AtGuiGu.Spring10_Transaction.test.User");
           User user1 = (User)context.getBean("user1");
           System.out.println(user1);
       }
   ```

   



## Spring5支持整合Junit5

### 整合Junit4

1. 引入Spring相关针对测试的依赖

2. 创建测试类, 通过注解方式完成

3. ```java
   @RunWith(SpringJUnit4ClassRunner.class) //单元测试框架
   @ContextConfiguration("classpath:bean15_Transaction.xml") //加载配置文件
   public class JTest4 {
   
       @Autowired
       private UserService userService;
   
       @Test
       public void test1(){
           userService.transfer();
       }
   
   }
   ```

4. Junit4依赖

### 整合Junit5

1. 引入Junit5依赖

2. 创建测试类, 使用注解完成

   1. ```java
      //@ExtendWith(SpringExtension.class)
      //@ContextConfiguration("classpath:bean15_Transaction.xml")
      
      @SpringJUnitConfig(locations="classpath:bean15_Transaction.xml")
      public class JTest5 {
          @Autowired
          @Qualifier()
          private UserService userService;
      
          @Test
          public void test1(){
              userService.transfer();
          }
      }
      ```



## SpringWebFlux

### 介绍

1. ![image-20210506151614015](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210506151614015.png)
2. 前置知识
   1. SpringMVC
   2. SpringBoot
   3. Maven
   4. Java8 新特性
3. 是Spring5添加新的模块, 用于Web开发, 功能和SpringMVC类似, WebFlux使用当前一种比较流行的响应式编程出现的框架
4. 传统web框架,  SpringMVC 这些基于Servlet同期, Webflux是一种异步非阻塞的框架, 在Servlet3.1以后才支持, 核心是基于Reactor的相关API实现的
5. 什么是异步非阻塞
   1. 异步同步
      1. 针对调用者, 调用者发送请求, 如果等着对方回应之后才做其他事情就是同步
      2. 发送请求以后不等着对方回应就去做其他事情就是异步
   2. 非阻塞和阻塞
      1. 针对被调用者, 被调用者收到请求以后, 做完请求任务之后才给出反馈就是阻塞
      2. 收到请求之后马上给出反馈然后再去做事情就是非阻塞
   3. 上面都是针对对象不一样
6. Webflux特点:
   1. 异步非阻塞
      1. 有限资源下, 提高系统吞吐量和伸缩性, 以Reactor为基础实现响应式编程
   2. 函数式编程
      1. Spring5框架基于Java8, Webflux使用Java8函数式编程的方式实现路由请求
7. 比较SpringMVC
   1. 两个框架都可以使用注解方式, 都运行在Tomcat等容器中
   2. SpringMVC采用命令式编程, Webflux采用异步响应式编程
   3. ![image-20210522153433988](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522153433988.png)

### 响应式编程

1. 什么是响应式编程

   1. 响应式编程是一种面向数据流和变化传播的编程范式。这意味着可以在编程语言中很方便地表达静态或动态的数据流，而相关的计算模型会自动将变化的值通过数据流进行传播。
   2. 电子表格程序就是响应式编程的一个例子。单元格可以包含字面值或类似"=B1+C1"的公式，而包含公式的单元格的值会依据其他单元格的值的变化而变化。

2. Java8及其之前版本

   1. 提供的观察者模式的两个类(Observer Observable)

   2. ```java
      public class ObserveDemo  extends Observable {
      
          public static void main(String[] args) {
              ObserveDemo observer = new ObserveDemo();
              //添加观察者
              observer.addObserver((o,arg)->{
                  System.out.println("发生变化");
              });
              observer.addObserver((o,arg)->{
                  System.out.println("手动被观察者通知, 准备改变");
              });
              observer.setChanged();//数据变化
              observer.notifyObservers();//通知
          }
      }
      ```



### 响应式编程(Reactor实现)

#### 基本介绍

1. 响应式编程操作中, Reactor是满足Reactive规范框架
2. Reactor有两个核心类, Mono 和 Flux, 这两个类都实现接口Publisher, 提供丰富操作符. 
   1. Flux对象实现发布者, 返回N个元素,  
   2. Mono实现发布者, 返回0或者1个元素
3. Flux和Mono都是数据流的发布者, 使用Flux和Mono都可以发出三种数据的信号
   1. 元素值
   2. 错误信号(终止信号)
   3. 完成信号(终止信号)
   4. 终止信号用于告诉订阅者数据流结束了, 错误信号终止数据流同时把错误信息传递给订阅者
   5. <img src="C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522161424923.png" alt="image-20210522161424923" style="zoom:50%;" />

#### 代码演示Flux和Mono

1. 引入依赖

   1. ```xml
      <dependency>
                  <groupId>io.projectreactor</groupId>
                  <artifactId>reactor-core</artifactId>
                  <version>3.1.5.RELEASE</version>
      </dependency>
      ```

2. 代码(不同方式实现信号)

   1. ```java
      public class TestReactor {
          public static void main(String[] args) {
              //just 方法之间申明
              Flux.just(1,2,3,4);
              Mono.just(1);
      
              //其他的方法
              Integer[] array = {1,2,3,4};
              Flux.fromArray(array);
      
              List<Integer> list = Arrays.asList(array);
              Flux.fromIterable(list);
      
              Stream<Integer> stream = list.stream();
              Flux.fromStream(stream);
          }
      }
      ```

1. 三种信号的特点

   1. 错误信号和完成信号都是终止信号, 但是不能共存
   2. 如果没有发送任何元素值,而是直接发送错误或者完成信号, 表示是空数据流
   3. 如果没有错误信号, 没有完成信号, 表示是无限数据流

2. 调用just或者其他方法只是声明数据流. 数据流并没有发出, 只有进行订阅之后才会触发数据流, 不订阅什么都不会发生

   1. ```
      //just 方法之间申明数据流  并且订阅
      Flux.just(1,2,3,4).subscribe(System.out::println);
      Mono.just(1).subscribe(System.out::println);
      ```

#### 操作符

1. 对数据流进行一道道操作, 称为操作符, 比如工厂流水线
2. map
   1. 元素映射为新元素
   2. <img src="C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522163118949.png" alt="image-20210522163118949" style="zoom:50%;" />
3. flatmap
   1. 元素映射为流
   2. 把每个元素转换成流, 把转换之后多个流变成大的流
   3. <img src="C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522163310195.png" alt="image-20210522163310195" style="zoom:50%;" />

### Webflux执行流程和核心API

1. SpringWebflux基于Reactor, 默认使用容器是Netty,Netty是高性能的NIO框架, 异步非阻塞框架
2. Netty
   1. BIO(Blocking I/O)
      1. <img src="C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522202504380.png" alt="image-20210522202504380" style="zoom: 50%;" />
   2. NIO(None Blocking I/O)
      1. <img src="C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522202640676.png" alt="image-20210522202640676" style="zoom:50%;" />
3. SpringWebflux执行的过程和SpringMVC相似的
   1. SpringWebflux核心控制器DispatchHandler, 实现接口WebHandler
   2. 接口WebHandler有一个方法
      1. ![image-20210522203208627](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522203208627.png)
      2. ![image-20210522203504807](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522203504807.png)
      3. ![image-20210522204024101](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522204024101.png)
   3. SpringWebflux里面DispatcherHandler, 负责请求的处理
      1. HanddlerMapping: 请求查询到处理的方法
      2. HandlerAdapter: 真正负责请求的处理
      3. HandlerResultHandler: 响应结果处理
   4. SpringWebflux实现函数式编程, 两个接口,: 
      1. RouterFunction(路由处理)
      2. HandlerFunction(处理函数)

### SpringWebflux(基于注解编程模型)

1. SpringWebflux实现方式有两种: 注解编程模式和函数式编程模式
2. 使用注解编程模式方式, 和之前SpringMVC使用相似的, 只需要把相关依赖配置到项目中, SpringBoot自动配置相关运行容器, 默认情况下使用Netty服务器

#### 创建SpringBoot工程, 引入webflux依赖

1. ![image-20210522204552032](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522204552032.png)

#### 配置启动的端口号

1. ![image-20210522204725635](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522204725635.png)

#### 创建包和相关类

1. ![image-20210522204851087](C:\Users\Chaoq\AppData\Roaming\Typora\typora-user-images\image-20210522204851087.png)

#### 创建接口, 定义操作方法

1. ```java
   //用户操作接口
   public interface UserService {
       //根据id查询用户
       Mono<User> getUserById(int id);
   
       //查询所有用户
       Flux<User> getAllUser();
   
       //添加用户
       Mono<Void> saveUserInfo(Mono<User> user);
   }
   ```

2. ```java
   public class UserServiceImpl implements UserService {
   
       //创建map集合存储数据
       private final Map<Integer, User> users = new HashMap<>();
   
   
       public UserServiceImpl() {
           this.users.put(1, new User("lucy", "man", 20));
           this.users.put(2, new User("lucy2", "man", 21));
           this.users.put(3, new User("lucy3", "man", 22));
       }
   
       //根据id查询
       @Override
       public Mono<User> getUserById(int id) {
           return Mono.justOrEmpty(this.users.get(id));
       }
       //查询所有
       @Override
       public Flux<User> getAllUser() {
           return Flux.fromIterable(users.values());
       }
   
       //添加用户
       @Override
       public Mono<Void> saveUserInfo(Mono<User> userMono) {
           userMono.doOnNext(person->{
               //向map集合放值
               int id = users.size()+1;
               users.put(id, person);
           }).thenEmpty(Mono.empty());
       }
   }
   ```

#### 创建controller

1. ```java
   @RestController
   public class UserController {
   
       //注入service
       @Autowired
       private UserService userService;
   
       //id查询
       @GetMapping("/user/{id}")
       public Mono<User> getUserId(@PathVariable int id){
           return userService.getUserById(id);
       }
   
       //查询所有
       @GetMapping("/user")
       public Flux<User> getUsers(){
           return userService.getAllUser();
       }
   
       //添加
       @PostMapping("/saveuser")
       public Mono<Void> saveUser(@RequestBody User user){
           Mono<User> userMono = Mono.just(user);
           return userService.saveUserInfo(userMono);
       }
   
   }
   ```

#### 说明

1. SpringMVC方式实现, 同步阻塞的方式, 基于SpringMVC+Servlet+Tomcat
2. SpringWebflux方式实现, 异步非阻塞的方式, 基于SpringWebflux+Reactor+Netty



### SpringWebflux(基于函数式编程模型)

1. 在使用函数式编程模型操作的时候, 需要自己初始化服务器
2. 基于函数式编程模型的时候, 有两个核心接口:RouterFunction(实现路由功能, 请求转发给对应的handler) 和HandlerFunction(处理请求生成响应的函数, 核心人物定义两个函数式接口的实现和启动需要的服务器)
3. SpringWebflux请求和响应不再是ServletRequest和ServletResponse, 而是ServerRequest和ServerResponse

#### 复制注解模型工程

#### 创建Handler(具体实现方法)

```java
public class UserHandler {
    private final UserService userService;

    public UserHandler(UserService userService){
        this.userService = userService;
    }

    //根据id查询
    public Mono<ServerResponse> getUserById(ServerRequest request){
        //获取id值
        int userId = Integer.valueOf(request.pathVariable("id"));
        //控制处理
        Mono<ServerResponse> notFound = ServerResponse.notFound().build();
        //调用service方法得到数据
        Mono<User> userMono = this.userService.getUserById(userId);
        //把userMono进行转换返回
        //使用Reactor操作符flatMap
        return userMono
                .flatMap(person->ServerResponse.ok()
                        .contentType(MediaType.APPLICATION_JSON)
                        .body(fromObject(person)))
                .switchIfEmpty(notFound);
    }

    //查询所有
    public Mono<ServerResponse> getAllUser(){
        //调用service得到结果
        Flux<User> users = this.userService.getAllUser();
        return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(users, User.class);
    }

    //添加
    public Mono<ServerResponse> saveUser(ServerRequest request){
        //得到User对象
        Mono<User> userMono = request.bodyToMono(User.class);
        return ServerResponse.ok().build(this.userService.saveUserInfo(userMono));
    }
}
```



#### 初始化服务器 写Router

创建路由的方法

```java
public class Server {

    //1.创建Router路由
    public RouterFunction<ServerResponse> routerFunction(){
        //创建handler对象
        UserService userService = new UserServiceImpl();
        UserHandler handler = new UserHandler(userService);
        //设置路由
        return RouterFunctions.route(
                GET("/users/{id}").and(accept(APPLICATION_JSON)),handler::getUserById)
                .andRoute(GET("/users").and(accept(APPLICATION_JSON)), handler::getAllUsers);
    }
}
```

#### 创建服务器完成适配

```java
//2.创建服务器完成适配
    public void createReactorServer(){
        //路由和handler适配
        RouterFunction<ServerResponse> route = routerFunction();
        HttpHandler httpHandler = toHttpHandler(route);
        ReactorHttpHandlerAdapter adapter = new ReactorHttpHandlerAdapter(httpHandler);

        //创建服务器
        HttpServer httpServer = HttpServer.create();
        httpServer.handle(adapter).bindNow();
    }
```

最终调用

```java
public static void main(String[] args) throws Exception{
        Server server = new Server();
        server.createReactorServer();
        System.out.println("enter to exit");
        System.in.read();
    }
```



### 使用WebClient调用

```java
public class Client {

    public static void main(String[] args) {
        //调用服务器地址
        WebClient webClient = WebClient.create("http://127.0.0.1:59115");
        //根据id查询
        String id = "1";
        User userResult = webClient.get().uri("/users/{id}", id).accept(MediaType.APPLICATION_JSON).retrieve().bodyToMono(User.class).block();
        System.out.println(userResult.getName());

        //查询所有
        Flux<User> results = webClient.get().uri("/users").accept(MediaType.APPLICATION_JSON).retrieve().bodyToFlux(User.class);
        results.map(stu->stu.getName()).buffer().doOnNext(System.out::println).blockFirst();
    }
}
```



# 课程总结

## Spring框架概述

### 轻量级开源JavaEE框架, 为了解决企业复杂性, 两个核心组件:IOC和AOP

### Spring5.2.6版本



## IOC

### 底层原理(工厂反射)

### IOC接口(BeanFactory)

### IOC操作Bean管理(基于xml)

### IOC操作Bean管理(基于注解)



## AOP

### AOP底层原理, 动态代理, 有接口(JDK动态代理) 没有接口(CGLIB动态代理)

### 术语:切入点, 增强(通知), 切面

### 基于AspectJ实现AOP操作



## JdbcTemplate

### 使用JdbcTemplate实现数据库CURD

### 使用JdbcTemplate实现数据库批量操作



## 事务管理

### 事务概念

### 重要概念(传播行为隔离级别)

### 基于注解实现声明式事务管理

### 完全注解方式实现声明式事务管理



## Spring5新功能

### 整合日志框架

### @Nullable注解

### 函数式注册对象

### 整合JUnit5单元框架

### SpringWebflux使用

















 