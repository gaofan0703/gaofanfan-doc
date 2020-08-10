# 一、单例模式

### 1、双重检测

```java
public class Singleton {
    //私有构造函数
    private Singleton() {}  
    private volatile static Singleton instance = null;   
    //静态工厂方法
    public static Singleton getInstance() {
      //双重检测机制
      if (instance == null) {     
         //加同步锁
         synchronized (Singleton.class){ 
           //双重检测机制
           if (instance == null) {     
                  instance = new Singleton();
                }
             }
          }
          return instance;
      }
}
```

**为什么要用volatile修饰instance？**

> **volatile** 作用是保证内存可见性及防止指令重排。(但不能保证原子性)
>
> **instance = new Singleton()**  在Java指令中创建对象和赋值操作是分开进行的，JVM指令为：
>
> + 1、分配内存空间                                          memory =allocate(); 
> + 2、初始化对象                                              ctorInstance(memory);
> + 3、设置instance指向刚分配的内存地址    instance =memory;
>
> 实际执行的时候，JVM指令重排，最终顺序为 1、3 、2
>
> 例：A线程执行instance = new Singleton()代码，执行完1、3两步，instance对象还没初始化，但是已经不再指向null，这时候B线程执行到第8行代码 if (instance == null) ，结果会是false，返回一个未初始化完成的instance 。



### 2、静态内部类

```java
public class Singleton {
    //静态内部类
    private static class LazyInstanceHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    private Singleton (){}
    public static Singleton getInstance() {
        return LazyInstanceHolder.INSTANCE;
    }
}

```

> 静态内部类这种方式是利用**classloader的加载机制**来实现懒加载，并保证构建单例的线程安全。



### 3、枚举

``` java
public enum SingletonEnum {
    INSTANCE;
}
```

 **[为什么枚举是创建单例最近方案?](https://juejin.im/post/5e3d81ebe51d4526de39197b)**

> 枚举类型，JVM会阻止反射攻击获取私有的构造方法。
>
> 同时枚举也防止序列化攻击。



### 总结

|  单例模式  | 是否懒加载 | 是否防反射 |
| :--------: | :--------: | :--------: |
|  双重检测  |     是     |     否     |
| 静态内部类 |     是     |     否     |
|    枚举    |     否     |     是     |



![test for markdown](https://gaofan0703.github.io/gaofanfan-doc/image/1.png)



