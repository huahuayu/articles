[//title]:(java设计模式-单例模式)
[//englishTitle]:(java-design-pattern-singleton)
[//category]:(java,design-pattern,tutorial)
[//tags]:(java,singleton)
[//createTime]:(20200301)
[//updateTime]:(20200301)

## 概述
单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。    

**特点：**
1. 单例类只能有一个实例。
2. 单例类必须自己创建自己的唯一实例。
3. 单例类必须给所有其他对象提供这一实例。

## 线程不安全单例
### 饿汉模式
**优点：**  
- 实现简单 
- 线程安全    

**缺点：**
- 可能造成资源浪费，即使不使用也会占用内存，特别是实例比较大的时候  

**适用场景：**
只有在初始化类的成本较低或程序总是需要类的实例时才使用  

``` java
    public final class EagerSingleton {
        
        private static EagerSingleton singObj = new EagerSingleton();
 
        private EagerSingleton() {
        }
 
        public static EagerSingleton getSingleInstance() {
            return singObj;
        }
    }
```

### 懒汉模式
**优点：**  
- 对象仅在需要时被创建，无内存和cpu的浪费      

**缺点：**
- 非线程安全，多个线程同时走到`if (null == singObj )`就会创建多个实例    

**适用场景：**
非多线程环境    

``` java
    public final class LazySingleton {
 
        private static LazySingleton singObj = null;
 
        private LazySingleton() {
        }
 
        public static LazySingleton getSingleInstance() {
            if (null == singObj ) {
                singObj = new LazySingleton();
            }
            return singObj;
        }
    }
```

## 线程安全单例
线程安全单例测试方法，新建多个线程同时实例化单例类，看hashCode是否一致：    
``` java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println(Singleton.getInstance().hashCode());
    }

    public static void main(String[] args) {

        MyThread[] mts = new MyThread[10];
        for (int i = 0; i < mts.length; i++) {
            mts[i] = new MyThread();
        }

        for (int j = 0; j < mts.length; j++) {
            mts[j].start();
        }
    }
}
```

### 同步方法
**优点：**  
- 对象仅在需要时被创建，无内存和cpu的浪费 
- 线程安全，因为给方法加了`Synchronized`同步锁    

**缺点：**
- 方法上加锁大幅限制了多线程的效率    

**适用场景：**
不建议使用  

``` java
public final class ThreadSafeSingleton  {
    private static ThreadSafeSingleton singObj = null;

    private ThreadSafeSingleton() {
    }

    public static synchronized ThreadSafeSingleton getInstance() {
        if (null == singObj) {
            singObj = new ThreadSafeSingleton();
        }
        return singObj;
    }
}
```

### 同步代码块、双检查
**优点：**  
- 对象仅在需要时被创建，无内存和cpu的浪费 
- 线程安全。因为给需要加锁的代码块加了`Synchronized`同步锁，且代码块中有双检查      

**缺点：**
- 代码略繁琐       

**适用场景：**
基本上都适用，是一种较优的单例模式实现    

``` java
class DoubleCheckedSingleton {
    private static DoubleCheckedSingleton singObj = null;

    private DoubleCheckedSingleton() {
    }

    public static DoubleCheckedSingleton getInstance() {
        if (null == singObj) {
            synchronized (DoubleCheckedSingleton.class) {
                if (null == singObj) {
                    singObj = new DoubleCheckedSingleton();
                }
            }
        }
        return singObj;
    }

}
```

### 内部类
**优点：**  
- 对象仅在需要时被创建，无内存和cpu的浪费 
- 线程安全  
- 代码简洁    

**缺点：**
- 静态内部类虽然保证了单例在多线程并发下的线程安全性，但是在遇到序列化对象时，默认的方式运行得到的结果是多例的。  



**适用场景：**
不涉及序列化与反序列化的场景   

``` java
    public class Singleton {
        private static class SingletonHolder {
            public final static Singleton instance = new Singleton();
        }
 
        public static Singleton getInstance() {
            return SingletonHolder.instance;
        }
    }
```

### 静态代码块
**优点：**  
- 静态代码块在使用类的时候被执行一次 ，且仅执行一次，实现了延迟实例化  
- 线程安全  

**缺点：**
- 静态内部类虽然保证了单例在多线程并发下的线程安全性，但是在遇到序列化对象时，默认的方式运行得到的结果是多例的。     

**适用场景：**
推荐 

``` java
public class MySingleton{
	 
	private static MySingleton instance = null;
	 
	private MySingleton(){}
 
	static{
		instance = new MySingleton();
	}
	
	public static MySingleton getInstance() { 
		return instance;
	} 
}
```

## 序列化与反序列化单例问题
在不涉及序列化与反序列化的场景中，以上线程安全的单例类实现都没有问题，但是在反序列化时，默认得到的结果是多例的  

单例实现：  
``` java
    public class Singleton {
        private static class SingletonHolder {
            public final static Singleton instance = new Singleton();
        }
 
        public static Singleton getInstance() {
            return SingletonHolder.instance;
        }
    }
```

测试代码：  
``` java
public class SingletonTest {

    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();

        File file = new File("MySingleton.txt");

        try {
            FileOutputStream fos = new FileOutputStream(file);
            ObjectOutputStream oos = new ObjectOutputStream(fos);
            oos.writeObject(singleton);
            fos.close();
            oos.close();
            System.out.println(singleton.hashCode());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            FileInputStream fis = new FileInputStream(file);
            ObjectInputStream ois = new ObjectInputStream(fis);
            Singleton rSingleton = (Singleton) ois.readObject();
            fis.close();
            ois.close();
            System.out.println(rSingleton.hashCode());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }

    }
}
```

结果：  
``` text
1023892928
193064360
```

从结果中我们发现，序列号对象的hashCode和反序列化后得到的对象的hashCode值不一样，说明反序列化后返回的对象是重新实例化的，单例被破坏了。那怎么来解决这一问题呢？  

解决办法就是在反序列化的过程中使用readResolve()方法，单例实现的代码如下：  



``` java
class Singleton implements Serializable {
    private static final long serialVersionUID = 1L;

    private static class SingletonHolder {
        public final static Singleton instance = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.instance;
    }

    protected Object readResolve() throws ObjectStreamException {
        System.out.println("调用了readResolve方法！");
        return getInstance();
    }
}
```

结果：  
``` text
1023892928
调用了readResolve方法！
1023892928
```

对于Serializable和Externalizable类，readResolve方法允许类在将从流中读取的对象返回给调用者之前替换/解析它。通过实现readResolve方法，一个类可以直接控制被反序列化的自身实例的类型和实例。

## 参考资料
[高并发下线程安全的单例模式](https://blog.csdn.net/cselmu9/article/details/51366946) 
[jdk文档-The readResolve Method](https://www.math.uni-hamburg.de/doc/java/jdk1.4.1/docs/guide/serialization/spec/input.doc7.html)
[菜鸟教程-单例模式](https://www.runoob.com/design-pattern/singleton-pattern.html)