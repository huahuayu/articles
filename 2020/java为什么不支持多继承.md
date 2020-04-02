[//title]:(java为什么不支持多继承)
[//englishTitle]:(why-doesnt-java-support-multiple-inheritance)
[//category]:(java)
[//tags]:(java)
[//createTime]:(20200223)
[//updateTime]:(20200402)
## 概述
多重继承是面向对象概念的一个特性，其中一个类可以继承多个父类的属性。当超类和子类中都存在具有相同签名的方法时，就会出现问题。在调用该方法时，编译器无法确定要调用哪个类方法，甚至在调用哪个类方法时也无法确定优先级。

java不支持类的多继承，但是可以通过接口或内部类来曲线实现多继承。

## 为什么不支持多继承
请看下面的代码，它将编译报错：  
``` java
public class MultiInheritTest {
    @Test
    public void SonTest() {
        Son son = new Son();
        /**
         * 编译报错：
         * 模棱两可的方法调用。
         * son对象不知道是调用Parent1的fun()还是Parent2的fun()。
         */
        son.fun();
    }
}

class Parent1 {
    public void fun() {
        System.out.println("parent1");
    }
}

class Parent2 {
    public void fun() {
        System.out.println("parent2");
    }
}

// 编译报错: 类不支持多继承
class Son extends Parent1, Parent2 {

}
```

输出：  
``` text
compile error
```

以上例子可以看出，当多个父类有同样的方法签名时，子类将不知道调用哪一个父类的方法。  

## 钻石问题
``` text
          GrandParent
           /     \
          /       \
      Parent1   Parent2
          \       /
           \     /
             Son
```

``` java
// 祖父类
class GrandParent 
{ 
    void fun() 
    { 
        System.out.println("Grandparent"); 
    } 
} 
  
// 父类1
class Parent1 extends GrandParent 
{ 
    void fun() 
    { 
        System.out.println("Parent1"); 
    } 
} 
  
// 父类2 
class Parent2 extends GrandParent 
{ 
    void fun() 
    { 
        System.out.println("Parent2"); 
    } 
} 
  
  
// Error : 不支持多继承 
class Test extends Parent1, Parent2 
{ 
   public static void main(String args[]) 
   { 
       Test t = new Test(); 
       t.fun(); 
   } 
}
```

为了避免这种复杂性，java不支持多继承  

## 简化复杂性
多继承带来很多复杂性，容易在各种操作过程中产生问题，比如强制类型转换、构造函数链接等，而最重要的原因是，实际上需要多继承的场景非常少，所以为了保持事情的简单和直观，干脆不支持多继承。

## 参考资料
geeksforgeeks - [Java and Multiple Inheritance](https://www.geeksforgeeks.org/java-and-multiple-inheritance/)