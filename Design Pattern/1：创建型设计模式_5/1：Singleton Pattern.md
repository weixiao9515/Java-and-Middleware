### 单例模式：（懒汉，饿汉各 3 种）

​	保证一个类的只可创建一个对象

- 构造方法私有（类外不可创建对象，不可被继承）
- 类的 static 方法

##### 1：懒汉式，方法被调用时，对象才被初始化，也叫对象的延时加载

```java
//懒汉式，对象的延时加载，线程安全，同步代码块，双重检查 Double-Check,效率高（双检锁）
public class Singleton {
    private volatile static Singleton instance;
    public static Singleton getInstance() { 
        if (instance == null) {
            //只允许一个类的实例进入
            synchronized(Singleton.class { 
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

//并发情况下，如果没有volatile关键字，在 instance = new TestInstance
//(会出现出现问题，可以分解为3行伪代码)
	 memory = allocate()   //分配内存
   ctorInstanc(memory)   //初始化对象
   instance = memory  //设置 instance 指向刚分配的地址

//线程安全，同步方法，效率太低
public class Singleton {
      private static Singleton singleton;
      public static synchronized Singleton getInstance() {
          if (singleton == null) {
              singleton = new Singleton();
          }
          return singleton;
      }
}
                         
//静态内部类，通过类加载机制，懒加载，调用方法才会装载，效率高
public class Singleton {
  private static class SingletonInstance {
      private static final Singleton INSTANCE = new Singleton();
  }
  public static Singleton getInstance() {
      return SingletonInstance.INSTANCE;
  }
}
```

##### 2：饿汉式，直接创建对象

```java
public class Singleton {
    private static Singleton instance = new Singleton();
    public static Singleton getInstance() {
        return instance;
    }
}

//通过静态块，类初始化的时候就加载
public class Singleton {
      private static Singleton instance;
      static {
          instance = new Singleton();
      }
      public static Singleton getInstance() {
          return instance;
      }
}

//枚举实现单例模式，不仅能避免反射问题，而且还自动支持序列化机制
public enum  EnumSingleton {
  	// private static final INSTANCE ；
    INSTANCE;
    public EnumSingleton getInstance(){
        return INSTANCE;
    }
}
```

##### 3：枚举实现单例

```Java
public final class T extends Enum{

}
```

- enum 有且仅有 private 的构造方法，防止外部的额外构造，这恰好和单例模式吻合
- 反射不能创建枚举类型，如果时枚举类型直接抛出异常
- 对于序列化和反序列化，因为每一个枚举类型和枚举变量在JVM中都是唯一的，即Java在序列化和反序列化枚举时做了特殊的规定，枚举的writeObject、readObject、readObjectNoData、writeReplace和readResolve等方法是被编译器禁用的，因此也不存在实现序列化接口后调用 readObject 会破坏单例的问题