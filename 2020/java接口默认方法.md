[//title]:(java接口静态方法)
[//englishTitle]:(java-static-methods)
[//category]:(java)
[//tags]:(java)
[//createTime]:(20200223)
[//lastUpdateTime]:(20200223)

## 概述
Java8带来了一些全新的特性，包括lambda表达式、函数接口、方法引用、流、可选方法、接口中的静态方法和[默认方法](https://liushiming.cn/2020/02/23/java-default-methods/)。

在本文中，我们将深入讨论为什么java8接口新增了默认方法，如何使用默认方法，并讨论一些有用的用例。

## 静态方法
接口中的静态方法和类中定义的静态方法一样，不属于特定对象，所以它们不是实现接口的api的一部分，必须使用InterfaceName.staticMethod来调用它们。

为了理解静态方法如何在接口中工作，让我们看一个实例:
``` java
interface NewInterface { 
  
    // 静态方法
    static void hello() 
    { 
        System.out.println("Hello, New Static Method Here"); 
    } 
  
    // 抽象方法 
    void overrideMethod(String str); 
} 
  
// 实现类
public class InterfaceDemo implements NewInterface { 
  
    public static void main(String[] args) 
    { 
        InterfaceDemo interfaceDemo = new InterfaceDemo(); 
  
        // 调用接口静态方法 
        NewInterface.hello(); 
  
        // 调用被覆写后抽象方法 
        interfaceDemo.overrideMethod("Hello, Override Method here"); 
    } 
  
    // 实现接口方法
    @Override
    public void overrideMethod(String str) 
    { 
        System.out.println(str); 
   
```

## 为什么接口要支持静态方法
接口中的静态方法背后的思想是提供一种简单的机制，**允许通过将相关的方法内聚在接口中，而不必创建新的对象**。  

**抽象类也可以做同样的事情**。主要的区别在于**抽象类可以有构造函数、成员变量和方法。**

推荐把和只和接口相关的静态utility方法放在接口中（提高内聚性），而不需要额外创建一些utility类专门去放置这些方法。

## 参考资料
Baeldung - [Static and Default Methods in Interfaces in Java](https://www.baeldung.com/java-static-default-methods)
geeksforgeeks - [Static method in Interface in Java](https://www.geeksforgeeks.org/static-method-in-interface-in-java/)