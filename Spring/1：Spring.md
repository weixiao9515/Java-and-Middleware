### Spring ：其实就是一个容器，由容器来负责控制对象的生命周期和对象间的关系

- 独立于各种应用服务器，基于Spring框架的应用，可以真正实现Write Once，Run Anywhere
- Spring的IoC容器降低了业务对象替换的复杂性，提高了组件之间的**解耦**
- Spring的AOP支持允许将一些通用任务如**安全、事务、日志**等进行集中式管理，从而提供了更好的复用
- Spring的ORM和DAO提供了与第三方持久层框架的良好整合，并简化了底层的数据库访问

![](https://github.com/likang315/Java-and-Middleware/blob/master/Spring/Spring/spring-module.png?raw=true)

### 1：Spring Module

###### 	大约20个模块功能组成，核心模块为：

​		数据访问(持久层)，Web，AOP（面向切面编程），核心容器，消息传递和测试

###### 	核心容器需导入的Jar：

- spring-core
- spring-beans
- spring-context
- spring-context-support
- spring-expression

### 2：IoC（Inversion of Control）：控制反转

​		应用本身不负责依赖对象的创建及维护，依赖对象的创建及维护是由外部容器负责的，控制权就由应用转移到了外部容器，控制权的转移就是所谓控制反转

###### 原理：

​	Spring 就是通过反射来实现注入的

###### 作用：

​	因为当我们的需求出现变动时，工厂模式会需要进行相应的变化，就要就修改代码，而IoC是通过反射机制来实现的，它的对象都是动态生成的，允许我们不重新编译代码，其实就是**解耦，并且不用频繁的创建和销毁对象**，消耗资源

###### DI（Dependency Injection）：依赖注入

​	在运行期，由外部容器动态地将依赖对象注入到组件中，就是在运行时能对Bean对象修改属性值，依赖注入是一种优秀的解耦方式，其可以让Bean以配置文件组织在一起，而不是以硬编码的方式耦合在一起。

### 3：AOP (Aspect Oriented Programming) ：面向切面编程

​	在不修改源代码的情况下给方法的进出口，动态的统一添加功能（日志，安全）的一种技术

###### 原理：

​	通过动态代理 和 cglib代理实现的

### 4：Spring 配置的三种方式

######    1：用 XML 配置容器

```xml
<!-- 实例化此类，放入到容器中-->
<bean id="fe" class="com.xupt.FlashEmail"/>
<bean id="cp" class="com.xupt.CellPhone">
    <property name="name" value="lisi"/>
    <property name="email" ref="fe"/>
</bean>
```

######    2：通过注解（Annotation）自动装配

​	 当容器扫描到 @Autowired等注入时，就会在 IOC 容器自动查找需要的 bean，并装配给该对象的属性

```xml
<!--启用Annotation,扫描类上的注解，实例化对象，放入到容器中管理-->
<context:annotation-config />  
<!--扫描基包下所有的类的Annotation-->
<context:component-scan base-package="com.xupt" />
```

######    3：用 Java 显式配置

- 用一个配置类(MyConfig.java)来初始化容器并配置
  - @Configuration：表明此类是配置类，用于初始化Spring容器
  - @ComponentScan(basePackageClasses=MyConfig.class)：定义扫描基包

```java
@Configuration
@ComponentScan(basePackageClasses=MyConfig.class)
public class MyConfig {
  @Bean
  public Student newcellphone() {
    return new Student("lisi",23);
  }
}
```

### 5：Spring 两种容器

##### 1：Spring 的 BeanFactory 容器

​	最简单的容器，只提供了依赖注入 （DI） 功能，这个容器接口在 org.springframework.beans.factory.BeanFactor 中被定义，最常被使用的是BeanFactory接口的实现类： **XmlBeanFactory** 类

```java
public static void main(String[] args) {
  XmlBeanFactory factory = new XmlBeanFactory (new ClassPathResource("Beans.xml"));
  HelloWorld obj = (HelloWorld) factory.getBean("helloWorld");
  obj.getMessage();
}
```

##### 2：ApplicationContext 容器，是Spring最常用的应用上下文容器接口，该接口有如下两个实现类：

- ClassPathXmlApplicationContext: 从类加载路径下搜索xml配置文件，并根据配置文件来初始化Spring容器
  - 类路径：
    - **WEB-INF/classes** 存放src目录java文件编译之后的class文件，xml、properties等资源配置文件，是一个定位资源的入口，默认的classpath
    - maven构建项目时候 **resource**目录，默认的classpath 
- FileSystemXmlApplicationContext: 从文件系统的相对路径或绝对路径下去搜索配置文件，并根据配置文件来初始化Spring容器

```java
public static void main(String[] args) {
		ApplicationContext context =
				new ClassPathXmlApplicationContext(new String[] {"ApplicationContext.xml"});
		CellPhone cp=(CellPhone)context.getBean("cp");
		cp.run();
}
```

