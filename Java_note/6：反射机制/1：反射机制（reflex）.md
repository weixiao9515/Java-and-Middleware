
RTTI (Run-Time Type Identification)：运行时类型识别，RTTI 能在运行时就能够自动识别每个编译时已知的类型

### 1：JAVA 反射机制

在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法，对于任意一个对象，都能够调用它的任意方法和属性,这种动态获取类的信息和动态调用对象方法的功能称为 java 语言的反射机制


想获取一个类这些信息，就必须先获取到该类的字节码文件对象，即：Class对象

### 2：获取 Class 对象的方式 (3种) ：

Class 类没有公共构造方法，Class 对象是在加载类时,由 Java 虚拟机通过调用 类加载器中的 defineClass() 自动构造的

1. 通过类的静态属性,JVM 将使用类装载器,将类装入内存,不做类的初始化工作，返回 Class 的对象

   ​	类.class

2. 通过 Object 方法中,对类进行初始化工作

   ​	对象名.getClass( ) ;

3. 通过Class 类中 forName (String className) ,装入类,并做类的静态初始化，返回 Class 的对象 
   	Class.forName( 完全限定类名 )；

### 3：java.lang    Class<T>  class 

​    Class 对象表示正在运行的 Java 的类或者接口,一个类表示一个 class 对象
​    枚举，数组，void，数据类型，Filed，Method，都表示一个对象

```java
static Class<?> forName(String className) 
  返回与带有给定字符串名的类或接口相关联的 Class 对象
T newInstance() 
  创建此 Class 对象所表示的类的一个新实例	
```

###### getDeclaredXXX() ：获取的是类自身声明的所有方法，包含public、protected和private方法

- Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes) 

  ​	返回一个 Constructor 对象，该对象反映此 Class 对象所表示的类或接口的指定构造方法

- Constructor<?>[ ] getDeclaredConstructors( ) 
       返回 Constructor 对象的一个数组，这些对象反映此 Class 对象表示的类声明的所有构造方法 

- Field  getDeclaredField(String name) 
        	返回一个 Field 对象，该对象反映此 Class 对象所表示的类或接口的指定已声明字段 
- Field[ ] getDeclaredFields() 
        	返回 Field 对象的一个数组，这些对象反映此 Class 对象所表示的类或接口所声明的所有字段 

- Method getDeclaredMethod(String name, Class<?>... parameterTypes) 
            	返回一个 Method 对象，该对象反映此 Class 对象所表示的类或接口的指定已声明方法
- Method[ ] getDeclaredMethods() 
            	返回 Method 对象的一个数组，这些对象反映此 Class 对象表示的类或接口声明的所有方法，包括 公共、保护、默认（包）访问 和 私有方法，但不包括继承的方法 

###### getXXX( )：获取自身和继承的所有public 方法

- Field getField(String name) 
        	返回一个 Field 对象，它反映此 Class 对象所表示的类或接口的指定公共成员字段 
- Method getMethod(String name, Class<?>... parameterTypes) 
        	返回一个 Method 对象，它反映此 Class 对象所表示的类或接口的指定公共成员方法


​	

### 4：java.lang.reflect     class   AccessibleObject

是 Field、Method 和 Constructor 对象的基类，在反射对象中设置 accessible,**可以将反射的对象标记为在使用时
取消默认 Java 语言访问控制检查的能力，跳过访问检查阶段** 

反射做修改时必须标记		

- void **setAccessible**(boolean flag) 

  ​	将此对象的 accessible 标志设置为指示的布尔值，然后设置可访问性(true)

- java.lang.reflect.Constructor<T>
  	提供关于类的单个构造方法的信息以及对它的访问权

### 5：Java.lang.reflect    Class  Field 

​	 Field 类的对象描述了当前类中的所有属性信息，一个Field 类的对象就是该类中一个属性的信息

  方法：	
	 Object get(Object obj) 
          	返回指定对象上，此 Field 表示的字段的值
	 String getName( ) 
         	 返回此 Field 对象表示的字段的名称 
	 Class<?> getType() 
          	返回一个 Class 对象，它标识了此 Field 对象所表示字段的声明类型
	 void set(Object obj, Object value) 
          	将指定对象变量上此 Field 对象表示的字段设置为指定的新值
	 

### 6：Java.lang.reflect    Class  Method  

​	Method 类对象描述了当前类的方法信息，Method类对象就是该类一个方法的信息
​		
  方法：
​	Object invoke(Object obj, Object... args) 
  	   对带有指定参数的指定对象调用由此 Method 对象表示的底层方法
​		obj -  调用底层方法的对象
​		args - 用于方法调用的参数 ,可变长参数

###  7：反射的应用

1. Spring IOC 的实现
2. 通过反射越过泛型检查
3. 动态的调用方法，或者修改其属性值
   	使用 getDeclaredMethod（）获取方法, invoke（）调用

### 8：反射的作用

1. 动态地创建类的实例，将类绑定到现有的对象中
2. 应用程序需要在运行时从某个特定的程序集中载入一个特定的类





