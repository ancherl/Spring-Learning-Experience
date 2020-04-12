# Spring-Learning-Experience

![alt SpringBoot](https://bkimg.cdn.bcebos.com/pic/37d12f2eb9389b503a80d4b38b35e5dde6116ed7?x-bce-process=image/watermark,g_7,image_d2F0ZXIvYmFpa2UxNTA=,xp_5,yp_5)

*Spring基础知识*
1. JDK动态代理和CGLib动态代理的区别
```
一、原理区别：

java动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。

而cglib动态代理是利用asm开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。

1、如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP 
2、如果目标对象实现了接口，可以强制使用CGLIB实现AOP 

3、如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换

如何强制使用CGLIB实现AOP？
 （1）添加CGLIB库，SPRING_HOME/cglib/*.jar
 （2）在spring配置文件中加入<aop:aspectj-autoproxy proxy-target-class="true"/>

JDK动态代理和CGLIB字节码生成的区别？
 （1）JDK动态代理只能对实现了接口的类生成代理，而不能针对类
 （2）CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法
   因为是继承，所以该类或方法最好不要声明成final 

二、两者对比：

JDK动态代理是面向接口，在创建代理实现类时比CGLib要快，创建代理速度快。

CGLib动态代理是通过字节码底层继承要代理类来实现（如果被代理类被final关键字所修饰，那么抱歉会失败），在创建代理这一块没有JDK动态代理快，但是运行速度比JDK动态代理要快。
```
[JDK动态代理和CGLib动态代理](https://www.cnblogs.com/wangjing666/p/9660275.html)
[实现代码](https://blog.csdn.net/doujinlong1/article/details/80680149)

2. Spring AOP的实现原理
在appication.xml 文件中怎么配置aop
```
<!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!--以注解方式配置事务
    开启CGLib动态代理-->
    <tx:annotation-driven transaction-manager="transactionManager" proxy-target-class="true"></tx:annotation-driven>
<!--     <aop:aspectj-autoproxy proxy-target-class="true"/>-->
    <!--以XML方式配置事务-->
    <!--<tx:advice id="transactionAdvice" transaction-manager="transactionManager">-->
        <!--<tx:attributes>-->
            <!--<tx:method name="*" propagation="REQUIRED"/>-->
        <!--</tx:attributes>-->
    <!--</tx:advice>-->
    <!--<aop:config>-->
        <!--<aop:pointcut id="transactionPointcut" expression="execution(* com.wicresoft.serviceImpl.EmployeeServiceImpl.*(..))" />-->
        <!--<aop:advisor advice-ref="transactionAdvice" pointcut-ref="transactionPointcut"></aop:advisor>-->
    <!--</aop:config>-->



    <!--配置login validation aop-->
    <bean id="validateBean" class="com.wicresoft.aop.LoginValidate"></bean>

    <aop:config>
        <aop:pointcut id="validatePointcut" expression="execution(* com.wicresoft.serviceImpl.EmployeeServiceImpl.*(..))" />

        <aop:aspect id="myAspect" ref="validateBean">
            <aop:before method="Before" pointcut-ref="validatePointcut"></aop:before>
        </aop:aspect>
    </aop:config>
```
[Spring AOP简介](https://www.jianshu.com/p/5b9a0d77f95f)

3. SpringBoot配置文件的导入
 - 导入.properties配置文件
  ```
  PropertySource(value = {"classpath:config.properties"})
  ```
  不能加载.yml配置文件
 - 导入 spring的配置文件
  ```
  @ImportResource(locations="{classpath:spring.xml}")
  ```
