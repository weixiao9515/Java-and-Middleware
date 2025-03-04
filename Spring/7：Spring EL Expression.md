### Spring EL 表达式：(Spring Expression Language) ：即Spring3中功能丰富强大的表达式语言，简称 SpEL

SpEL 是类似于 OGNL 和 JSF EL 的表达式语言，能够在运行时构建复杂表达式，存取对象属性、对象方法调用等

所有的 SpEL 都支持XML 和 Annotation 两种方式，格式：#{ SpEL expression }

##### 1：下载Spring EL 的依赖包

首先在 Maven 的 pom.xml 中加入依赖包，这样会自动下载 SpEL 的依赖包

```
xml文件中配置：
<bean id="itemBean" class="com.lei.demo.el.Item">
	<property name="name" value="itemA" />
	<property name="total" value="10" />
</bean>
<bean id="customerBean" class="com.lei.demo.el.Customer">
	<property name="item" value="#{itemBean}" />
	<property name="itemName" value="#{itemBean.name}" />
</bean>
```

###### Annotation：

要在 Annotation 中使用 SpEL，必须要通过 annotation 注册组件 如果你在 xml 中注册了 bean 和在 java class 中定义了@Value，@Value 在运行时将失败

```
@Value("#{itemBean}")
private Item item;
@Value("#{itemBean.name}")
private String itemName;
```

##### 2：SpEL 方法调用：SpEL 允许开发者用 El 运行方法函数，并且允许将方法返回值注入到属性中

```
   Annotation
	1：@Value("#{'string'.toUpperCase()}")   	字符串直接调用函数
	2：@Value("#{priceBean.getSpecialPrice()}") 	实例对象调用
   xml
	<bean id="customerBean" class="com.leidemo.el.Customer">
		<property name="name" value="#{'lei'.toUpperCase()}" />
		<property name="amount" value="#{priceBean.getSpecialPrice()}" />
	</bean>
```

##### 3：SpEL 操作符(Spring EL Operators)

Spring EL 支持大多数的数学操作符、逻辑操作符、关系操作符。 1.关系操作符 包括：等于 (==, eq)，不等于 (!=, ne)，小于 (<, lt),，小于等于(<= ,le)，大于(>, gt)，大于等于 (>=, ge) 2.逻辑操作符 包括：and，or，and not(!) 3.数学操作符 包括：加 (+)，减 (-)，乘 (*)，除 (/)，取模 (%)，幂指数 (^)

```
   Annotation
	@Value("#{2 ^ 2}")
   xml
	<property name="testNotEqual" value="#{1 != 1}" />
```

##### 4：Spring EL 三目操作符 condition?true:false

? Annotation ?	@Value("#{itemBean.qtyOnHand < 100 ? true : false}") xml

?

##### 5：Spring EL 操作 List、Map 集合取值

Annotation //get map where key = 'MapA' @Value("#{testBean.map['MapA']}") private String mapA;

//get first value from list, list is 0-based. @Value("#{testBean.list[0]}") private String list;

xml